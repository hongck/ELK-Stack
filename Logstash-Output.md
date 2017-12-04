### 목록

* Output Plugin
    * [stdout](#stdout)
    * [elasticsearch](#es)
    * [slack](#slack)

<a name='stdout'></a>
#### Stdout

[[ images/logstash/output/stdout.gif | height = 500px | width = 1024px]]

* 기능
    * 수집한 (혹은 전처리까지 한) 데이터를 Shell에 출력한다
    * production level에서 사용하기보단 debug 목적으로 사용한다
* 명령어
    * config 파일 생성 : `$ vim stdout.conf`
    * config 파일 편집
    ```
    input {
      stdin {}
    }

    output {
      stdout {
        codec => rubydebug
      }
    }
    ```
* 주요 Configuration Options
    * codec
        * 출력 format 설정
        * `plain`, `rubydebug`, `json` 등 설정 가능

<a name='es'></a>
#### Elasticsearch

[[ images/logstash/output/es_basic.gif | height = 500px | width = 1024px]]

* 기능
    * Elastic Stack을 정석으로 활용할 경우 가장 많이 사용하는 Output Plugin이다
    * Logstash에서 수집하고 전처리한 데이터를 Elasticsearch로 전송하는 작업이다
* AWS EC2 Instance 인바운드 설정
    * [[ images/logstash/output/inbound.gif | height = 500px | width = 1024px]]
* 명령어
    * config 파일 생성 : `$ vim es_output.conf`
    * config 파일 편집
    ```
    input {
      jdbc  {
        jdbc_connection_string => "jdbc:mysql://52.78.61.155:3306/fc"
        jdbc_validate_connection => true
        jdbc_user => "fc"
        jdbc_password => "fc"
        jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
        jdbc_driver_class => "com.mysql.jdbc.Driver"
        statement => "SELECT * FROM fc"
      }
    }

    output {
      elasticsearch {
        hosts => ["13.125.35.175:9200"]
        index => "fc1"
        document_type => "fc1"
      }
      stdout {
        codec => rubydebug
      }
    }
    ```
* 주요 Configuration Options
    * hosts : 데이터를 저장할 Elasticsearch의 Host
    * index : Elasticsearch Host의 어떤 Index에 저장할지 설정 (없으면 생성)
    * document_type : Index의 어떤 Type에 저장할지 설정 (없으면 생성)
    * document_id : Document ID를 직접 지정 

<a name='slack'></a>
#### Slack (커뮤니티)

[[ images/logstash/output/slack-event.gif | height = 500px | width = 1024px]]

* 기능
    * 모든 event (혹은 특정 event) 발생시 Slack으로 실시간 알림을 보낼 수 있다.
    * 다만, 커뮤니티 버전이라는 것을 염두하고 사용하자.

* Slack 설정 (Incoming Webhook 설정)

    * [[ images/logstash/output/slack-setting.gif | height = 500px | width = 1024px]]
    * 본인이 admin인 slack team으로 로그인
    * apps에서 Incoming-Webhooks 검색 [클릭]
    * add configuration 선택
    * post to channel에서 Webhook 보낼 채널 선택
    * Add integration Webhooks integration
    * Webhook URL 복사 후 Save Settings 선택

* 명령어
    * 설치
        * git 설치 : `$ sudo yum install git`
        * Logstash Home Directory 이동 : `$ cd ~/fc/logstash-5.6.4/`
        * Logstash Output Slack 다운로드 :`$ git clone https://github.com/cyli/logstash-output-slack.git`
        * Logstash Output Slack 설치 : `$ bin/logstash-plugin install logstash-output-slack`
    * config 생성 : `$ vim slack.conf`
    * config 편집 [참고](https://discuss.elastic.co/t/output-if-else-configuration-error/39869)
    ```
    input {
      jdbc  {
        jdbc_validate_connection => true
        jdbc_connection_string => "jdbc:mysql://52.78.61.155:3306/fc"
        jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
        jdbc_driver_class => "com.mysql.jdbc.Driver"
        jdbc_user => "fc"
        jdbc_password => "fc"
        statement => "SELECT * FROM fc"
      }
    }

    output {
      if [salary] > 10000 {
        slack {
          url => ["{Slack Webhook URL}"]
          format => "축하드립니다! %{location} 국적의 %{age}살님의 연봉이 1억원을 돌파했습니다."
        }
      }
      stdout {
        codec => rubydebug
      }
    }
    ```
    * Logstash 실행 : `$ bin/logstash -f slack.conf`
### 목록

* [개요](#logstash-outline)
* Input Plugin
    * [stdin](#logstash-stdin)
    * file
        * [csv](#logstash-csv)
        * [log](#logstash-log)
    * [jdbc - database](#logstash-db)
    * [elasticsearch](#logstash-es)

---

<a name='logstash-outline'></a>
### 개요

* Logstash config는 (이하: `logstash.conf`)을 Logstash Home Directory(예: `~/fc/logstash-5.6.4/`)에 저장한다
* Logstash를 실행할 때 Logsatsh Home Directory 아래 `bin/logstash`를 사용하기 때문이다.
* `logstash.conf`은 크게 `input`, `filter`, `output` 부분으로 나눠진다
* `input`과 `output`은 필수인 반면, `filter`는 필요할 경우 사용하면 된다
* 아직 `output`은 다루지 않았기에 가장 기본적인 stdout을 사용한다

---

### Input

* Logstash로 어떤 데이터를 수집할지 설정하는 부분이다.
* Elastic이 공식적으로 지원하는 plugin은 [여기](https://www.elastic.co/guide/en/logstash/6.0/input-plugins.html)에서 확인 가능하다
* 이 외에도 커뮤니티에서 제작한 plugin(예: mongodb input)을 사용해서 수집할 수도 있다
* 기본적으로 아래 모든 작업은 Logstash Home Directory에서 진행한다 (`/home/ec2-user/fc/logstash-5.6.4/`)
* 시작에 앞서 실습에 사용할 데이터를 다운 받자
   * 데이터 다운 받을 디렉토리 생성 : `$ mkdir /home/ec2-user/fc/logstash-5.6.4/sample`
   * 디렉토리 이동 : `$ cd /home/ec2-user/fc/logstash-5.6.4/sample`
   * 데이터 다운로드
       * `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/titanic.csv`
       * `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/titanic2.csv`
       * `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/titanic3.csv`
       * `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/apache.log`
       * `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/test.log`

<a name='logstash-stdin'></a>
#### stdin

[[ images/logstash/input/stdin.gif | height = 500px | width = 1024px]]

* 기능 : standard input (사용자가 콘솔에 직접 입력한 내용)을 읽어들인다
* 명령어
   * config 파일 생성 : `$ vim stdin.conf`
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
   * logstash 실행 : `$ bin/logstash -f stdin.conf`
   * 데이터 입력 : 콘솔에 아무 내용이나 입력하고 `Enter`를 누른다

<a name='logstash-csv'></a>

#### file - csv

[[ images/logstash/input/csv.gif | height = 500px | width = 1024px]]

* 기능 : file input plugin을 이용해서 csv 파일을 읽어들인다
* 명령어
   * config 파일 생성 : `$ vim csv.conf`
   * config 파일 편집
   ```
   input {
     file  {
       path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
     }
   }
   
   output {
     stdout {
       codec => rubydebug
     }
   }
   ```
   * logstash 실행 : `$ bin/logstash -f csv.conf`
<a name='file_conf'></a>
* 주요 Configuration Options (전체 Options은 [elastic](https://www.elastic.co/guide/en/logstash/6.0/plugins-inputs-file.html) 참조)
    * `path`
        * Logstash를 통해 수집하려는 데이터 위치
        * 절대경로를 입력해야 한다 (상대경로 x)
        * 복수개 경로를 입력할 때는 배열로 한다 : `path => [ "/var/log/171201.log", "/var/log/171202.log" ]`
        * wildcard도 사용 가능하다 : `path => "/var/log/*.log"`
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/*.csv"
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `exclude`
        * path에서 설정한 파일 중 제외할 파일 설정
        * 복수개 경로를 입력할 때는 배열로 한다 : `exclude => [ "/var/log/171201.log", "/var/log/171202.log" ]`
        * wildcard도 사용 가능하다 : `exclude => "/var/log/1712*.log"`
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/*.csv"
            exclude => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```

    * `sincedb_path`
        * logstash가 특정 파일의 어느 위치까지 조회했는지 sincedb에 기록한다
        * 그러므로 logstash를 중단하고 다시 시작해도 중단하기 전의 위치부터 조회하여 데이터를 누락하지 않는다
        * sincedb 파일은 Logstash Home directory 아래 data/plugins/inputs/file 아래 생긴다
            *  실습환경의 경우 : `/home/ec2-user/fc/logstash-5.6.4/data/plugins/inputs/file/`
        * 만약에 이미 조회한 데이터를 강제로 조회하고 싶으면 `sincedb_path => /dev/null`로 설정한다
            * [[ images/logstash/input/sincedb_path.png | height = 500px | width = 1024px]]

    * `start_position`
        * `beginning` 또는 `end` 중에 하나로 설정한다
        * 파일의 처음부터 조회할지 마지막부터 조회할지 설정하는 옵션으로 default는 streaming 목적으로 `end`다
        * 단, 이 옵션은 특정 파일을 logstash로 처음 읽을 때만 적용되고 그 후에는 sincedb에 저장된 부분부터 이어서 읽는다
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
            start_position => "beginning"
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `sincedb_write_interval`
        * logstash가 몇 초 주기로 sincedb에 파일을 어디까지 읽었는지 기록할지 설정한다
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
            sincedb_write_interval => 15
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `stat_interval`
        * 몇 초 주기로 현재 조회하고 있는 파일이 수정되었는지 확인할지 설정한다
        * 간격을 작게할수록 실시간으로 데이터 수집이 가능하나 시스템 부하는 커진다
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
            stat_interval => 15
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `ignore_older`
        * 이 옵션으로 설정한 시간(초 단위)보다 이전에 수정된 파일은 무시한다
        * 단, 처음에 무시되었지만 그 후에 파일이 새로 수정될 경우는 제대로 수집된다
        ```
        input {
          file  {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
            ignore_older => 5
            start_position => "beginning"
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```

<a name='logstash-log'></a>
#### file - log

[[ images/logstash/input/log.gif | height = 500px | width = 1024px]]

* 기능 : file input plugin을 이용해서 log 파일을 읽어들인다
* 명령어
   * 데이터 다운로드 : `$ wget https://raw.githubusercontent.com/higee/elastic/master/Week4_Logstash/data/apache.log -O /home/ec2-user/fc/logstash-5.6.4/sample/apache.log`
   * config 파일 생성 : `$ vim log.conf`
   * config 파일 편집
   ```
   input {
     file  {
       path => "/home/ec2-user/fc/logstash-5.6.4/sample/apache.log"
       start_position => "beginning"
     }
   }
   
   output {
     stdout {
       codec => rubydebug
     }
   }
   ```
   * logstash 실행 : `$ bin/logstash -f log.conf`
* 주요 Configuration Options : `csv`와 같은 file input plugin을 사용하므로 Option도 동일하다

<a name='logstash-db'></a>
#### jdbc - database

[[ images/logstash/input/db.gif | height = 500px | width = 1024px]]

* 기능
    * jdbc input plugin을 이용해서 database 데이터를 읽어들인다
    * 기본적으로 row 1개가 logstash에서 event1개로 인식된다
* 명령어
   * jdbc input 사용하기 위한 사전작업
       * 드라이버 다운받을 디렉토리 생성 : `$ mkdir /home/ec2-user/fc/logstash-5.6.4/driver`
       * 디렉토리 이동 : `$ cd driver`
       * jdbc driver 다운로드 :  `$ wget https://downloads.mysql.com/archives/get/file/mysql-connector-java-5.1.36.tar.gz`
       * 파일 압축 해제 : `$ tar -xzvf mysql-connecttor-java-5.1.36.tar.gz`
       * 기존 파일 삭제 : `$ rm *.gz`
   * Logstash Home Directory로 이동 : `$ cd ..`
   * config 파일 생성 : `$ vim log.conf`
   * config 파일 편집
   ```
   input {
     jdbc  {
       jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
       jdbc_validate_connection => true
       jdbc_user => "fc"
       jdbc_password => "fc"
       jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
       jdbc_driver_class => "com.mysql.jdbc.Driver"
       statement => "SELECT * FROM fc"
     }
   }
   
   output {
    stdout {
      codec => rubydebug
     }
   }
   ```
   * logstash 실행 : `$ bin/logstash -f db.conf`
* 주요 Configuration Options (전체는 [참고](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-jdbc.html#plugins-inputs-jdbc-jdbc_driver_library))
    * `jdbc_connection_string`
        * 사용하는 db 종류, db가 위치한 host, db 이름 등 명시해야 한다
        * 예를 들어, `jdbc:mysql://52.78.61.155:3306/fc`의 경우
            * db 종류 : mysql
            * db host : 52.78.61.155:3306 
            * db 이름 : fc
    * `jdbc_driver_class`
        * 사용하는 jdbc driver 명시
        * 예를 들어
            * [oracle](https://github.com/logstash-plugins/logstash-input-jdbc/issues/43) : `"oracle.jdbc.driver.OracleDriver"`
            * mysql : `"com.mysql.jdbc.Driver"`
    * `jdbc_driver_library`
        * 사용하는 jdbc driver 위치 설정
        * 일반적으로, local에 다운로드 받고 경로를 설정한다
    * `jdbc_user` & `jdbc_password` : db 서버에서 권한을 부여 받은 사용자 이름과 비밀번호
    * `jdbc_validate_connection` : jdbc로 제대로 연결되었는지 확인 
    * `schedule`
        * staetement (SQL query)를 언제 또는 얼마나 주기적으로 실행할지 설정
        * format은 [Cron format](http://www.nncron.ru/help/EN/working/cron-format.htm)이다
        * 예를 들어, 매일 새벽 0시에 statement를 실행하고 싶은 경우
        ```
        input {
          jdbc  {
            jdbc_validate_connection => true
            jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
            jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
            jdbc_driver_class => "com.mysql.jdbc.Driver"
            jdbc_user => "fc"
            jdbc_password => "fc"
            statement => "SELECT * FROM fc"
            schedule => "0 0 * * * * "
          }
        }
   
        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `statement` : db에서 어떤 row를 추출할지 query를 작성하여 선택
    * `use_column_value`
        * db의 column value를 통해 이미 조회한 값인지 판단할지 설정
        * `true` 또는 `false`
        ```
        input {
          jdbc  {
            jdbc_validate_connection => true
            jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
            jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
            jdbc_driver_class => "com.mysql.jdbc.Driver"
            jdbc_user => "fc"
            jdbc_password => "fc"
            use_column_value => true
            statement => "SELECT * FROM fc"
          }
        }
   
        output {
          stdout {
            codec => rubydebug
          }
        }
        ```

    * `tracking_column` 
        * 위의 `use_column_value`가 true일 경우, 어떤 column으로 track할 지 설정
        ```
        input {
          jdbc  {
            jdbc_validate_connection => true
            jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
            jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
            jdbc_driver_class => "com.mysql.jdbc.Driver"
            jdbc_user => "fc"
            jdbc_password => "fc"
            use_column_value => true
            tracking_column => "id"
            statement => "SELECT * FROM fc"
          }
        }
   
        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
    * `parameters`
        * statement에서 parameter 값을 받아서 사용 가능
        * `sql_last_value` ([참고](https://discuss.elastic.co/t/how-should-i-use-sql-last-value-in-logstash/64595/7))와 같이 built-in parameter도 있고
        ```
        input {
          jdbc  {
            jdbc_validate_connection => true
            jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
            jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
            jdbc_driver_class => "com.mysql.jdbc.Driver"
            jdbc_user => "fc"
            jdbc_password => "fc"
            statement => "SELECT * FROM fc WHERE id > :sql_last_value"
            use_column_value => true
            tracking_column => "id"
          }
        }
   
        output {
          stdout {
            codec => rubydebug
          }
        }
        ```
        * 직접 설정도 가능하다
        ```
        input {
          jdbc  {
            jdbc_validate_connection => true
            jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
            jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
            jdbc_driver_class => "com.mysql.jdbc.Driver"
            jdbc_user => "fc"
            jdbc_password => "fc"
            parameters => { "my_country" => "KR" }
            statement => "SELECT * FROM fc WHERE location = :my_country"
          }
        }
   
        output {
          stdout {
            codec => rubydebug
          }
        }
        ```

<a name='logstash-es'></a>
#### elasticsearch

[[ images/logstash/input/es.gif | height = 500px | width = 1024px]]

* 기능 : elasticsearchfile input plugin을 이용해서 elasticsearch에서 데이터를 읽어들인다
* 명령어
   * config 파일 생성 : `$ vim es.conf`
   * config 파일 편집
   ```
  input {
    elasticsearch {
      hosts => ["13.125.21.52:9200"]
      index => "test"
      query => '{ "query": { "match_all": {}}}'
    }
  }
   
  output {
    stdout {
       codec => rubydebug
    }
  }
   ```
  * logstash 실행 : `$ bin/logstash -f es.conf`
* 주요 Configuration Options (전체 Options은 [elastic](https://www.elastic.co/guide/en/logstash/6.0/plugins-inputs-elasticsearch.html#plugins-inputs-elasticsearch-options) 참고)
    * `hosts` : 데이터를 수집하려는 Elasticsearch Host 정보
    * `index` : 선택한 Elasticsearch Host의 어느 index에서 데이터를 수집할지 설정
    * `query` : 선택한 index에서 어떤 Documents를 수집할 지 query DSL을 통해 설정

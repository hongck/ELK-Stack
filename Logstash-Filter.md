### 목록

* [csv](#csv)
* [date](#date)
* [mutate](#mutate)
* [grok](#grok)
* [공통 옵션](#common)
    * add_field
    * remove_field
---

<a name='csv'></a>
#### csv

[[ images/logstash/filter/csv.gif | height = 500px | width = 1024px]]

* 기능
    * 특정 구분자를 이용해서 event message를 작은 단위로 쪼갠다
    * 문장 마지막이나 파일 마지막 줄에 공백이 포함되어 있지 않은지 확인해야 한다 (`_csvparsefailure` 원인)
* 명령어
    * config 생성 : `$ vim csv.conf`
    * config 편집
    ```
    input {
      file {
        path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
      }
    }

    filter {
      csv {
        columns => ["Index", "Name", "Survival", "Pclass", "Sex", "Age", "SibSp", "Parch", "Ticket", "Fare", "Embarked"]
        separator => ","
        skip_empty_columns => true
        convert => {
          "Pclass" => "integer"
          "Index" => "integer"
          "Survival" => "integer"
          "Age" => "integer"
          "Fare" => "integer"
        }
      }
    }

    output {
      stdout {
        codec => rubydebug
      }
      elasticsearch {
       hosts => ["13.125.35.175:9200"]
       index => "titanic"
       document_id => "%{Index}"
      }
    }
    ```
* 주요 Configuration options
    * `columns` : separator로 나눈 후 각 단위에게 부여할 Column 이름 지정
    * `convert` : 특정 Field의 Datatype 변환
    * `separator` : 하나의 row를 어떤 구분자로 쪼갤지 설정 (default는 ",")
    * `skip_empty_columns` : 빈 column이 나올 경우 넘어갈지 설정 (true/false)
    

<a name='date'></a>
#### date

[[ images/logstash/filter/date.gif | height = 500px | width = 1024px]]

* 기능 : 날짜 Field를 읽어들여서 event time으로 활용한다
* 명령어
    * config 파일 생성 : `$ vim date.conf`
    * config 파일 편집
    ```
    input {
      stdin{}
    }

    filter {
      date {
        match => ["message", "YYYY-MM-dd;HH:mm:ss.SSS"]
        target => "@timestamp"
      }
    }

    output {
      stdout {
        codec => "rubydebug"
      }
      elasticsearch {
        hosts => ["13.125.21.52:9200"]
        index => "date_filter"
      }
    }  
    ```
    * Logstash 실행 : `$ bin/logstash -f date.conf`
    * Standard Input 입력 : `$ 2014-08-01;11:00:22.123`
* 주요 Configuration Options
    * `match` : 날짜 Field를 mapping 하려는 패턴
    * `target` : 변경한 값을 저장할 Field 선택
    * `timezone` : timezone 설정, 한국의 경우 `Asia/Seoul`

<a name='mutate'></a>
#### mutate

[[ images/logstash/filter/mutate.gif | height = 500px | width = 1024px]]

* 기능 : merge, rename, replace, split, update 등 다양한 기능을 제공한다
* 명령어
    * config 파일 생성 : `$ vim mutate.conf`
    * conf 파일 편집
    ```
    input {
      stdin {}
    }

    filter {
      mutate {
        copy => { "message" => "copied message" }
        split => { "message" => "," }
        rename => { "host" => "my host" }
        merge => ["@version", "host"]
        replace => { "@version" => "It's Confidential!" }
      }
    }

    output {
      stdout {
        codec => rubydebug
      }
    }
    ```
* 주요 Configuration Options
    * copy : 특정 Field 값과 같은 값을 가지는 Field 생성
    * split : 특정 Field를 구분자를 이용해서 작은 단위로 나눈다.
    * update/replace : 특정 Field 값을 변경
    * rename : 특정 Field 이름 변경
    * merge : 여러 Field 의 값으로 하나의 배열 생성

<a name='grok'></a>
#### grok


* 기능
    * 구조화되지 않은 데이터 처리에 적합
    * 그 중에서도 패턴이 보이는 120 종류 [클릭](https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns)
* 명령어
    * 예시1)
        * [[ images/logstash/filter/grok.gif | height = 500px | width = 1024px]]
        * config 파일 생성 : `$ vim grok1.conf`
        * config 파일 편집
        ```
        input {
          file {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/apache.log"
          }
        }
        
        filter {
          grok {
            match => { "message" => "%{COMBINEDAPACHELOG}" }
          }
        }
        
        output {
          stdout {
            codec => rubydebug
          }
        }
        ``` 
    * 예시2
        * [[ images/logstash/filter/grok2.gif | height = 500px | width = 1024px]]
        * config 파일 생성 : `$ vim grok2.conf`
        * config 파일 편집
        ```
        input {
          file {
            path => "/home/ec2-user/fc/logstash-5.6.4/sample/test.log"
          }
        }

        filter {
          grok {
            match => {
              "message" => "%{TIMESTAMP_ISO8601:date}, %{USERNAME:file}, %{USERNAME:function}, %{INT:line}, %{LOGLEVEL:level}, %{GREEDYDATA:messsage}"
            }
          }
        }

        output {
          stdout {
            codec => rubydebug
          }
        }
        ```

<a name='common'></a>
#### common

[[ images/logstash/filter/common.gif | height = 500px | width = 1024px]]

* 기능
    * 대부분의 filter에서 사용가능한 옵션이다
    * 대표적으로 Field를 추가하는 `add_field`와 Field를 제거하는 `remove_field`가 있다
    * Logstash는 Log를 유의미한 단위로 변환한다는 면에서 꽤 자주 쓰이는 plugin이다.
* 명령어
    * config 파일 생성 : `$ vim common.conf`
    * config 파일 편집
    ```
    input {
      stdin {
      }
    }

    filter {
      mutate {
        split => {"message" => ","}
        add_field => {
          "first" => "%{message[0]}"
          "second" => "%{message[1]}"
        }
        remove_field => ["message", "host"]
      }
    }

    output {
      stdout {
        codec => rubydebug
      }
    }
    ```
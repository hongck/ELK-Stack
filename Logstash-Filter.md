### 목록

* [csv](#csv)
* [date](#date)
* [grok](#grok)
* [mutate](#mutate)
* [Common Options]
    * [add_field]
    * [remove_field]
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
    

* 기능
* 명령어
* 주요 Configuration Options









<a name='add'></a>
#### add

<a name='remove'></a>
#### remove

<a name='date'></a>
#### date

<a name='grok'></a>
#### grok

<a name='mutate'></a>
#### mutate
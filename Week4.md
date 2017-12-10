### 목록

* [문제1 : 국가별 평균 연봉](#ex1)
* [문제2 : employment_status 별 연봉](#ex2)
* [문제3 : 연봉이 10,000인 사람의 수](#ex3)
* [문제4 : 30세 이하의 평균 연봉](#ex4)
* [문제5 : 구매사이트가 옥션 또는 11번가인 고객](#ex5)
* [문제6 : 구매사이트가 옥션 또는 11번가인 고객들의 평균나이](#ex6)
* [문제7 : 판매자 평점이 2~4 사이인 고객의 평균 상품개수](#ex7)
* [문제8 : 사용자 입력을 받아서 콘솔에 출력해보자](#ex8)
* [문제9 : 이미 읽은 titanic.csv 파일을 강제로 다시 읽어서 콘솔에 출력해보자](#ex9)
* [문제10 : sql_last_value 활용](#ex10)
* [문제11 : sample.csv를 csf filter로 처리](#ex11)
* [문제12 : “Seoul”, “Tokyo”, “Beijing”를 mutate filter로 처리](#ex12)

<a name='ex1'></a>
#### 문제1
```
mysql(13.125.21.52:3306) database(fc) table(fc)에서 국가별 평균연봉을 콘솔에 출력해보자
```

#### 풀이1
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    statement => "
      SELECT 
        location, 
        AVG(salary)
      FROM fc 
      GROUP BY location
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='e2'></a>
#### 문제2
```
mysql(13.125.21.52:3306) database(fc) table(fc)에서 employment_status 별 평균연봉을 콘솔에 출력해보자
```

#### 풀이2
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    statement => "
      SELECT
        employment_status,
        AVG(salary)
      FROM fc
      GROUP BY employment_status
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex3'></a>
#### 문제3
```
mysql(13.125.21.52:3306) database(fc) table(fc)에서 연봉이 10k 이상인 사람의 수를 콘솔에 출력해보자
```

#### 풀이3
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    statement => "
      SELECT COUNT(id)
      FROM fc
      WHERE salary >= 10000
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex4'></a>
#### 문제4
```
mysql(13.125.21.52:3306) database(fc) table(fc)에서 30세 이하의 평균연봉을 콘솔에 출력해보자
```

#### 풀이4
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    statement => "
      SELECT AVG(salary)
      FROM fc
      WHERE age <= 30
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex5'></a>
#### 문제5
```
elasticsearch(13.125.21.52:9200) `shopping` index에서 구매사이트가 옥션 또는 11번가인 Document를 출력해보자
```

#### 풀이5
```
input {
  elasticsearch {
    hosts => ["13.125.21.52:9200"]
    index => "shopping"
    query => '{
      "query": {
        "terms": {
          "구매사이트" : ["11번가", "옥션"]
        }
      }
    }'
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex6'></a>
#### 문제6
```
elasticsearch(13.125.21.52:9200) `shopping` index에서 구매사이트가 옥션 또는 11번가인 고객의 평균나이를 출력해보자
```

#### 풀이6 

* ex6.sh
```
curl -XGET "http://13.125.21.52:9200/shopping/_search" -H 'Content-Type: application/json' -d'
{
  "size":0,
  "query": {
    "terms": {
      "구매사이트": [
        "11번가",
        "옥션"
      ]
    }
  },
  "aggs": {
    "avg_age": {
      "avg": {
        "field": "고객나이"
      }
    }
  }
}'
```
* ex6.sh 권한변경
```
$ chmod 777 ex6.sh
```

* ex6.conf
```
input {
  exec {
    command => "/home/ec2-user/fc/logstash-5.6.4/ex6.sh"
    interval => 500
  }
}

filter {
  grok {
    match => { "message" => "%{GREEDYDATA:took}valu%{GREEDYDATA:avg}}}}"}
  }
  mutate {
    split => {"avg" => ":"}
    add_field => {
      "to_be_removed" => "%{avg[0]}"
      "answer" => "%{avg[1]}"
    }
    remove_field => ["took", "message", "command", "to_be_removed", "avg"]
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex7'></a>
#### 문제7
```
elasticsearch(13.125.21.52:9200) `shopping` index에서 판매자 평점이 2~4 사이인 고객의 평균 상품개수를 출력해보자
```

#### 풀이7

* ex7.sh
```
curl -XGET "http://13.125.21.52:9200/shopping/_search" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "query": {
    "range": {
      "판매자평점": {
        "gte": 2,
        "lte": 4
      }
    }
  },
  "aggs": {
    "avg_item": {
      "avg": {
        "field": "상품개수"
      }
    }
  }
}'
```

* ex7.sh 권한설정
```
$ chmod 777 ex7.sh
```

* ex7.conf
```
input {
  exec {
    command => "/home/ec2-user/fc/logstash-5.6.4/ex7.sh"
    interval => 500
  }
}

filter {
  grok {
    match => { "message" => "%{GREEDYDATA:took}valu%{GREEDYDATA:avg}}}}"}
  }
  mutate {
    split => {"avg" => ":"}
    add_field => {
      "to_be_removed" => "%{avg[0]}"
      "answer" => "%{avg[1]}"
    }
    remove_field => ["took", "message", "command", "to_be_removed", "avg"]
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex8'></a>
#### 문제8
```
사용자 입력을 받아서 콘솔에 출력해보자
```

#### 풀이8
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

<a name='ex9'></a>
#### 문제9
```
이미 읽은 titanic.csv 파일을 강제로 다시 읽어서 콘솔에 출력해보자
```

#### 풀이9
```
input {
  file {
    path => "/home/ec2-user/fc/logstash-5.6.4/sample/titanic.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex10'></a>
#### 문제10
```
mysql(13.125.21.52:3306) database(fc) table(fc)에서 sql_last_value를 활용해서 id > 5인 값을 출력해보자
```

#### 풀이10

* 우선 id < 5 까지 실행해서 `sql_last_value`에 기억시켜둔다
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    use_column_value => true
    tracking_column => id
    statement => "
      SELECT *
      FROM fc
      WHERE id < 5
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

* `sql_last_value` 이상인 값을 출력한다 
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/fc"
    jdbc_user => "fc"
    jdbc_password => "fc"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    use_column_value => true
    tracking_column => id
    statement => "
      SELECT *
      FROM fc
      WHERE id > :sql_last_value
    "
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

<a name='ex11'></a>
#### 문제11
```
* [문제11 : sample.csv를 csf filter로 처리](#ex11)
```
#### 풀이11

<a name='ex12'></a>
#### 문제12
```
* “Seoul”, “Tokyo”, “Beijing”를 입력 받아서 mutate filter 이용해 다음처럼 출력해보자
"Korea":"Seoul",
"Japan":"Tokyo",
"China":"Beijing"
```

#### 풀이12
```
input {
  stdin {}
}

filter {
  mutate {
    copy => { "message" => "original" }
    split => { "message" => "," }
    add_field => {
      "Koera" => "%{message[0]}"
      "Japan" => "%{message[1]}"
      "China" => "%{message[2]}"
    }
    remove_field => ["@version", "host", "@timestamp", "message"]
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```
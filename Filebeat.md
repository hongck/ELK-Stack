### 개요
* Filebeat는 Logstash처럼 데이터를 전송하는 기능이 있지만, 전처리하는 기능이 없다
* 그러므로 log 데이터가 전처리된 형태라면 바로 elasticsearch로 보내고, 그렇지 않으면 logstash로 보내서 전처리하게 하는게 일반적이다
* Logstash 대신 [Ingest Node](https://github.com/higee/elastic/wiki/Ingest-Node)로 보내서 전처리를 할 수도 있다
* output으로 logstash와 elasticsearch 모두 동시에 보낼 수 있지만 여기서는 하나씩 알아보겠다

### 목록
* [Filebeat로 logstash 전송](#filebeat-logstash)
* [Filebeat로 elasticsearch 전송](#filebeat-elasticsearch)

---

<a name='filebeat-logstash'></a>
### Filebeat로 logstash 전송

[[ images/filebeat/filebeat-logstash.gif | height = 500px | width = 1024px]]

#### 다음과 같은 (전처리가 필요한) log 파일이 있다고 하자

```
2017-09-12T00:28:53, test.py, func1, 18, INFO, address found, 37.123, 127.436
2017-09-12T00:33:13, test.py, func2, 25, CRITICAL, address not found, 37.456, 127.124
```

#### 우선 Filebeat Home Directory 이동하자
```
$ cd /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64
```

#### 파일을 읽어 logstash로 전송하도록 Filebeat.yml을 편집하자
```
$ vim filebeat.yml
```

```
#=========================== Filebeat prospectors =============================

filebeat.prospectors:

- input_type: log

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
     - /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64/sample/*.log



#================================ Outputs =====================================


#-------------------------- Elasticsearch output ------------------------------
#output.elasticsearch:
  # Array of hosts to connect to.
  #hosts: ["http://13.125.21.52:9200"]
  #index: "filebeat_index"

#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["13.125.21.52:5044"]
```

#### filebeat를 실행해보자
```
$ ./filebeat -e -c filebeat.yml
```

* connection refused가 나오는 이유는 output인 돌아가고 있는 logstash가 없기 때문이다.
* 만약에 elasticsearch로 했을 경우 elasticsearch가 down인 상태에서 error가 나오는 것과 같은 이유다.

[[ images/filebeat/connection_refused.png | height = 500px | width = 1024px]]

#### logstash를 시작하고 filebeat도 다시 시작하자

* logstash Home Directory 이동
```
$ cd /home/ec2-user/fc/logstash-5.6.4/
```
    
* logstash conf 파일 생성
```
$ vim filebeat.conf
```

* logstash conf 파일 편집
```
input {
  beats {
    port => 5044
  }
}

filter {
  grok {
    match => { "message" => "%{TIMESTAMP_ISO8601:date}, %{USERNAME:file}, %{USERNAME:function}, %{INT:line}, %{LOGLEVEL:level}, %{GREEDYDATA:error}, %{BASE16FLOAT:gps_lat}, %{BASE16FLOAT:gps_lon}"}
  }
  mutate {
    add_field => {
      "gps" => "%{gps_lat}, %{gps_lon}"
    }
    remove_field => ["gps_lat", "gps_lon", "message", "source", "beat", "offset", "@timestamp", "input_type", "tags", "host", "@version"]
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
```

* logstash를 실행하자
```
$ bin/logstash -f filebeat.conf
```

* 그 상태에서 filebeat를 다시 실행해보자
```
$ ./filebeat -e -c filebeat.yml
```

---

<a name='filebeat-elasticsearch'></a>
### Filebeat로 elasticsearch 전송

[[ images/filebeat/filebeat-elasticsearch.gif | height = 500px | width = 1024px]]

#### 다음과 같은 log 파일을 그대로 elasticsearch에 전송한다고 하자

```
2017-10-13T00:28:53, test.py, func1, 18, INFO, address found, 37.123, 127.436
2017-10-13T00:33:13, test.py, func2, 25, CRITICAL, address not found, 37.456, 127.124
```

#### 우선 Filebeat Home Directory 이동하자
```
$ cd /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64
```

#### 파일을 읽어 elasticsearch로 전송하도록 Filebeat.yml을 편집하자
```
$ vim filebeat.yml
```

```
#=========================== Filebeat prospectors =============================

filebeat.prospectors:

- input_type: log

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
     - /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64/sample/*.log



#================================ Outputs =====================================


#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["http://13.125.21.52:9200"]
  index: "filebeat_index"

#----------------------------- Logstash output --------------------------------
#output.logstash:
  # The Logstash hosts
  #hosts: ["13.125.21.52:5044"]
```

#### filebeat를 실행해보자
```
$ ./filebeat -e -c filebeat.yml
```


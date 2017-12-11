### 개요

[[ images/ingest_node/architecture.png | height = 450px | width = 850px]]

* logstash를 통한 파일 전처리 및 전송 기능과 유사한 기능을 elasticsearch ingest node를 통해서 할 수 있다.
* 100% 대체제 관계는 아니니 사용하는 환경/목적에 따라서 선택해서 사용하길 권장한다
* 자세히는 아래 Source를 참고하길 권장하며 여기서는 간단한 사용법 정도를 다룬다

### 목록

* [Index 생성](#index)
* [Pipeline 생성](#pipeline)
* [데이터 전송](#send)
* [Index 확인](#check)

<a name='index'></a>
#### Index 생성

* Ingest Node를 거쳐서 전처리된 데이터를 저장할 Index를 말한다
* 여기서는 다음과 같이 설정해보자
```
PUT ingest_index
{
  "mappings": {
    "ingest_type": {
      "properties": {
        "date": {
          "type": "date"
        },
        "file": {
          "type": "keyword"
        },
        "function": {
          "type": "keyword"
        },
        "line": {
          "type": "keyword"
        },
        "level": {
          "type": "keyword"
        },
        "error": {
          "type": "keyword"
        },
        "gps": {
          "type": "geo_point"
        }
      }
    }
  }
}
```

<a name='pipeline'></a>
#### Pipeline 생성
* logstash에서 filter 부분이 processor를 통해 이루어진다
* processor에 대한 상세한 정보는 [여기](https://www.elastic.co/guide/en/elasticsearch/reference/master/ingest-processors.html)서 확인하자
* 이번 exercise에서는 다음과 같이 설정하자

```
PUT _ingest/pipeline/ingest_pipeline
{
  "description" : "Ingest pipeline",
  "processors" : [
    {
      "grok": {
        "field": "message",
        "patterns": [
          "%{TIMESTAMP_ISO8601:date}, %{USERNAME:file}, %{USERNAME:function}, %{INT:line}, %{LOGLEVEL:level}, %{GREEDYDATA:error}, %{BASE16FLOAT:gps_lat}, %{BASE16FLOAT:gps_lon}"
        ]
      }
    },
    {
      "set": {
        "field": "gps",
        "value": "{{gps_lat}}, {{gps_lon}}"
      }
    },
    {
      "remove": {
        "field": ["gps_lat", "gps_lon", "message", "source", "beat", "offset", "@timestamp", "input_type"]
      }
    }
  ]
}
```
<a name='esnd'></a>
#### 데이터 전송

* filebeat 등을 통해서 데이터가 pipeline을 거쳐 index로 갈 수 있도록 설정한다
* logstash 대신 사용하는 게 목적이므로, 이번에는 filebeat를 통해 다음과 같이 설정했다
* 우선 filebeat Home Directory로 이동하자 : `$ cd /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64`
* 그리고 편집기를 통해 filebeat.yml을 수정하자 : `$ vim filebeat.yml`
* 편집모드로 들어가서 아래처럼 수정하자 : `i`

```
#=========================== Filebeat prospectors =============================

filebeat.prospectors:

- input_type: log

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
     - /home/ec2-user/fc/filebeat-5.6.4-linux-x86_64/sample/*.log

⋮


#================================ Outputs =====================================

#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:

  hosts: ["http://13.125.21.52:9200"]
  index: "ingest_index"
  pipeline: "ingest_pipeline"

#----------------------------- Logstash output --------------------------------
#output.logstash:
  # The Logstash hosts
  #hosts: ["localhost:5044"]
```

* 편집 후에 `:wq`를 통해 저장하고 나오자
* 실행은 filebeat Home Direcory에서 다음처럼 한다 `$ ./filebeat -e -c filebeat.yml`

<a name='check'></a>
#### 데이터 확인

* 우선 원본 데이터를 보자
```
2017-09-11T00:28:53, test.py, func1, 18, INFO, address found, 37.123, 127.436
2017-09-11T00:33:13, test.py, func2, 25, CRITICAL, address not found, 37.456, 127.124
```

* 이런 데이터가 Ingest Pipeline의 processor를 통해 어떻게 변형되었는지 보자
```
GET ingest_index/_search
{
  "query" : {
    "match_all" : {}
  }
}
```

* 결과는 아래와 같다
```
{
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 1,
    "hits": [
      {
        "_index": "ingest_index",
        "_type": "doc",
        "_id": "AWBAmHWXu1AHri8lXBYF",
        "_score": 1,
        "_source": {
          "date": "2017-09-11T00:28:53",
          "level": "INFO",
          "line": "18",
          "gps": "37.123, 127.436",
          "type": "log",
          "error": "address found",
          "file": "test.py",
          "function": "func1"
        }
      },
      {
        "_index": "ingest_index",
        "_type": "doc",
        "_id": "AWBAmHWXu1AHri8lXBYG",
        "_score": 1,
        "_source": {
          "date": "2017-09-11T00:33:13",
          "level": "CRITICAL",
          "line": "25",
          "gps": "37.456, 127.124",
          "type": "log",
          "error": "address not found",
          "file": "test.py",
          "function": "func2"
        }
      }
    ]
  }
}
```



### Source

* [A new way to Ingest - Part 1](https://www.elastic.co/blog/new-way-to-ingest-part-1)
* [A new way to Ingest - Part 2](https://www.elastic.co/blog/new-way-to-ingest-part-2)
* [Ingest Node](https://www.elastic.co/guide/en/elasticsearch/reference/master/ingest.html)
* [Ingest Node to Logstash Config Converter](https://www.elastic.co/blog/ingest-node-to-logstash-configuration-converter)
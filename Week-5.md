### 목록

* [week5_{id}로 index 생성](#ex1)
* [week5_{id} index에 mapping 추가](#ex2)
* [logstash로 데이터 전송하기 (es -> es)](#ex3)
* [logstash로 데이터 전송하기 (jdbc -> es)](#ex4)
* [logstash로 데이터 전송하기 (file -> es)](#ex5)

<a name='ex1'></a>
#### 문제1
```
week5_{id}로 index 생성해보자
```

#### 풀이1
```
PUT week5_higee
```

<a name='ex2'></a>
#### 문제2
```
week5_{id} index에 다음과 같은 mapping을 추가해보자
---
주문시간 : date
수령시간 : date
고객성별 : keyword
고객나이 : integer
물건좌표 : geo_point
고객주소_시도 : keyword
판매사이트 : keyword
판매자평점: integer
상품분류 : keyword
상품가격 : integer
상품개수 : integer
결제카드 : keyword
```

#### 풀이2
```
PUT week5_higee
{
  "mappings": {
    "week5_higee": {
      "properties": {
        "주문시간": {
          "type": "date"
        },
        "수령시간": {
          "type": "date"
        },
        "고객성별": {
          "type": "keyword"
        },
        "고객나이": {
          "type": "integer"
        },
        "물건좌표": {
          "type": "geo_point"
        },
        "고객주소_시도": {
          "type": "keyword"
        },
        "판매사이트": {
          "type": "keyword"
        },
        "판매자평점": {
          "type": "integer"
        },
        "상품분류": {
          "type": "keyword"
        },
        "상품가격": {
          "type": "integer"
        },
        "상품개수": {
          "type": "integer"
        },
        "결제카드": {
          "type": "keyword"
        }
      }
    }
  }
}
```

<a name='ex3'></a>
#### 문제3
```
다음과 같은 조건을 만족하는 elasticsearch의 document만 logstash로 옮겨보자
-----
Input
  host : 13.125.21.52:9200
  index : shopping
  Field : 결제카드, 고객나이, 고객성별, 고객주소_시도, 물건좌표, 상품가격, 상품개수, 상품분류, 수령시간, 주문시간, 판매사이트, 판매자평점
  query
    20 <= 고객나이 <= 30
    구매사이트 : 옥션, 쿠팡, 11번가 중 하나

Output
  host : 13.125.21.52:9200
  index : week5_{id}
```

#### 풀이
```
input {
  elasticsearch {
    hosts => ["http://13.125.21.52:9200"]
    index => "shopping"
    query =>
    '{
      "_source" : ["결제카드", "고객나이", "고객성별", "고객주소_시도", "물건좌표", "상품가격", "상품개수", "상품분류", "수령시간", "주문시간", "판매사이트", "판매자평점"],
      "query": {
        "bool": {
          "must": [
            {
              "range": {
                "고객나이": {
                  "gte": 20,
                  "lte": 30
                }
              }
            },
            {
              "terms": {
                "구매사이트": [
                  "11번가",
                  "옥션",
                  "쿠팡"
                ]
              }
            }
          ]
        }
      }
    }'
  }
}

filter {
  mutate {
    remove_field => ["@version", "@timestamp"]
  }
}
```

<a name='ex4'></a>
#### 문제4
```
mysql의 데이터를 logstash로 elasticsearch에 옮겨보자
---
Input
  host : 13.125.21.52:3306
  user/password : fc/fc
  database : fc
  table : fc

Output
  elasticsearch 
  host : 13.125.21.52:9200
  index : week5_{id}_jdbc
  type : week5_{id}_jdbc
```

#### 풀이4
```
input {
  jdbc {
    jdbc_validate_connection => true
    jdbc_connection_string => "jdbc:mysql://13.125.21.52:3306/final"
    jdbc_user => "final"
    jdbc_password => "final"
    jdbc_driver_library => "/home/ec2-user/fc/logstash-5.6.4/driver/mysql-connector-java-5.1.36/mysql-connector-java-5.1.36-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    statement => "SELECT * FROM final"
  }
}

filter {
  mutate {
    replace => {
      "item" => "스웨터"
      "location" => "서울특별시"
      "sex" => "남성"
      "card" => "시티"
    }
    rename => {
      "item" => "상품분류"
      "quantity" => "상품개수"
      "sex" => "고객성별"
      "rating" => "판매자평점"
      "rec_date" => "수령시간"
      "ord_date" => "주문시간"
      "age" => "고객나이"
      "card" => "결제카드"
      "location" => "고객주소_시도"
    }
    remove_field => ["@version", "@timestamp"]
  }
}

output {
  elasticsearch {
    hosts => ["13.125.21.52:9200"]
    index => "week5_higee_jdbc"
    document_type => "week5_higee_jdbc"
  }
}
```

<a name='ex5'></a>
#### 문제5
```
다음 csv 파일을 적당히 처리해서 elasticsearch로 전송하자
----
Input
 Source https://gist.githubusercontent.com/higee/1e3c3137195cf14eb23dd827a55e9b1d/raw/388c7df111beded4d136465474fcd9bf7826b545/titanic.csv

Filter
  column : "Index", "Name", "Survival", "Pclass", "Sex", "Age", "SibSp", "Parch", "Ticket", "Fare", “Embarked”]
  data type 
    "Pclass" => “integer"
    "Index" => “integer"
    "Survival" => “integer"
    "Age" => “integer"
    “Fare" => "float"

Output
  elasticsearch
  host : 13.125.21.52:9200
  index : week5_{id}_titanic
  type : week5_{id}_titanic
```

#### 풀이
* 데이터 다운로드
```
$ cd /home/ec2-user/fc/logstash-5.6.4
$ wget https://gist.githubusercontent.com/higee/1e3c3137195cf14eb23dd827a55e9b1d/raw/388c7df111beded4d136465474fcd9bf7826b545/titanic.csv
```

* logstash 작성
```
input {
  file {
    path => "/home/ec2-user/fc/logstash-5.6.4/titanic.csv"
  }
}

filter {
  csv {
    columns => ["Index", "Name", "Survival", "Pclass", "Sex", "Age", "SibSp", "Parch", "Ticket", "Fare", "Embarked"]
    separator => ","
    convert => {
      "Pclass" => "integer"
      "Index" => "integer"
      "Survival" => "integer"
      "Age" => "integer"
      "Fare" => "float"
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://13.125.21.52:9200"]
    index => "week5_higee_titanic"
    document_type => "week5_higee_titanic"
  }
}
```

<a name='ex6'></a>
#### 문제6
```
Kibana에서 Index Patterns (week5_higee) 를 등록하자
```

#### 풀이6

[[ images/exercise/week5/ex6.gif | height = 500px | width = 1024px]]

<a name='ex7'></a>
#### 문제7
```
지난 2년 간 기준으로 볼 경우, 총 Documents의 수는?
```

#### 풀이7

[[ images/exercise/week5/ex7.png | height = 500px | width = 1024px]]


<a name='ex8'></a>
#### 문제8
```
지난 2년 간 기준으로 볼 경우, Count가 가장 많았던 월은?
```

#### 풀이8

[[ images/exercise/week5/ex8.gif | height = 500px | width = 1024px]]

<a name='ex9'></a>
#### 문제9
```
수령시간 기준으로 최신순으로 정렬할 경우, 결제카드의 비율은?
```

#### 풀이9

[[ images/exercise/week5/ex9.gif | height = 500px | width = 1024px]]

<a name='ex10'></a>
#### 문제10
```
상품가격을 큰 순으로 정렬할 경우, 상품분류의 비율은?
```

#### 풀이10

[[ images/exercise/week5/ex10.gif | height = 500px | width = 1024px]]

<a name='ex11'></a>
#### 문제11
```
수령시간이라는 Field의 표기를 “XX월 XX일” 형식으로 변환해보자
```

#### 풀이11

[[ images/exercise/week5/ex11.gif | height = 500px | width = 1024px]]

<a name='ex12'></a>
#### 문제12
```
Scripted Field를 이용해서 다음과 같은 Field를 생성해보자
---
시간대 : 주문시간에서 시간대만 추출 (예: 13시 55분 -> 13)
```

#### 풀이12

[[ images/exercise/week5/ex12.gif | height = 500px | width = 1024px]]

<a name='ex13'></a>
#### 문제13
```
Scripted Field를 이용해서 다음과 같은 Field를 생성해보자
---
요일 : 주문시간에서 요일만 추출 (예: 2017년 12월 12일 -> 화)
```

[[ images/exercise/week5/ex13.gif | height = 500px | width = 1024px]]

<a name='ex14'></a>
#### 문제14
```
Area Chart를 이용해서 다음과 같은 Visualization을 만들어보자
---
x축 : 주문시간 기준으로 일별로 정렬
y축 : 주문 개수 (판매자 평점 (1, 2, 3, 4, 5) 별로)
```

#### 풀이 14

[[ images/exercise/week5/ex14.gif | height = 500px | width = 1024px]]

<a name='ex15'></a>
#### 문제15
```
Gauge를 이용해서 다음과 같은 Visualization을 만들어보자
---
평균 상품가격이 높은 결제카드를 3개 선별하여 
각 카드사의 평균 판매자 평점을 0~1, 1~2, 2~3, 3~4, 4~5 구간으로 목표 선정
```

#### 풀이 15

[[ images/exercise/week5/ex15.gif | height = 500px | width = 1024px]]

<a name='ex16'></a>
#### 문제16
```
Heatmap을 이용하여 요일별/시간대별 Count를 나타내는 Visualization을 만들어보자
```

#### 풀이 16
* 요일을 정렬하기 위해 요일_sort 라는 scripted_field 생성
```
doc['주문시간'].date.dayOfWeek
```

* Visualization

[[ images/exercise/week5/ex16.gif | height = 500px | width = 1024px]]

<a name='ex17'></a>
#### 문제17
```
Pie Chart를 이용해서 다음과 같은 Visualization을 만들어보자
---
상품가격의 합이 높은 고객주소_시도 3곳 선정하고 각 Pie에서는 상품가격의 합이 큰 상품분류 5곳의 Value Counts를 시각화하자```

#### 풀이 17

[[ images/exercise/week5/ex17.gif | height = 500px | width = 1024px]]

<a name='ex18'></a>
#### 문제18
```
Data Table을 이용하여 다음과 같은 Visualization을 만들어보자
---
날짜는 수령시간 기준으로 Daily
첫 번째 column에는 일별 상품가격의 합 (매출)
두 번째 column에는 일별 매출 증감
세 번째 column에는 매출 누적합
네 번째 column에는 매출 3일 이동 평균

#### 풀이 18

[[ images/exercise/week5/ex18.gif | height = 500px | width = 1024px]]

<a name='ex19'></a>
#### 문제19
```
Timelion을 이용해서 다음과 같은 Visualization을 만들어보자
---
index : week5_{id}
timefield : 주문시간
올해 매출 (=상품가격의 합) 그래프와 작년 매출 그래프를 생성하고 전년 대비 30,000원 이상 오른 구간을 표시하는 그래프를 만들어보자
```

#### 풀이19

[[ images/exercise/week5/ex19.png | height = 500px | width = 1024px]]

```
.es(index=week5_higee, timefield=주문시간, metric=sum:상품가격).label('올해 매출'),  
.es(index=week5_higee, timefield=주문시간, metric=sum:상품가격, offset=-1y).label('작년 매출').color(#00b8ff),  .es(index=week5_higee, timefield=주문시간, metric=sum:상품가격).subtract(.es(index=week5_higee, timefield=주문시간, metric=sum:상품가격, offset=-1y)).if(gte, 30000, .es(index=week5_higee, timefield=주문시간, metric=sum:상품가격), null).lines(fill=5, width=2).color(#fd8282).label('전년 대비 30,000 이상 상승 구간').yaxis(label=매출)
```

<a name='ex20'></a>
#### 문제20

```
위에서 만든 Visualization을 하나의 Dashboard로 합쳐보자
```

#### 풀이20

[[ images/exercise/week5/ex20.gif | height = 500px | width = 1024px]]

<a name='ex21'></a>
#### 문제21

```
문제18번에서 만든 visualization 데이터를 csv로 export 해보자
```

#### 풀이21

[[ images/exercise/week5/ex21.gif | height = 500px | width = 1024px]]

<a name='ex22'></a>
#### 문제22

```
Filter와 Lucene Query를 이용해서 Discover Page를 보며 다음 질문에 답해보자
2017-01-01에서 2017-12-11 기간 내에 주말 거래건수(총 Count)는? 
```

#### 풀이22

[[ images/exercise/week5/ex22.gif | height = 500px | width = 1024px]]

<a name='ex23'></a>
#### 문제23

```
Filter와 Lucene Query를 이용해서 Discover Page를 보며 다음 질문에 답해보자
2017-01-01에서 2017-12-11 기간 내에 주말 동안 신한 카드 또는 국민 카드 결제 건수는? 
```

#### 풀이23

[[ images/exercise/week5/ex23.gif | height = 500px | width = 1024px]]

<a name='ex24'></a>
#### 문제24

```
Filter와 Lucene Query를 이용해서 Discover Page를 보며 다음 질문에 답해보자
2017-01-01에서 2017-12-11 기간 내에 판매자 평점이 2~4 사이면서 상품분류가 “자”로 시작하는 건수는?
```

#### 풀이24
```
판매자평점 : [2 TO 4] AND 상품분류 : 자*
```

<a name='ex25'></a>
#### 문제25

```
Filter와 Lucene Query를 이용해서 Discover Page를 보며 다음 질문에 답해보자
2017-01-01에서 2017-12-11 기간 내에 20~25세 남성 고객의 10~17시를 제외한 시간대 이용건수는?
```

#### 풀이25

[[ images/exercise/week5/ex25.gif | height = 500px | width = 1024px]]

<a name='ex26'></a>
#### 문제26

```
Filter와 Lucene Query를 이용해서 Discover Page를 보며 다음 질문에 답해보자
2017-01-01에서 2017-12-11 기간 내에 고객주소_시도가 광역시이면서 (니트, 스웨터, 청바지) 중에 적어도 하나를 이용한 건수는?
```

#### 풀이26

```
고객주소_시도 : *광역시 AND 상품분류 : (니트, 스웨터, 청바지)
```

<a name='ex27'></a>
#### 문제27

```
Document API를 이용해서 다음과 같은 Document를 week5_{id} index에 넣어보자
---
{
  "고객주소_시도": "경상남도",
  "상품가격": 9000,
  "고객성별": "남성",
  "고객나이": 35,
  "판매자평점": 1,
  "수령시간": "2017-09-18T14:15:33",
  "상품분류": "자켓",
  "상품개수": 3,
  "물건좌표": "36.95300223329071, 126.06934136285218",
  "주문시간": "2017-11-24T10:31:33",
  "결제카드": “국민"
}
```

#### 풀이27

```
POST week5_higee/week5_higee
{
  "고객주소_시도": "경상남도",
  "상품가격": 9000,
  "고객성별": "남성",
  "고객나이": 35,
  "판매자평점": 1,
  "수령시간": "2017-09-18T14:15:33",
  "상품분류": "자켓",
  "상품개수": 3,
  "물건좌표": "36.95300223329071, 126.06934136285218",
  "주문시간": "2017-11-24T10:31:33",
  "결제카드": “국민"
}
```

<a name='ex26'></a>
#### 문제26

```
고객나이가 20-25세 사이면서 고객주소_시도가 서울특별시이고 고객성별이 남성인 Documents의 상품분류를 “자켓”으로 바꿔보자
```

#### 풀이26

```
POST week5_higee/week5_higee/_update_by_query
{
  "script": {
    "source": "ctx._source['상품분류'] = '자켓'"
  },
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "고객나이": {
              "gte": 20,
              "lte": 25
            }
          }
        },
        {
          "term": {
            "고객주소_시도": "서울특별시"
          }
        },
        {
          "term": {
            "고객성별": "남성"
          }
        }
      ]
    }
  }
}
```

<a name='ex27'</a>
#### 문제27
```
고객나이가 20-25세 사이면서 고객주소_시도가 서울특별시이고 고객성별이 남성인 Documents를 삭제해보자
```

#### 풀이27
```
POST week5_higee/week5_higee/_delete_by_query
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "고객나이": {
              "gte": 20,
              "lte": 25
            }
          }
        },
        {
          "term": {
            "고객주소_시도": "서울특별시"
          }
        },
        {
          "term": {
            "고객성별": "남성"
          }
        }
      ]
    }
  }
}
```

<a name='ex28'</a>
#### 문제28
```
Search API를 이용해서 고객나이가 20-25세 사이인 사람들의 평균 상품가격을 구해보자
```

#### 풀이28
```
GET week5_higee/week5_higee/_search
{
  "size": 0, 
  "query": {
    "range": {
      "고객나이": {
        "gte": 20,
        "lte": 25
      }
    }
  }, 
  "aggs": {
    "NAME": {
      "avg": {
        "field": "상품가격"
      }
    }
  }
}
```

<a name='ex29'</a>
#### 문제29
```
Search API를 이용해서 고객주소_시도가 서울특별시거나 경상남도인 사람들의 평균 판매자평점을 구해보자
```

#### 풀이29
```
GET week5_higee/week5_higee/_search
{
  "size": 0, 
  "query": {
    "terms": {
      "고객주소_시도": [
        "서울특별시",
        "경상남도"]
    }
  }, 
  "aggs": {
    "NAME": {
      "avg": {
        "field": "판매자평점"
      }
    }
  }
}
```

<a name='ex30'</a>
#### 문제30
```
Search API를 이용해서 Daily 수령시간 별 거래건수가 많은 카드사 3개 별 상품가격이 가장 컸던 상품분류를 구해보자
```

#### 풀이30
```

GET week5_higee/week5_higee/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "수령시간",
        "interval": "1d"
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "결제카드",
            "size": 3,
            "order": {
              "_count": "desc"
            }
          },
          "aggs": {
            "1": {
              "top_hits": {
                "docvalue_fields": [
                  "상품분류"
                ],
                "_source": "상품분류",
                "size": 1,
                "sort": [
                  {
                    "상품가격": {
                      "order": "desc"
                    }
                  }
                ]
              }
            }
          }
        }
      }
    }
  }
}
```
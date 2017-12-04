### 목록
* [문제1 : index 생성 및 mapping 추가](#1)
* [문제2 : document api 이용해서 데이터 입력](#2)
* [문제3 : Kibana에서 데이터 포맷 변경](#3)
* [문제4 : reindex api 이용해서 재색인](#4)
* [문제5 : search api로 데이터 조회](#5)
* [문제6 : query string query를 이용한 데이터를 조회](#6)
* [문제7 : sort와 size를 이용한 데이터 조회](#7)
* [문제8 : sort, source, size 이용해서 검색](#8)
* [문제9 : max aggregation query 작성](#9)
* [문제10 : date-histogram, terms, avg aggregation query를 작성](#10)

---

<a name='1'></a>
#### 문제 1
```
week3_exercise_{id}라는 이름의 Index를 생성하고 (id : email 앞자리) 다음과 같이 mapping을 추가하자
Field : 데이터이용량, Datatype : long
Field : 날짜, Datatype : date
Field : 어플리케이션, Datatype : keyword
```
#### 풀이 1
```
PUT week3_exercise_higee
{
  "mappings": {
    "week3_exercise_higee": {
      "properties": {
        "데이터이용량": {
          "type": "long"
        },
        "날짜": {
          "type": "date"
        },
        "어플리케이션": {
          "type": "keyword"
        }
      }
    }
  }
}
```

<a name='2'></a>
#### 문제 2
```
Documents API를 이용해서 다음의 Documents를 넣어보자
{“데이터이용량” : 10000, “날짜” : “2017-11-27T13:00”, “어플리케이션” : “페이스북”}
{“데이터이용량” : 15000, “날짜” : “2017-11-27T15:00”, “어플리케이션” : “지메일”}
```

#### 풀이 2
```
POST week3_exercise_higee/week3_exercise_higee
{
  "데이터이용량": 10000,
  "날짜": "2017-11-27T13:00",
  "어플리케이션": "페이스북"
}
```

```
POST week3_exercise_higee/week3_exercise_higee
{
  "데이터이용량": 15000,
  "날짜": "2017-11-27T15:00",
  "어플리케이션": "지메일"
}
```

<a name='3'></a>
#### 문제 3
데이터 사용량을 Bytes 형식으로 표시되게 해보자 (예: 14.648KB, 9.766KB)

#### 풀이
[[ images/exercise/week3/3.gif | height = 500px | width = 1024px]]

<a name='4'></a>
#### 문제 4
```
Reindex API를 이용해서 위의 Index에서 “어플리케이션” : “페이스북” 인 document를 week3_exercise_{id}_reindex로 옮겨보자
```

#### 풀이 4
```
POST _reindex
{
  "source": {
    "index": "week3_exercise_higee",
    "query": {
      "term": {
        "어플리케이션": "페이스북"
      }
    }
  },
  "dest": {
    "index": "week3_exercise_higee_reindex"
  }
}
```

<a name='5'></a>
#### 문제 5
```
Search API로 shopping Index에서 상품가격이 5000~10000 사이인 Documents를 조회해보자
```

#### 풀이 5
```
GET shopping/_search
{
  "query": {
    "range": {
      "상품가격": {
        "gte": 5000,
        "lte": 10000
      }
    }
  }
}
```

<a name='6'></a>
#### 문제 6
```
Search API로 shopping Index에서 다음과 같은 조건을 만족하는 Documents를 조회해보자
상품분류가 “셔츠”, “니트”, “스웨터” 중 하나이고, 주문날짜가 2017-05-01 ~ 2017-07-01 사이이지만,
고객주소_시도가 “서울특별시”가 아니다
```

#### 풀이 6
```
GET shopping/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "query_string": {
            "query": "상품분류: (\"셔츠\", \"니트\", \"스웨터\") AND NOT 고객주소_시도 : \"서울특별시\""
          }
        },
        {
          "range": {
            "주문시간": {
              "gte": "2017-05-01",
              "lte": "2017-07-01"
            }
          }
        }
      ]
    }
  }
}
```

<a name='7'></a>
#### 문제 7
```
shopping Index에서 주문날짜를 최신순으로 정렬하고 가장 앞 5개를 출력해보자
```

#### 풀이 7
```
GET shopping/_search
{
  "size": 5, 
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "주문시간": {
        "order": "desc"
      }
    }
```

<a name='8'></a>
#### 문제 8
```
shopping Index에서 1) 상품가격이 작은순으로 정렬, 2)그리고 `주문시간` 기준 최신순으로 정렬한 후에 가장 앞 5개 Documents의 `상품분류`를 출력해보자
```

#### 풀이 8
```
GET shopping/_search
{
  "size": 5,
  "_source": "상품분류", 
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "상품가격": {
        "order": "asc"
      }
    },
    {
      "주문시간": {
        "order": "desc"
      }
    }
  ]
}
```

<a name='9'></a>
#### 문제 9
```
shopping Index에서 2017년 11월 동안 상품가격의 최대값을 구해보자
```

#### 풀이 9
```
GET shopping/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "match_all": {}
        },
        {
          "range": {
            "주문시간": {
              "gte": "2017-11-01",
              "lte": "2017-11-30"
            }
          }
        }
      ]
    }
  },
  "aggs": {
    "1": {
      "max": {
        "field": "상품가격"
      }
    }
  }
}
```

<a name='10'></a>
#### 문제 10
```
shopping Index에서 2017년 11월 동안 일별로 평균 상품가격이 높은 구매사이트 5개 별로 평균 상품가격을 구해보자
```

#### 풀이 10
```
GET shopping/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "match_all": {}
        },
        {
          "range": {
            "주문시간": {
              "gte": "2017-11-01",
              "lte": "2017-11-30"
            }
          }
        }
      ]
    }
  },
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "주문시간",
        "interval": "1d",
        "time_zone": "Asia/Seoul",
        "min_doc_count": 1
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "구매사이트",
            "size": 5,
            "order": {
              "3-orderAgg": "desc"
            }
          },
          "aggs": {
            "1": {
              "avg": {
                "field": "상품가격"
              }
            },
            "3-orderAgg": {
              "avg": {
                "field": "상품가격"
              }
            }
          }
        }
      }
    }
  }
}
```
### 주의

* Kibana Console에서 사용 가능한 코드이다
* Kibana Console은 Kibana 접속 후 `Dev Tools` 선택하면 나온다
* 터미널에서 사용하는 `curl`을 이용하려면 적당히 변형이 필요하다
* 한 번 설정한 Mapping은 변경할 수 없다
* reindex는 복사하려는 Index에 Mapping을 먼저 해두기를 권장한다

### 목록

* Indicies API
    * [Index 생성](#create_index)
    * [Index 삭제](#delete_index)
    * Mapping 설정
        * 생성
            * [Index 생성 후 Mapping 설정](#mapping_after)
            * [Index 생성과 동시에 Mapping 설정](#mapping_before)
            * [Template 활용해서 자동으로 Mapping 설정](#template)
        * 추가 : [기존 Mapping에 새로운 Mapping 추가](#add_mapping)
        * 확인 : [특정 Index의 Mapping 확인](#check_mapping)
* Document API
    * [Document 추가](#add_doc)
    * [ID로 Document 조회](#get_doc)
    * Document 삭제
         * [ID로 삭제](#delete_doc_id)
         * [Query로 삭제](#delete_doc_query)
    * Document 수정
         * [ID로 수정](#update_doc_id)
         * [ID로 조건부 수정](#upsert_doc_id)
         * [Query로 수정](#update_doc_query)
    * Documetns 복사
         * [Index 전체 복사](#reindex_full)
         * [Index 일부 복사](#reindex_partial)
         * [외부 Index 복사](#reindex_remote)
* Search API (Request Body Search)
    * Query
        * [Match All](#match_all)
        * [Match](#match)
        * [Query String](#query_string)
        * [Bool](#bool)
        * [Term](#term)
        * [Terms](#terms)
        * [Range](#range)
        * [Exists](#exists)
        * [Prefix](#prefix)
        * [Wildcard](#wildcard)
        * [Fuzzy](#fuzzy)
    * [From/Size](#size)
    * [Sort](#sort)
    * [Source](#source)
    * [Scroll](#scroll)

---

### Indicies API
<a name='create_index'></a>
#### [Index 생성](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html)
* 형식 : ```PUT {Index 이름}```
* 예시 : ```PUT test_index```

[[ images/api/index/create.png | height = 500px | width = 1024px]]

<a name='delete_index'></a>
#### [Index 삭제](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-delete-index.html)
* 형식 : ```DELETE {Index 이름}```
* 예시 : ```DELETE test_index```

[[ images/api/index/delete.png | height = 500px | width = 1024px]]

<a name='mapping_after'></a>
#### [Index 생성 후 Mapping 설정](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html)
* 형식
```
PUT {Index 이름}/_mapping/{Type 이름}
{
  "properties": {
    "{Field 이름}" : {
      "type" : "{Field Type}"
    }
  }
}
```
* 예시    
```
PUT test_index/_mapping/test_type
{
  "properties": {
    "가격" : {
      "type" : "integer"
    }
  }
}
```
[[ images/api/index/mapping_after.png | height = 500px | width = 1024px]]

<a name='mapping_before'></a>
#### [Index 생성과 동시에 Mapping 설정](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html)
* 형식
```
PUT {Index 이름}
{
  "mappings": {
    "{Type 이름}": {
      "properties": {
        "{Field1 이름}" : {
          "type": "{Field1 Type}"
        },
        "{Field2 이름}": {
          "type" : "{Field2 Type}"
        }
      }
    }
  }
}
```
* 예시
```
PUT new_index
{
  "mappings": {
    "new_type": {
      "properties": {
        "가격" : {
          "type": "integer"
        },
        "날짜": {
          "type" : "date"
        }
      }
    }
  }
}
```
[[ images/api/index/mapping_before.png | height = 500px | width = 1024px]]

<a name='template'></a>
#### [Template 활용해서 자동으로 Mapping 설정](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/indices-templates.html)
* 형식
```
PUT _template/{Template 이름}
{
  "template": "{Index Pattern}",
  "mappings": {
    "{Type 이름}": {
      "properties": {
        "Field1 이름": {
          "type": "Field1 Type"
        },
        "Field2 이름": {
          "type": "Field2 Type"
        }
      }
    }
  }
}
```
* 예시
```
PUT _template/template_1
{
  "template": "log-10.*",
  "mappings": {
    "my_type": {
      "properties": {
        "조회수(sec)": {
          "type": "integer"
        },
        "날짜": {
          "type": "date"
        }
      }
    }
  }
}
```

[[ images/api/index/template.png | height = 500px | width = 1024px]]

<a name='add_mapping'></a>
#### 기존 Mapping에 새로운 Mapping 추가
* 형식
```
PUT {Index 이름}/_mapping/{Type 이름}
{
  "properties": {
    "{Field 이름}" : {
      "type" : "{Field Type}"
    }
  }
}
```
* 예시    
```
PUT test_index/_mapping/test_type
{
  "properties": {
    "나이" : {
      "type" : "integer"
    }
  }
}
```
[[ images/api/index/add_mapping.png | height = 500px | width = 1024px]]

<a name='check_mapping'></a>
#### [특정 Index의 Mapping 확인하기](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/indices-get-mapping.html)

* 형식 : `GET /{Index 이름}/_mapping/{Type 이름}`
* 예시 : `GET /test_index/_mapping/test_type`

[[ images/api/index/check_mapping.png | height = 500px | width = 1024px]]

---

### Documents API
<a name='add_doc'></a>
#### [Document 추가](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)

* Document ID 지정
    * 형식
    ```
    PUT {Index 이름}/{Type 이름}/{ID}
    {
      "{Field 이름}" : {Value}
    }
    ```
    * 예시
    ```
    PUT test_index/test_type/1
    {
      "가격" : 10000,
      "나이" : 17,
      "상품" : "맥북프로"
    }
    ```

    [[ images/api/document/add_doc_put.png | height = 500px | width = 1024px]]

* Document ID 지정하지 않음
    * 형식
    ```
    PUT {Index 이름}/{Type 이름}/{ID}
    {
      "{Field 이름}" : {Value}
    }
    ```
    * 예시
    ```
    PUT test_index/test_type/1
    {
      "가격" : 10000,
      "나이" : 17,
      "상품" : "맥북프로"
    }
    ```

    [[ images/api/document/add_doc_post.png | height = 500px | width = 1024px]]

<a name='get_doc'></a>
#### [ID로 Document 조회](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html)

* 형식 : `GET {Index 이름}/{Type 이름}/{ID}`
* 예시 : `GET test_index/test_type/1`

[[ images/api/document/get_doc.png | height = 500px | width = 1024px]]

<a name='delete_doc_id'></a>
#### [ID로 Document 삭제](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete.html)

* 형식 : `DELETE {Index 이름}/{Type 이름}/{ID}`
* 예시 : `DELETE test_index/test_type/1`

[[ images/api/document/delete_doc_id.png | height = 500px | width = 1024px]]

<a name='delete_doc_query'></a>
#### [Query로 Document 삭제](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete-by-query.html)

* 형식 (match query 예시)
```
POST {Index 이름}/_delete_by_query
{
  "query": { 
    "match": {
      "{Field 이름}": "{Value}"
    }
  }
}
```
* 예시
```
POST test_index/_delete_by_query
{
  "query": { 
    "match": {
      "상품": "아이폰"
    }
  }
}
```

[[ images/api/document/delete_doc_query.png | height = 500px | width = 1024px]]

<a name='update_doc_id'></a>
#### [ID로 Document 수정](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)

* 형식
```
POST {Index 이름}/{Type 이름}/{ID}/_update
{
  "doc": {
    "{Field}" : {Value}  
  } 
}
```
* 예시
```
POST test_index/test_type/3/_update
{
  "doc": {
    "가격" : 17000  
  } 
}
```

[[ images/api/document/update_doc_id.png | height = 500px | width = 1024px]]

<a name='upsert_doc_id'></a>
#### [ID로 조건부 Document 수정 : Document가 없으면 Document 생성하는 조건](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)

* 형식
```
POST {Index 이름}/{Type 이름}/{ID}/_update
{
  "doc" : {
    "{Field 이름}" : "{Value}"
  },
  "doc_as_upsert" : true
}
```
* 예시
```
POST test_index/type_type/3/_update
{
  "doc" : {
    "나이" : "30"
  },
  "doc_as_upsert" : true
}
```

[[ images/api/document/upsert_doc_id.png | height = 500px | width = 1024px]]

<a name='update_doc_query'></a>
#### [Query로 Document 수정](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update-by-query.html)
* 형식 (term query 예시)
```
POST {Index 이름}/{Type 이름}/_update_by_query
{
  "script": {
    "source": "ctx._source[{Field 이름}] = Value
  },
  "query": {
    "term": {
      "{Field 이름}": "Value"
    }
  }
}
```
* 예시
    * 1개 Field
    ```
    POST test_index/test_type/_update_by_query
    {
      "script": {
        "source": "ctx._source['가격'] = 7777",
         "lang": "painless"
      },
      "query": {
        "term": {
          "상품": "아이폰"
        }
      }
    }
    ```
    * 복수개 Field
    ```
    POST test_index/test_type/_update_by_query
    {
      "script": {
        "inline" : "ctx._source['가격'] = 7777; ctx._source['나이'] = 19];",
        "lang" : "painless"
      },
      "query": {
        "term": {
          "나이": 17
        }
      }
    }
    ```
   [[ images/api/document/update_doc_query.png | height = 500px | width = 1024px]]

#### [Document 복사](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html)
<a name='reindex_full'></a>
* 전체 Documents 복사
    * 형식
    ```
    POST _reindex
    {
      "source": {
        "index": "{복사하려는 원본 Index 이름}"
      },
      "dest": {
        "index": "{복사본을 저장할 Index 이름}"
      }
    }
    ```
    * 예시
    ```
    POST _reindex
    {
      "source": {
        "index": "test_index"
      },
      "dest": {
        "index": "test_index_2"
      }
    }
    ```
    [[ images/api/document/reindex_full.png | height = 500px | width = 1024px]]

<a name='reindex_partial'></a>
* 일부 Documents 복사
    * 형식
    ```
    POST _reindex
    {
      "source": {
        "index": "{복사하려는 Index 이름}",
        "type" : "{복사하려는 Type 이름}",
        "query": {
          "term": {
            "{Field 이름}": "{Value}"
          }
        }
      },
      "dest": {
        "index": "{복사본을 저장할 Index 이름}"
      }
    }
    ```
    * 예시
    ```
    POST _reindex
    {
      "source": {
        "index": "test_index",
        "type" : "test_type",
        "query": {
          "term": {
            "상품": "아이폰"
          }
        }
      },
      "dest": {
        "index": "test_index_3"
      }
    }
    ```
    [[ images/api/document/reindex_partial.png | height = 500px | width = 1024px]]

<a name='reindex_remote'></a>
* 외부에 있는 Elasticsearch Index 복사
    * 형식
    ```
    POST _reindex
    {
      "source": {
        "remote": {
          "host": "{Elasticsearch url}",
          "username": "{유저 이름}",
          "password": "{비밀번호}"
        },
        "index": "{복사할 Index 이름}",
      },
      "dest": {
        "index": "{복사본을 저장할 Index 이름}"
      }
    }
    ```
    * 예시
    ```
    POST _reindex
    {
      "source": {
        "remote": {
          "host": "http://otherhost:9200",
          "username": "user",
          "password": "pass"
        },
        "index": "source",
        "query": {
          "match": {
            "test": "data"
          }
        }
      },
      "dest": {
        "index": "dest"
      }
    }
    ```

---

### Search API

<a name='match_all'></a>
#### [Match All Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-match-all-query.html)
* 설명 : Index의 모든 Documents를 보여준다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query" : {
    "match_all" : {}
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query" : {
    "match_all" : {}
  }
}
```

[[ images/api/search/match_all.png | height = 500px | width = 1024px]]

<a name='match'></a>
#### [Match Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-match-query.html)
* 설명 : Match 조건을 충족하는 Documents를 보여준다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query" : {
    "match" : {
      "{Field 이름}" : "{Value}"
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query" : {
    "match" : {
      "상품분류" : "셔츠"
    }
  }
}
```

[[ images/api/search/match1.png | height = 500px | width = 1024px]]

* 파라미터
    * `operator`, `minimum_should_match`가 자주 쓰인다
    * operator
        * `and` 또는 `or` 이며 기본은 `or`이다
        * `query`가 "상품 이상"면 
            * and일 경우 "상품", "이상"를 모두 가진 Document가 출력되며
            * or일 경우, "상품", "이상" 중 하나라도 가진 Document가 출력된다
        * 예시
        ```
        GET shopping/shopping/_search
        {
          "query": {
            "match" : {
              "배송메모" : {
                "query" : "상품 이상",
                "operator" : "and"
              }
            }
          }
        }
        ```
     [[ images/api/search/match2.png | height = 500px | width = 1024px]]
     * minimum_should_match
        * `query`가 "상품 이상 고장"이고 `minium_should_match`가 2라고 하면 "상품", "이상", "고장" 중 적어도 2개는 포함하는 Documents가 출력된다. (operator는 default로 or이다)
        * 예시
        ```
        GET shopping/shopping/_search
        {
          "query": {
            "match" : {
              "배송메모" : {
                "query" : "상품 이상 고장",
                "minimum_should_match" : 2
              }
            }
          }
        }
        ```
        [[ images/api/search/match3.png | height = 500px | width = 1024px]]

<a name='query_string'></a>
#### [Query String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-query-string-query.html#query-dsl-query-string-query)

* 설명 : Search에서 사용했던 Lucene Query Syntax를 만족하는 Documents가 표시된다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query" : {
    "query_string" : {
      "query" : "{LUCENE QUERY}"
    }
  }
}
```
* 예시
```
GET shopping/_search
{
  "query" : {
    "query_string" : {
      "query": "상품가격 : [1 TO 50000]"
    }
  }
}
```

[[ images/api/search/query_string.png | height = 500px | width = 1024px]]

<a name='bool'></a>
#### [Bool Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-bool-query.html)
* 설명
    * 다양한 종류의 혹은 복수개의 query를 묶어서 사용할 수 있게 해준다
    * 예를 들어 query1, query2는 만족하면서 query3은 만족하지 않는 Documents를 찾고 싶은 경우
* 사용 가능한 Boolean Clauses
    * must : 반드시 만족해야 하는 query
    * must not : 반드시 제외되어야 하는 query
* 예시 
```
GET shopping/shopping/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": { "결제카드":  "시티" }
        }
      ],
      "must_not": [
        { "term": { "구매사이트": "11번가" }},
        { "range": { 
            "상품가격" : {"gte" :  20000 }}}
      ]
    }
  }
}
```
[[ images/api/search/bool.png | height = 500px | width = 1024px]]

<a name='term'></a>
#### [Term Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-term-query.html)

* 설명 : 검색 Term과 정확히 일치하는 Document를 표시한다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query" : {
    "term" : {
      "{Field 이름}" : "{Value}"
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query" : {
    "term" : {
      "상품분류" : "셔츠"
    }
  }
}
```
[[ images/api/search/term.png | height = 500px | width = 1024px]]

<a name='terms'></a>
#### [Terms Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-terms-query.html)
* 설명 : 검색 Terms 중에서 적어도 1개의 Term과 정확히 일치하는 Document를 표시한다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query" : {
    "terms" : {
      "{Field 이름}" : ["{Value}", "{Value}"]
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query" : {
    "terms" : {
      "상품분류" : ["셔츠", "스웨터"]
    }
  }
}
```
[[ images/api/search/terms.png | height = 500px | width = 1024px]]

<a name='range'></a>
#### [Range Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-range-query.html)
* 설명 : 특정 Field의 값이 입력값의 범위 내에 존재하는 Document를 표시한다
* 파라미터
    * `gte` : 특정값보다 크거나 같다
    * `gt` : 특정값보다 크다
    * `lte` : 특정값보다 작거나 같다
    * `lt` : 특정값보다 작다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query": {
    "range": {
      "{Field 이름}": {
        "gte": "{Value},
        "lte": "{Value},
      }
    }
  }
}
```
* 예시
    * Date Field
    ```
    GET shopping/shopping/_search
    {
      "query": {
        "range": {
          "주문시간": {
            "gte": "2017-02-15T07:00:00"
          }
        }
      }
    }
    ```
    [[ images/api/search/range_date.png | height = 500px | width = 1024px]]

    * Number Field
    ```
    GET shopping/shopping/_search
    {
      "query": {
        "range": {
          "상품가격": {
            "gte" : 15000,
            "lte" : 20000
          }
        }
      }
    }
    ``` 
    [[ images/api/search/range_number.png | height = 500px | width = 1024px]]

<a name='exists'></a>
#### [Exists Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-exists-query.html)
* 설명 : 검색하려는 Field에 [non-null value](https://github.com/higee/elastic/issues/4)가 하나라도 있는 Document를 표시한다        
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query": {
    "exists" : { 
      "field" : "{Field 이름}" 
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query": {
    "exists" : { 
      "field" : "상품분류" 
    }
  }
}
```
[[ images/api/search/exists.png | height = 500px | width = 1024px]]

<a name='prefix'></a>
#### [Prefix Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-prefix-query.html)
* 설명 : 특정 Field가 특정 검색어로 시작하는 Documents 표시
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{ 
  "query": {
    "prefix" : { 
      "{Field 이름}" : "{Value}" 
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query": {
    "prefix" : { 
      "고객주소_시도" : "경상" 
    }
  }
}
```
[[ images/api/search/prefix.png | height = 500px | width = 1024px]]

<a name='wildcard'></a>
#### [Wildcard Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-wildcard-query.html)
* 설명 : 특정 Field가 wildcard expression을 만족하는 Documents 표시
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query": {
    "wildcard" : { 
      "{Field 이름}" : "{Value}" 
    }
  }
}
```
* 예시
```
GET shopping/shopping/_search
{
  "query": {
    "wildcard" : { 
      "고객주소_시도" : "경**도" 
    }
  }
}
```
[[ images/api/search/wildcard.png | height = 500px | width = 1024px]]


<a name='fuzzy'></a>
#### [Fuzzy Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.4/query-dsl-fuzzy-query.html)
* 설명 : 특정 Field의 Value가 검색어와 유사한 Document 보여준다
* 파라미터 : `fuzziness`로서 편집 거리를 나타낸다
* 형식
    * 기본 (fuzziness=auto)
    ```
    GET {Index 이름}/{Type 이름}/_search
    {
      "query": {
        "fuzzy" : { 
          "{Field 이름}" : "{Value}" 
        }
      }
    }
    ```
    * fuzziness 할당
    ```
    GET {Index 이름}/{Type 이름}/_search
    {
      "query": {
        "fuzzy" : {
          "{Field 이름}" : {
            "value" : "{Value}",
            "fuzziness" : {Fuzziness 정도}
          }
        }
      }
    }
    ```
* 예시
```
GET shopping/shopping/_search
{
  "query": {
    "fuzzy" : {
      "고객주소_시도" : {
        "value" : "경상북남",
        "fuzziness" : 2
      }
    }
  }
}
```
[[ images/api/search/fuzzy.png | height = 500px | width = 1024px]]

<a name='size'></a>
#### [From/Size](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-from-size.html)

* 설명 : 결과 Documents의 Pagination으로 조건을 만족하는 Documents 중, 어느 위치에서부터 몇 개를 출력할지 결정한다
* 예시 : 처음 10개 출력하는 경우
```
GET shopping/shopping/_search
{
  "from" : 0, "size" : 1,
  "query" : {
    "match_all" : {}
  }
}
```
[[ images/api/search/from_size.png | height = 500px | width = 1024px]]

<a name='sort'></a>
#### [Sort](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-sort.html)

* 설명 : 결과 Documents를 특정 기준으로 정렬해준다
* 파라미터 : 정렬기준은 `desc(내림차순)` 또는 `asc(오름차순)`이 있다
* 형식
```
GET {Index 이름}/{Type 이름}/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "{FIELD 이름}": {
        "order": "{정렬기준}"
      }
    }
  ]
}
```
* 예시 : 판매자평점이 큰 순으로 정렬
```
GET shopping/shopping/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "판매자평점": {
        "order": "desc"
      }
    }
  ]
}
```
[[ images/api/search/sort.png | height = 500px | width = 1024px]]

<a name='source'></a>
#### [Source](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html)

* 설명 : 결과 Documents에서 특정 Field만 조회하고 싶을 때 사용
* 형식
    * 기본 : `_source`에 표시한 Field만 조회
    ```
    GET {Index 이름}/{Type 이름}/_search
    {
      "_source": "{Field 이름}",
      "query" : {
        "match_all" : {}
      }
    }
   ```
   * 응용 : 특정 필드는 제외하고, 특정 필드는 포함시키고 싶을 때
   ```
   GET {Index 이름}/{Type 이름}/_search
   {
     "_source": {
       "includes" : "{Field 이름}",
       "excludes" : "{Field 이름}"
     },
     "query" : {
       "match_all" : {}
     }
   }
   ```
* 예시
```
GET shopping/shopping/_search
{
  "_source": {
    "includes" : ["고객*", "구매사이트"],
    "excludes" : "상품*"
  },
  "query" : {
    "match_all" : {}
  }
}
```
[[ images/api/search/source.png | height = 500px | width = 1024px]]

<a name='scroll'></a>
#### [Scroll](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html)

* 설명 : 검색된 Documents는 한 번에 보여주지 않고 Page 별로 보여주게 하는 기능
* 사용법 예시
    * 검색 Query 작성
    ```
    POST shopping/shopping/_search?scroll=10m
    {
      "size": 500,
      "query": {
        "match" : {
          "상품분류" : "셔츠"
        }
      }
    }
    ```
    [[ images/api/search/scroll1.png | height = 500px | width = 1024px]]
    * 결과로 나온 `_scroll_id` 확인
    * scroll를 이용한 결과 조회
    ```
    POST  /_search/scroll 
    {
      "scroll" : "10m", 
      "scroll_id" : "{_scroll_id}" 
    }
    ```
    [[ images/api/search/scroll_result.gif | height = 500px | width = 1024px]]
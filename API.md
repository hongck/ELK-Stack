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
#### [ID로 조건부 Document 수정](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)

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
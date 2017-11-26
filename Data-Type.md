|대분류|중분류|소분류|설명|예시|
|--|--|--|--|--|
|Core|String|[Text](https://www.elastic.co/guide/en/elasticsearch/reference/current/text.html)|Term 단위로 분석기를 거쳐 색인| "Elasticsearch는 검색엔진입니다."
|Core|String|[Keyword](https://www.elastic.co/guide/en/elasticsearch/reference/current/keyword.html)| 더 이상 작게 나누지 않으며 카테고리 데이터 자주 사용 | "한국", "일본", "중국", "미국"
|Core|Date|[Date](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html)| `string`, `long`, `integer` 등을 이용해 날짜 표시 | "2015-01-01", 1420070400001
|Core|Boolean|[Boolean](https://www.elastic.co/guide/en/elasticsearch/reference/current/boolean.html)| 특정 Field의  True/False 값 표시 | true, "true", false, "false"
|Core|Numeric|[Long](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html)| 기본 정수형으로 `integer`, `short` 등 사용 가능 | 1, 100, 10000
|Core|Numeric|[Double](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html)| 기본 부동소수점으로 `float`, `half_float` 등 사용 가능 | 0.1, 0.01, 0.001
|Geo|Geo-point|[Geo-point](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html)| 위도, 경도 쌍으로 특정 좌표 표시| "41.12,-71.34", [ -71.34, 41.12 ]
|Complex|Array|[Array](https://www.elastic.co/guide/en/elasticsearch/reference/current/array.html)| 같은 datatype의 값을 복수개 갖음 | [1, 2, 3], ["one", "two"]
|Complex|Object|[Object](https://www.elastic.co/guide/en/elasticsearch/reference/current/object.html)| Field가 또 다른 Field를 갖음| "출신": { "국가":"한국", "도시":"서울"}
|Complex|Nested|[Nested](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html)| 복수개 Object Field 존재| "출신":[{"국가":"한국", "도시":"서울"}, {"국가":"한국", "도시":"부산"}]


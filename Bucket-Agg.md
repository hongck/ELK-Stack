### 개요 
* Bucket은 Group 정도로 보면 된다.
* Bucket Aggregation은 일정 기준으로 Elasticsearch Documents를 (여러) Bucket으로 나눈다.
* [Metric Aggregation]()과 달리 특정 Field(s)에 대해서 연산을 수행하지는 않는다.

### 참고
* Date Range Aggregation는 Bucket 마다 다른 Interval을 가질 수 있으며 (Histogram과 달리) 모든 데이터가 포함되지 않아도 된다.
* Date Range의 `from`과 `to`는 Range Aggregation과 달리 [Math Expression](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/common-options.html#date-math) 사용 가능하다
* Significant Terms 사용시 Filter 혹은 Search 등을 활용해서 Foreground를 설정해야 유의미한 결과를 얻는다

### 종류

`Date Histogram Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| Date Field의 Histogram 각 구간으로 Bucket 생성 | Date| Interval : 초, 분, 시간, 일, 주, 월, custom |

[[ images/bucket_agg_date_histogram.gif | height = 500px | width = 1024px]]

`Date Range Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| 옵션으로 설정한 Date Field의 각 구간으로 Bucket 생성 | Date| From : 구간 시작 Date <br /> To : 구간 끝 Date

[[ images/bucket_agg_date_range.gif | height = 450px | width = 1024px]]

`Histogram Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| Number Field의 각 구간으로 Bucket 생성 | Number | Interval : 각 구간의 간격 설정

[[ images/bucket_agg_histogram.gif | height = 500px | width = 1024px]]

`Range Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| 옵션으로 설정한 Number Field의 각 구간으로 Bucket 생성 | Number| From : 구간 시작 값 <br /> To : 구간 끝 값

[[ images/bucket_agg_range.gif | height = 500px | width = 1024px]]

`IPv4 Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| IP값의 범위/성격으로 Bucket 생성 | ip| From & To : IP 범위 설정 <br /> Mask : CIDR 마스크

[[ images/bucket_agg_ipv4.gif | height = 500px | width = 1024px]]

`Terms Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| 선택한 Term를 기준으로 Bucket 생성 | Date, Number, Ip, String|  Field : Terms Aggregation 적용할 Field 선택 <br /> Order by : 정렬할 기준 선택 (Field 이름 또는 함수 가능) <br /> Order : Order로 정한 결과를 오름차순/내림차순 결정 <br /> Size : Bucket 몇 개 생성할 지 결정

[[ images/bucket_agg_terms.gif | height = 500px | width = 1024px]]

`Significant Terms Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| Background 대비 선택한 Foreground에서 특별한 Term으로 Bucket 생성 | String| Field <br /> Size : Bucket 몇 개 생성할 지 결정

[[ images/bucket_agg_significant_terms.gif | height = 500px | width = 1024px]]

`Filters Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| Query String으로 작성한 조건 만족하는 Bucket 생성 | {} | Filters : [Query String 문법](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)

[[ images/bucket_agg_filters.gif | height = 500px | width = 1024px]]

`Geo Hash Aggregation`

| 역할  | Data Type | 주요 옵션 | 
|-------------------|:------:| ----------|
| Geo Point Field의 각 Centroid로 Bucket 생성 | Geo Point| Precision : Centroid로 묶을 범위 (작으면 많이 묶인다)

[[ images/bucket_agg_geo_hash.png | height = 500px | width = 1024px]]
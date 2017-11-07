### 개요 
* Bucket은 Group 정도로 보면 된다.
* Bucket Aggregation은 일정 기준으로 Elasticsearch Documents를 (여러) Bucket으로 나눈다.
* [Metric Aggregation]()과 달리 특정 Field(s)에 대해서 연산을 수행하지는 않는다.

### 참고
* Date Range Aggregation는 Bucket 마다 다른 Interval을 가질 수 있으며 (Histogram과 달리) 모든 데이터가 포함되지 않아도 된다.
* Date Range의 `from`과 `to`는 Range Aggregation과 달리 [Math Expression](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/common-options.html#date-math) 사용 가능하다
* Significant Terms 사용시 Filter 혹은 Search 등을 활용해서 Foreground를 설정해야 유의미한 결과를 얻는다

### 종류

* [Date Histogram Aggregation](#dh)
* [Date Range Aggregation](#dr)
* [Histogram Aggregation](#ha)
* [Range Aggregation](#ra)
* [IPv4 Aggregation](#ip)
* [Terms Aggregation](#ta)
* [Significant Terms Aggregation](#st)
* [Filters Aggregation](#fa)
* [Geo Hash Aggregation](#gh)

---

#### Date Histogram Aggregation<a name="dh"></a>

* 역할 : Date Field의 Histogram 각 구간으로 Bucket 생성
* Date Type : Date
* 주요 옵션
    * Interval : 구간을 얼마나 잘게 나눌지 설정
        * 옵션 : 밀리초, 초, 분, 시, 일, 주, 월, custom

[[ images/bucket_agg_date_histogram.gif | height = 500px | width = 1024px]]

#### Date Range Aggregation<a name="dr"></a>


* 역할 : 각 Date 구간의 시작과 끝점을 설정하여 Bucket 생성
* Date Type : Date
* 주요 옵션
    * From : 구간 시작 날짜
    * To : 구간 끝 날짜

[[ images/bucket_agg_date_range.gif | height = 450px | width = 1024px]]

#### Histogram Aggregation<a name="ha"></a>


* 역할 : Number Field의 각 구간으로 Bucket 생성
* Date Type : Number
* 주요 옵션
    * Interval : 각 구간 간격 설정

[[ images/bucket_agg_histogram.gif | height = 500px | width = 1024px]]

#### Range Aggregation<a name="ra"></a>


* 역할 : 옵션으로 설정한 Number Field의 각 구간으로 Bucket 생성
* Date Type : Number
* 주요 옵션
    * From : 구간 시작 값
    * To : 구간 끝 값

[[ images/bucket_agg_range.gif | height = 500px | width = 1024px]]

#### Terms Aggregation<a name="ta"></a>

* 역할 : 선택한 Term를 기준으로 Bucket 생성
* Date Type : Date, Number, IP, String
* 주요 옵션
    * Field : 적용할 Field 선택
    * Order By : Field 값들을 어떤 기준으로 정렬할 것인지 설정
        * 옵션
            * Custom Metric : 어떤 Metric Agg로 할 것인지 설정
            * Term : 값 자체로 대소 비교 (string : alphabet, number : 대소)
    * Order : Order By된 값들을 오름차순 또는 내림차순 할 것인지 설정
    * Size : Bucket 개수 결정

[[ images/bucket_agg_terms.gif | height = 500px | width = 1024px]]

#### Significant Terms Aggregation<a name="st"></a>

* 역할 : Background 대비 선택한 Foreground에서 특별한 Term으로 Bucket 생성
* Date Type : String
* 주요 옵션
    * Size : Bucket 몇 개 생성할 지 설정

[[ images/bucket_agg_significant_terms.gif | height = 500px | width = 1024px]]

#### Filters Aggregation<a name="fa"></a>

* 역할 : [Query String](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)으로 작성한 조건 만족하는 Bucket 생성
* Date Type : *

[[ images/bucket_agg_filters.gif | height = 500px | width = 1024px]]

#### Geo Hash Aggregation<a name="gh"></a>

* 역할 : Geo Point Field의 각 Centroid로 Bucket 생성
* Date Type : Geo Point
* 주요 옵션
    * Precision : Centroid에서 얼마나 가까이/멀리까지 묶을지 설정 (작으면 많이 묶인다)

[[ images/bucket_agg_geo_hash.gif | height = 500px | width = 1024px]]

#### IPv4 Aggregation<a name="ip"></a>

* 역할 : IP값의 범위/성격으로 Bucket 생성
* Date Type : IP
* 주요 옵션
    * Use From/To
        * From  : 구간 내 가장 작은 IP 주소값
        * To : 구간 내 가장 큰 IP 주소값
    * Use CIDR Masks
        * Mask : CIDR mask 설정

[[ images/bucket_agg_ipv4.gif | height = 500px | width = 1024px]]
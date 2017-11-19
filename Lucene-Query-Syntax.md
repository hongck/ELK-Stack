### 주의

* scripted field는 검색이 안된다. 대신 필터로 사용해야 한다
* 기본 operator는 OR 연산자다. `서울 한국`으로 검색하면 `서울` 또는 `한국`이 존재하는 문서가 검색된다.
* 텍스트 검색은 case sensitive하지 않다. 예를 들어 hello나 Hello나 같은 결과를 반환한다.
* " " 내부의 단어는 정확한 일치를 원칙으로 하기에 "hello"와 "Hello"는 다르다
* 구(phrase) 내에서는 wildcard를 사용할 수 없다.

### 목록

* [Keyword 검색](#keyword)
* [Field Match 검색](#field_match)
* [Exact Field Match 검색](#exact_field_match)
* [Must be present 검색](#must_be_present)
* [Must not be present 검색](#must_not_be_present)
* [AND 검색](#and)
* [OR 검색](#or)
* [NOT 검색](#not)
* [Grouping 검색](#grouping)
* [Term 검색](#term)
* [Fuzzy 검색](#fuzzy)
* [Proximity 검색](#proximity)
* [Numeric Values 검색](#numeric_values)
* [Range 검색](#range)
* [Wildcard ? 검색](#wildcard_?)
* [Wildcard * 검색](#wildcard_*)
---

<a name='keyword'></a>
#### Keyword 검색

* 기능 : 임의의 Field Value가 검색어와 일치하는 Documents 찾기
* 예시 : `셔츠` 
* 예시 설명 : 임의의 Field Value가 `셔츠`인 Documents 찾기

[[ images/search/keyword.png | height = 500px | width = 1024px]]

<a name='field_match'></a>
#### Field Match 검색

* 기능 : 특정 Field의 Value가 검색어와 일치하는 Documents 검색
* 예시 : `배송메모:부재중` 
* 예시 설명 : `배송메모` Field의 Value가 `부재중`인 Documents 찾기

[[ images/search/field_match.png | height = 500px | width = 1024px]]

<a name='exact_field_match'></a>
#### Exact Field Match 검색

* 기능
    * 특정 Field의 Value가 검색어와 정확히 일치하는 Documents 찾기
    * 큰 따옴표("")는 정확히 일치하는 Documents를 찾고 싶을 때 사용
* 예시 : `배송메모:"상품 이상"` 
* 예시 설명
    * `배송메모` Field의 Value가 정확히 `상품 이상`인 Documents 찾기
    * 큰 따옴표 없이 검색할 경우 `배송메모`가 `상품` 또는 `이상`인 Documents를 찾게된다.

[[ images/search/exact_field_match.png | height = 500px | width = 1024px]]

<a name='must_be_present'></a>
#### Must be present 검색

* 기능 : 특정 Field가 존재하는 Documents 찾기
* 예시 : `_exists_:상품분류` 
* 예시 설명 : `상품분류` Field가 존재하는 Documents 찾기

[[ images/search/must_be_present.png | height = 500px | width = 1024px]]

<a name='must_not_be_present'></a>
#### Must not be present 검색

* 기능 : 특정 Field가 존재하지 않는 Documents 찾기
* 예시 : `_missing_:상품분류` 
* 예시 설명 : `상품분류` Field가 존재하지 않는 Documents 찾기

[[ images/search/must_not_be_present.png | height = 500px | width = 1024px]]

<a name='and'></a>
#### AND 검색

* 기능 : 여러 조건을 동시에 만족하는 Documents 찾기
* 예시
    * `셔츠 AND 서울특별시`
    * `고객성별:여성 AND 고객나이:40`   
* 예시 설명
    * 임의 Field의 Value가 `셔츠`와 `서울특별시`인 Documents 찾기
    * `고객성별` Field가 `여성`이고 `고객나이` Field가 `40`인 Documents 찾기

[[ images/search/and_keyword.png | height = 500px | width = 1024px]]
[[ images/search/and_field.png | height = 500px | width = 1024px]]

<a name='or'></a>
#### OR 검색

* 기능 : 여러 조건 중 최소 1개를 만족하는 Documents 찾기
* 예시
    * `셔츠 OR 서울특별시`
    * `고객성별:여성 OR 고객나이:40`   
* 예시 설명
    * 임의의 Field 값이 `셔츠`거나 `서울특별시`인 Documents 찾기
    * `고객성별` Field Value가 `여성`이거나 `고객나이` Field Value가 `40`인 Documents 찾기

[[ images/search/or_keyword.png | height = 500px | width = 1024px]]
[[ images/search/or_field.png | height = 500px | width = 1024px]]

<a name='not'></a>
#### NOT 검색

* 기능 : 해당 조건을 제외한 Documents 찾기
* 예시
    * `NOT 셔츠`
    * `NOT 고객성별:여성`   
* 예시 설명
    * 임의의 Field Value가 `셔츠`가 아닌 Documents 찾기
    * `고객성별` Field Value가 `여성`이 아닌 Documents 찾기

[[ images/search/not_keyword.png | height = 500px | width = 1024px]]
[[ images/search/not_field.png | height = 500px | width = 1024px]]

<a name='grouping'></a>
#### Grouping 검색

* 기능 : 여러 조건을 만족하는 Documents 찾기
* 예시
    * `NOT 셔츠 AND (20대 OR 서울특별시)`
    * `NOT 고객성별:여성 AND (상품분류:셔츠 OR 구매사이트:티몬)`
* 예시 설명
    * 임의의 Field의 Value가 `셔츠`가 아니면서 임의의 Field Value가 `20대`이거나 `서울특별시`인 Documents 찾기
    * `고객성별` Field의 Value가 `여성`이 아니면서 `상풍분류` Field의 Value가 `셔츠`이거나 `구매사이트` Field의 Value가 `티몬`인 Documents 찾기

[[ images/search/grouping_keyword.png | height = 500px | width = 1024px]]
[[ images/search/grouping_field.png | height = 500px | width = 1024px]]

<a name='term'></a>
#### Term 검색

* 기능 : 조건 중 적어도 하나라도 만족하는 Documents 찾기
* 예시
    * `(우리 니트)`
    * `상품분류:(니트 코트)`
* 예시 설명
    * 임의의 Field의 Value가 `우리`거나 `니트`인 Documents 찾기
    * `상품분류` Field의 Value가 `니트`거나 `코트`인 Documents 찾기

[[ images/search/term_keyword.png | height = 500px | width = 1024px]]
[[ images/search/term_field.png | height = 500px | width = 1024px]]

<a name='fuzzy'></a>
#### Fuzzy 검색

* 기능
    * 검색어와 유사한 Value를 갖는 Documents 찾기 
    * 자세히는, 검색어와 임의의 Field Value가 같아지기 위해 몇 번의 편집 연산(삽입/삭제/대체)을 해야하는지 계산하여 구한다
* 예시
    * `경상북두~`
    * `구매사이트:22번가~2`
* 예시 설명
    * 임의의 Field의 Value가 `경상북두`이거나 한 글자만 다른 Documents 찾기
    * `구매사이트` Field Value가 `22번가`이거나 두 글자까지만 다른 Documents 찾기
* [옵션 설명](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#fuzziness)
    *  ~ 다음의 숫자가 '몇 글자까지 다른 글자를 수용할 것인지`를 정하는 역할
        * 예를 들어 `22번가~2`는 두 글자까지 다름을 허용한다는 뜻이다.
    * 명시하지 않을 경우 AUTO 적용
        * 검색어가 2글자 이하 : 완전 일치
        * 검색어가 3~5글자 사이 : 한 글자 차이 허용
        * 검색어가 5글자 이상 : 두 글자 차이 허용

[[ images/search/fuzzy_keyword.png | height = 500px | width = 1024px]]
[[ images/search/fuzzy_field.png | height = 500px | width = 1024px]]

<a name='proximity'></a>
#### Proximity 검색

* 기능 : 검색어의 순서를 변경해서 Documents 찾기
* 예시
    * `"이상 상품"~2`
    * `배송메모:"고객님이 안 받아요 전화를 계속"~5`
* 예시 설명
    * 임의의 Field Value가 `이상 상품`에서 단어 순서를 2번까지 바꿔서 찾을 수 있는 Documents 찾기
    * `배송메모` Field Value 중에서 `고객님이 안 받아요 전화를 계속`의 단어들의 순서를 최대 5번까지 바꾸는 과정에서 찾을 수 있는 Documents 찾기
 
<a name='numeric_values'></a>
#### Numeric Values 검색

* 기능 : Numeric Field Value로 Documents 찾기
* 예시
    * `상품가격:5000`
    * `상품가격:>=5000`
    * `상품가격:<=5000`
* 예시 설명
    * `상품가격` Field Value가 `5000`인 Documents 찾기
    * `상품가격` Field Value가 `5000`보다 크거나 같은 Documents 찾기
    * `상품가격` Field Value가 `5000`보다 작거나 같읕 Documents 찾기

[[ images/search/numeric_values.png | height = 500px | width = 1024px]]

<a name='range'></a>
#### Range 검색

* 기능 : Field Value의 범위로 Documents 찾기
* 예시
    * `주문시간:[2017-10-01 TO 2017-12-31]`
    * `상품가격:[1000 TO 10000}`
    * `tag:{alpha TO omega}`
* 예시 설명
    * `주문시간` Field Value가 `2017-10-01 ~ 2017-12-31` 사이인 Documents 찾기
    * `상품가격` Field Value가 `1000 이상 & 10000 미만`인 Documents 찾기
    * `tag` Field Value가 `alpha ~ omega` 사이인 Documents 찾기

[[ images/search/range_date.png | height = 500px | width = 1024px]]
[[ images/search/range_number.png | height = 500px | width = 1024px]]

<a name='wildcard_?'></a>
#### Wildcard ? 검색

* 기능 : Wildcard `?`를 사용하여 Documents 찾기
* 예시
    * `서?특별시`
    * `구매사이트:쿠?`
* 예시 설명
    * 임의의 Field Value가 `서`로 시작하고 `특별시`로 끝나는 5글자인 모든 Documents 찾기 (서울특별시)
    * `구매사이트` Field Value가 `쿠`로 시작하는 2글자인 모든 Documents 찾기 (쿠팡)

[[ images/search/wildcard_keyword.png | height = 500px | width = 1024px]]

[[ images/search/wildcard_field.png | height = 500px | width = 1024px]]

<a name='wildcard_*'></a>
#### Wildcard * 검색

* 기능 : Wildcard `*`를 사용하여 Documents 찾기
* 예시
    * `전라*`
    * `상품분류:가*`
* 예시 설명
    * 임의의 Field Value가 `전라`로 시작하는 모든 Documents 찾기 (전라북도, 전라남도)
    * `상품분류` Field Value가 `가`로 시작하는 모든 Documents 찾기 (가디건)

[[ images/search/wildcard_*_keyword.png | height = 500px | width = 1024px]]

[[ images/search/wildcard_*_field.png | height = 500px | width = 1024px]]

---

### source

* [Kibana Tutorial](https://logz.io/blog/kibana-tutorial/)
* [Kibana Cheat Sheet](https://drive.google.com/file/d/0B2S_IOa0MiOHWndxWFRiUHNoNW8/view)
* [Elasticsearch/Kibana Queries](https://www.timroes.de/2016/05/29/elasticsearch-kibana-queries-in-depth-tutorial/)
* [Elastic Blog](https://www.elastic.co/blog/building-a-better-search-experience-in-kibana?blade=linkedin)
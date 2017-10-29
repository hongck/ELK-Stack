* web browser를 통해 kibana에 접속한다
    * local에서 사용하는 경우 : `http://localhost:5601`
    * cloud에서 사용하는 경우 : `http://{ip주소}:5601`
        * {}를 제거하고 ip를 입력한다.
        * 예) `http://12.345.6.789:5601`
* `Management`에서 `Index Patterns` 선택

    [[ images/management_index-patterns.png | height = 500px | width = 1024px]]
* 기존에 kibana에 어떤 index도 등록하지 않았을 경우 아래와 같은 화면이 나온다.
   
    [[ images/management_create-index-pattern.png | height = 500px | width = 1024px]]
* `Index pattern`에 
    * 특정 elasticsearch index만 보고 싶을 때 : 정확히 index 이름 입력
   
       [[ images/management_configure-an-index-pattern.png | height = 500px | width = 1024px]]
    * 복수개의 elasticsearch index를 보고 싶을 때 : wildcard를 적절히 이용해서 입력 (자세히는 [elastic 참조](https://www.elastic.co/guide/en/kibana/current/index-patterns.html))
  
       [[ images/management_configure-index-patterns.png| height = 500px | width = 1024px]]
* `Time Filter field name`
    * kibana는 기본적으로 시계열 데이터가 들어올 것이라고 생각한다.
 
       [[ images/management_time-filter.png | height = 500px | width = 1024px]]
    * 복수개의 시계열 field가 존재할 경우, 기준으로 삼을 field를 선택한다.
    
       [[ images/management_time-filters.png | height = 500px | width = 1024px]]
    * 만약에 시계열 field가 존재하지 않거나 선택하고 싶지 않으면 `I don't want to use the Time Filter`를 선택한다.
* 기타
    * 아래 두 옵션은 deprecated 되었으므로 사용하지 않을 것을 권장한다.
        * `Expand index pattern when searching [DEPRECATED]`
        * `Use event times to create index names [DEPRECATED]`
* 생성
    * `Create` 선택

---
#### 주의
* kibana에 index를 등록하는 작업은 elasticsearch index에 저장된 data를 불러오는 작업이다.
* 그러므로 이 작업을 하기 전에 kibana에서 사용하려는 elasticsearch index에 데이터가 저장되어 있어야 한다.

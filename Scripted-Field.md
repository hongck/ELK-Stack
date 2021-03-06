### 주의

* Scripted Field는 시계열 연산을 제공하지 않는다.
* 잘못 사용할 경우 심각한 성능 저하의 원인이 될 수 있다.
* `text` data type에도 사용 가능하나, 전체 term을 JVM heap에 로드해야 하기에 memory와 CPU를 많이 소비한다.

### 목록

* 사용법
    * [Management에서 Index Patterns선택](#management)
    * [Index 선택](#select_index)
    * [Scripted Fields 선택](#scripted_field)
    * [Add Scripted Field 선택](#add_scripted_field)
    * [양식에 맞게 설정 후 Create Field 선택](#create_field)
* 종류
    * [Numeric Field](#numeric)
    * [Date Field](#date)
    * [String Field](#string)
    * [Geo Point Field](#geo)
    * [Logic (조건)](#logic)

---

### 사용법

<a name='management'></a>
Management에서 Index Patterns 선택

[[ images/management/main.png | height = 500px | width = 1024px]]

<a name='select_index'></a>
Index 선택

[[ images/management/select_index.png | height = 500px | width = 1024px]]

<a name='scripted_field'></a>
Scripted Fields 선택

[[ images/management/scripted_field.png | height = 500px | width = 1024px]]

<a name='add_scripted_field'></a>
Add Scripted Field 선택

[[ images/management/add_scripted_field.png | height = 500px | width = 1024px]]

<a name='create_field'></a>
Create Field 선택

[[ images/management/scripted_field_create_field.png | height = 500px | width = 1024px]]

* Name : Scripted Field 이름 설정
* Language : Painless 또는 (Lucene) Expression 선택
* Type : number, date, string, boolean 중 선택
* Format : 선택한 Type이 제공하는 Format 중 선택
* Script
    * Scripted Field Script 작성
    * 예) `doc['상품가격'].value * 1.05`

---

<a name='numeric'></a>
#### Numeric Field

* 적용 가능한 Field : Number
* Return Type : Number
* 기본 Syntax : doc['Field 이름'].value 
* 사용 가능한 연산 Operator : +, -, *, /, % (나머지)
* 예시 
    * `doc['상품가격'].value + 10`, `doc['상품가격'].value + doc['상품개수'].value`
    * `doc['상품가격'].value - 10`, `doc['상품가격'].value - doc['상품개수'].value`
    * `doc['상품가격'].value * 10`, `doc['상품가격'].value * doc['상품개수'].value`
    * `doc['상품가격'].value / 10`, `doc['상품가격'].value / doc['상품개수'].value`
    * `doc['상품가격'].value % 10`, `doc['상품가격'].value % doc['상품개수'].value`

[[ images/management/scripted_field_numeric.png | height = 500px | width = 1024px]]

<a name='date'></a>
#### Date Field

* 적용 가능한 Field : Date
* Return Type : Number
* Language : expressions
* 기본 Syntax 
    * 편의상 `Field 이름`을 t라고 표시
    * `doc['Field 이름'].date.year`와 `doc['t'].date.year는 같다

| 선택할 날짜 단위  |  예시       |  설명 |  범위 |
| ------------- |------------| -----| ----- |
| year          |  `doc['t'].date.year` | 연도(year) 출력 | -292m - 292m |
| month         |  `doc['t'].date.monthOfYear` | 몇 번째 달인지 출력 | 1-12 |
| day         |  `doc['t'].date.dayOfYear` | (해당 년의) 몇 번째 날인지 출력  | 1-365 |
| day         |  `doc['t'].date.dayOfMonth` | (해당 월의) 몇 번째 날인지 출력  | 1-31 |
| day         |  `doc['t'].date.dayOfWeek` | (해당 주의) 몇 번째 날인지 출력  | 1-7 |
| hour         |  `doc['t'].date.hourOfDay` | (하루 중) 몇 시간째인지 출력  | 0-23 |
| minute         |  `doc['t'].date.minuteOfDay` | (하루 중) 몇 분째인지 출력  | 0-1439 |
| minute         |  `doc['t'].date.minuteOfHour` | (한 시간 중) 몇 분째인지 출력 | 0-59 |
| second         |  `doc['t'].date.secondOfDay` | (하루 중) 몇 초째인지 출력 | 0-86399 |

[[ images/management/scripted_field_date.png | height = 500px | width = 1024px]]

<a name='string'></a>
#### String Field

* 적용 가능한 Field : Number/String
* Return Type : String
* Language : painless
* 예시
    * 'Field 값은...' + doc['Field 이름'].value + '...입니다.'

[[ images/management/scripted_field_string.png | height = 500px | width = 1024px]]

<a name='geo'></a>
#### Geo Point Field

* 적용 가능한 Field : Geo Point
* Language : expressions
* 기본 Syntax
    * Geo Point의 위도 출력 : `doc['Field 이름'].lat`
    * Geo Point의 경도 출력 : `doc['Field 이름'].lon`
* 응용
    * 출발점~도착점 거리 계산 (km)
    * `haversin(doc['출발점'].lat, doc['출발점'].lon, doc['도착점'].lat, doc['도착점'].lon)` 

<a name='logic'></a>
### Logic 조건

* Language : Painless
* Syntax 예시

        if (doc['고객나이'].value < 20) { 
            return "10대" 
        } 
        if (doc['고객나이'].value < 30) {
            return "20대" 
        } 
        if (doc['고객나이'].value < 40) { 
            return "30대" 
        } 
        if (doc['고객나이'].value < 50) {
            return "40대" 
        } 
        return "50대 이상"

[[ images/management/scripted_field_logic.png | height = 500px | width = 1024px]]

---

### Source

* [elastic reference - Lucene Expressions Language](https://www.elastic.co/guide/en/elasticsearch/reference/5.0/modules-scripting-expression.html)
* [elastic blog - Using Painless in Kibana scripted fields](https://www.elastic.co/blog/using-painless-kibana-scripted-fields)
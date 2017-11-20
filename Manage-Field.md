### 목록

* String
    * [Url](#url)
    * String
        * [Lower Case](#lower)
        * [Upper Case](#upper)
        * [Title Case](#title)
* Number
    * [Bytes](#bytes)
    * [Percentage](#percentage)
    * [Number](#number)
    * [Duration](#duration)
* Date
    * [Date](#date)

---

### String

<a name='url'></a>
#### Url

url 형태로 formatting해서 kibana에서 url로 직접 링크연결을 가능하게 해준다

* Field Value가 full url 형태일 경우 `"full_url" : "http://higee.io/221094794994"`

    * Management 이동
    * Index Patterns 선택
    * Index 선택
    * url 형태의 Value가 담긴 Field의 `control` 부분 선택
    * Format을 `Url`로 변경
    * `Url Template` 부분에 `{{rawValue}}` 입력
    * Update Field 선택

[[ images/manage_field/full_url.gif | height = 500px | width = 1024px]]

* Field Value가 domain 뒤의 번호일 경우 `"index_url" : "221094794994"`

    * Management 이동
    * Index Patterns 선택
    * Index 선택
    * Url화 하려는 Value가 담긴 Field의 `control` 부분 선택
    * Format을 `Url`로 변경
    * `Url Template` 부분에 `http://higee.io/{{value}}` 입력
    * Update Field 선택

[[ images/manage_field/index_url.gif | height = 500px | width = 1024px]]

<a name='lower'></a>
#### String - Lower Case

특정 String Field의 모든 대문자를 소문자로 변형한다

* Management 이동
* Index Patterns 선택
* Index 선택
* Lower 적용하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `String`로 변경
* Transform을 `Lower Case` 선택
* Update Field 선택

[[ images/manage_field/lower.gif | height = 500px | width = 1024px]]

<a name='upper'></a>
#### String - Upper Case

특정 String Field의 모든 소문자를 대문자로 변형한다

* Management 이동
* Index Patterns 선택
* Index 선택
* Upper 적용하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `String`로 변경
* Transform을 `Upper Case` 선택
* Update Field 선택

[[ images/manage_field/upper.gif | height = 500px | width = 1024px]]

<a name='title'></a>
#### String - Title Case

특정 String Field의 Value를 띄어쓰기 단위로 대문자로 표시한다.

* Management 이동
* Index Patterns 선택
* Index 선택
* Title 적용하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `String`로 변경
* Transform을 `Title Case` 선택
* Update Field 선택

[[ images/manage_field/upper.gif | height = 500px | width = 1024px]]

### Number

<a name='bytes'></a>
#### Bytes

Number Value를 Bytes 형태로 표시

* Management 이동
* Index Patterns 선택
* Index 선택
* Bytes로 표시하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `Bytes`로 변경
* 원하는 Numeral.js format pattern 형식 입력, default `0,0.[000]b`
* Update Field 선택

[[ images/manage_field/bytes.gif | height = 500px | width = 1024px]]

<a name='percentage'></a>
#### Percentage

Number Value를 Percentage(%) 형태로 표시

* Management 이동
* Index Patterns 선택
* Index 선택
* Percentage로 표시하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `Bytes`로 변경
* 원하는 Numeral.js format pattern 형식 입력, default `0,0.[000]%`
* Update Field 선택

[[ images/manage_field/percentage.gif | height = 500px | width = 1024px]]

<a name='number'></a>
#### Number

Number Value를 Percentage(%) 형태로 표시

* Management 이동
* Index Patterns 선택
* Index 선택
* Number로 표시하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `Number`로 변경
* 원하는 Numeral.js format pattern 형식 입력, default `0,0.[000]`
* Update Field 선택

[[ images/manage_field/number.gif | height = 500px | width = 1024px]]

<a name='duration'></a>
#### Duration

Number Value를 Duration 형태로 표시

* Management 이동
* Index Patterns 선택
* Index 선택
* Duration 형태로 표시하려는 Value가 담긴 Field의 `control` 부분 선택
* Format을 `Duration`로 변경
* Input Format과 Output Format을 원하는 형태로 설정
* Update Field 선택

[[ images/manage_field/duration.gif | height = 500px | width = 1024px]]

### Date

* Date Field를 원하는 형태로 변경해서 표시 (`2017년10월11일`, `17-10-11`, `11th, Oct 2017`)

    * Management 이동
    * Index Patterns 선택
    * Index 선택
    * Date Field의 `control` 부분 선택
    * Format을 `Date`로 변경
    * moment.js format pattern를 원하는 설저에 맞게 설정
    * Update Field 선택

[[ images/manage_field/date.gif | height = 500px | width = 1024px]]

* 주요 옵션

| 날짜 단위 | 문법 | 예시 | 설명 |
| --- | --- | ---- | ---- |
| Year | YYYY | 2014 | 4자리 표시 |
| Year | YY | 14 | 2자리 표시 |
| Month | M | 1 | 1~2자리 표시 |
| Month | MM | 01 | 2자리 표시 |
| Day | D | 1 | 1~2자리 표시 |
| Day | DD | 01 | 2자리 표시 |
| Day | Do | 1st | 며칠째인지 표시 |
| Hour | H | 1 | 1자리 표시 (24시)|
| Hour | HH | 01 | 1~2자리 표시 (24시)|
| Hour | h | 1 | 1자리 표시 (12시) |
| Hour | hh | 01 | 1~2자리 표시 (12시)|
| a | h | am/pm | 소문자 표시 |
| A | hh | AM/PM | 대문자 표시 |
| Minute | m | 1 | 1자리 표시  |
| Minute | mm | 01 | 1~2자리 표시 |
| Second | s | 1 | 1자리 표시  |
| Second | ss | 01 | 1~2자리 표시 |
| Second | X | 1410715640.579 | Unix Timestamp 초 |
| Millisecond | x | 1410715640579 | Unix Timestam 밀리초 |

* 위의 옵션을 원하는 조건에 맞게 조합해서 `moment.js format pattern`에 입력한다

[[ images/manage_field/date1.png | height = 500px | width = 1024px]]
[[ images/manage_field/date2.png | height = 500px | width = 1024px]]
[[ images/manage_field/date3.png | height = 500px | width = 1024px]]

### Source

* [momentjs](http://momentjs.com/docs/#/displaying/format/)
* [numeraljs](http://numeraljs.com/)
### 주의

* 데이터 조회를 위한 Filter 설정을 위한 페이지입니다.
* Dashboard에서 Filter를 적용하여 원하는 데이터를 보기 위한 과정을 다룹니다.

### 목록

* 필터 설정
    * [Dashboard 이동](#page)
    * [Field 선택](#field)
    * [Operator 선택 및 Value 입력](#operator)
    * [Label 입력](#label)
* 필터 적용
    * [필터 적용 전](#before)
    * [필터 적용 후](#after)
* 필터 관리
    * [Enable Filter](#enable)
    * [Pin Filter](#pin)
    * [Invert Filter](#invert)
    * [Remove Filter](#remove)
    * [Edit filter](#edit)

---

### 필터 설정

<a name='page'></a>
#### Dashboard 이동

[[ images/filter/main_dashboard.png | height = 500px | width = 1024px]]

* Dashboard 이동
* `Add a filter` 선택

<a name='field'></a>
#### Field 선택

[[ images/filter/field.png | height = 500px | width = 1024px]]

* Filter를 적용하려는 특정 Field를 선택
* 예를 들어, `구매사이트`가 `11번가`인 데이터만 보고 싶다고 할 때, `구매사이트`를 선택하면 된다 

<a name='operator'></a>
#### Operator 선택 및 Value 입력

* 적용하려는 operator 선택
* 옵션
    * `is`
        * 기능 : 선택한 Field가 특정 Value를 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is` `11번가`
        * 예시 설명 : `구매사이트`가 `11번가`인 Documents만 필터 
        * [[ images/filter/operator_is.png | height = 500px | width = 1024px]]
    * `is not`
        * 기능 : 선택한 Field가 특정 Value 이외의 Value를 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is not` `11번가`
        * 예시 설명 : `구매사이트`가 `11번가`가 아닌 Documents만 필터 
        * [[ images/filter/operator_is_not.png | height = 500px | width = 1024px]]
    * `is one of`
        * 기능 : 선택한 Field가 특정 Values 중에 적어도 하나의 값을 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is one of` `11번가` `옥션` `쿠팡`
        * 예시 설명 : `구매사이트`가 `11번가`, `옥션`, `쿠팡` 중 하나라도 해당하는 Documents만 필터 
        * [[ images/filter/operator_is_one_of.png | height = 500px | width = 1024px]]
    * `is not one of`
        * 기능 : 선택한 Field가 특정 Values 이외의 값을 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is not one of` `11번가` `옥션` `쿠팡`
        * 예시 설명 : `구매사이트`가 `11번가`, `옥션`, `쿠팡` 중 어느 하나에도 해당하지 않는 Documents만 필터 
        * [[ images/filter/operator_is_not_one_of.png | height = 500px | width = 1024px]]
    * `exists`
        * 기능 : 선택한 Field가 존재하는 Documents만 필터한다
        * 예시 : `구매사이트` `exists`
        * 예시 설명 : `구매사이트`가 존재하는 Documents만 필터
        * [[ images/filter/operator_exists.png | height = 500px | width = 1024px]]
    * `does not exist`
        * 기능 : 선택한 Field가 존재하지 않는 Documents만 필터한다
        * 예시 : `구매사이트` `does not exist`
        * 예시 설명 : `구매사이트`가 존재하지 않는 Documents만 필터
        * [[ images/filter/operator_does_not_exist.png | height = 500px | width = 1024px]]
    * `is between`
        * 기능 : 선택한 Field가 특정 Value 범위 안의 값을 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is between` `1` `5`
        * [[ images/filter/operator_is_between.png | height = 500px | width = 1024px]]
    * `is not between`
        * 기능 : 선택한 Field가 특정 Value 범위 밖의 값을 갖는 Documents만 필터한다
        * 예시 : `구매사이트` `is not between` `1` `5`
        * [[ images/filter/operator_is_not_between.png | height = 500px | width = 1024px]]
* Value 입력

<a name='label'></a>
#### Label 입력

[[ images/filter/label.png | height = 500px | width = 1024px]]

필터 가독성을 위해 알아보기 쉬운 Label을 짧게 입력한다

---

### 필터적용

<a name='before'></a>
#### 필터 적용 전

[[ images/filter/before.png | height = 500px | width = 1024px]]

<a name='after'></a>
#### 필터 적용 후

[[ images/filter/after.png | height = 500px | width = 1024px]]

---

### 필터관리

박스 내 왼쪽에서부터 `Enable Filter`, `Pin Filter`, `Invert Filter`, `Remove Filter`, `Edit Filter`다.

[[ images/filter/filter.png | height = 500px | width = 1024px]]

<a name='enable'></a>
#### Enable Filter

* 설정한 Filter를 적용할 지 말 지 선택
* 적용
    * [[ images/filter/enable_filter.png | height = 500px | width = 1024px]]
* 적용하지 않음
    * [[ images/filter/disable_filter.png | height = 500px | width = 1024px]]

<a name='pin'></a>
#### Pin Filter

* 설정한 Filter를 Pin하여 Discover에서 Visualize 또는 Dashboard로 이동해도 필터 효과 지속
* 적용
    * [[ images/filter/enable_pin.png | height = 500px | width = 1024px]]
* 적용하지 않음
    * [[ images/filter/disable_pin.png | height = 500px | width = 1024px]]

<a name='invert'></a>
#### Invert Filter

* 설정한 Filter를 그대로 적용할 지 역으로 적용할 지 선택 
* 그대로
    * [[ images/filter/positive.png | height = 500px | width = 1024px]]
* 역으로 (예를 들어 `is`의 역은 `is not`이 된다)
    * [[ images/filter/negative.png | height = 500px | width = 1024px]]

<a name='remove'></a>
#### Remove Filter

필터를 제거한다

<a name='edit'></a>
#### Edit Filter

필터를 수정한다
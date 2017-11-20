### 목록

* Aggregation 공통 parameter
    * [Missing](#missing)
    * [Script](#script)
* Aggregation 별 parameter
    * Terms Aggregation
        * [Min_doc_count](#min_doc)
    * Seiral Diff Aggregation
        * [Lag](#lag)
    * Moving Average Aggregation
        * [Window](#window)

---

### Aggregation 공통 parameter

<a name='missing'></a>
#### Missing : Missing Data를 특정값으로 대체

* 아래와 같은 데이터가 있다고 하자.

[[ images/json_input/missing/missing_data.png | height = 500px | width = 1024px]]

* 이 때 7시와 8시에는 데이터가 없어서 누적합에도 변화가 없다.
* 이처럼 missing data가 생겼을 시에 사용할 수 있는 parameter가 missing이다
* 우선 아래 그림처럼, `Advance`를 선택해서 `JSON Input`을 활성화시켜보자

[[ images/json_input/missing/activate_json_input.gif | height = 500px | width = 1024px]]

* 그리고 아래와 같은 코드를 입력해보자. 이 경우 missing data를 100으로 대체하겠다는 것이다.

`{"missing":100}`

* 결과를 보자

[[ images/json_input/missing/missing_100.png | height = 500px | width = 1024px]]

<a name='script'></a>
#### Script : Aggregation 내부에 script evaluation을 사용

* 아래와 같은 데이터가 있다고 하자

[[ images/json_input/missing/sample_data.png | height = 500px | width = 1024px]]

* 이 때 스크립트를 사용해서 `개별 매출의 2배의 합`을 구한다고 해보자
   
    * 방법1 (elasticsearch에서는 변수를 hard coding 하는 형태를 권장하지 않는다)
    ```
   {
     "script" : {
       "source": "_value * 2"
     }
   }
   ```
    * 방법2 
   ```
   {
     "script" : {
       "source" : "_value * params.x",
       "params" : {
           "x" : 2
       }
     }
   } 
   ```
    * 방법3 
   ```
      {
     "script" : {
       "source" : "doc['상품가격'].value * params.x",
       "params" : {
           "x" : 2
       }
     }
   } 
   ```
* 결과는 아래와 같다

[[ images/json_input/missing/script_evaluation.png | height = 500px | width = 1024px]]

---

### Aggregation 별 parameter

<a name='terms'></a>
#### Terms Histogram

* min_doc_count : bucket 내 최소 documents 개수 설정하여 그 이하의 documents를 갖는 bucket은 제외

[[ images/json_input/missing/script_evaluation.png | height = 500px | width = 1024px]]

<a name='lag'></a>
#### Serial Diff Histogram

* lag : 몇 개 전 bucket과의 차이를 구할 것인지 설정

    * 먼저 serial diff는 기본적으로 바로 전 bucket과의 차이를 구하므로 derivative aggregation과 같다

    [[ images/json_input/missing/default_serial_diff.png | height = 500px | width = 1024px]]

    * 이 때, 2개 전 bucket과의 차이를 구할 경우 `lag`를 사용할 수 있다 : `{'lag' : 2}`

    [[ images/json_input/missing/lag_serial_diff.png | height = 500px | width = 1024px]]

<a name='window'></a>
#### Moving Average Histogram

* window : 몇 개의 bucket을 가지고 평균을 구할지 설정

    * default로는 window=5로 설정되어 있다

    [[ images/json_input/missing/default_moving_average.png | height = 500px | width = 1024px]]

    * 이 때, window=2로 설정하면 2개 bucket 마다 평균을 구할 수 있다 : `{'window':2}`

    [[ images/json_input/missing/window_moving_average.png | height = 500px | width = 1024px]]


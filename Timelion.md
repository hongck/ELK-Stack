### 주의
* 그래프를 여러개 생성하고 싶으면 ,로 구분해라 `.es(), es()`
* `@timestamp` 외의 `timefield`를 사용할 시 명시하지 않으면 아무것도 안 나타난다. `.es(timefield=newdate)`
* 기본함수 이외에는 (일반적으로) 기본함수와 chain해서 사용한다. ~~`.abs()`~~, `es.().abs()`
* `.label()`에 띄어쓰기가 있을 땐 ' '로 감싸줘야 한다. `.es().lable('hello world')`
* chain을 잘 사용하는 것이 point! 대부분의 경우 chain, chain해서 사용한다 `es().sum(10).scale_interval(1h).label('chain..')`

---

### 목록

* [기본함수 - `.es()`](#es)
* [조건함수 - `.es().if()`](#if)
* [수학함수 - `.es().multiply()`](#mul)
* [수학함수 - `.es().divide()`](#div)
* [수학함수 - `.es().subtract()`](#sub)
* [수학함수 - `.es().sum()`](#sum)
* [수학함수 - `.es().abs()`](#abs)
* [수학함수 - `.es().log()`](#log)
* [수학함수 - `.es().max()`](#max)
* [수학함수 - `.es().min()`](#min)
* [수학함수 - `.es().static()`](#static)
* [수학함수 - `.es().cusum()`](#cusum)
* [수학함수 - `.es().derivative()`](#derivative)
* [수학함수 - `.es().movingaverage()`](#mvavg)
* [수학함수 - `.es().scale_interval()`](#si)
* [수학함수 - `.es().range()`](#rg)
* [수학함수 - `.es().trend()`](#trend) 
* [스타일함수 - `.es().bars()`](#bar)
* [스타일함수 - `.es().lines()`](#ln)
* [스타일함수 - `.es().points()`](#poi)
* [스타일함수 - `.es().label()`](#lb)
* [스타일함수 - `.es().color()`](#cl)
* [스타일함수 - `.es().yaxis()`](#yaxis)
* [스타일함수 - `.es().title()`](#title)

### 예제

* [전년 대비 매출이 50,000 이상 상승 구간 하이라이트](#ex1)
* [매출 7일 이동평균선 대비 14일 이동평균선이 1보다 큰 구간 하이라이트](#ex2)
* [평균 매출 추세선, 매출 5일 이동평균선, 평균 매출을 한 번에 그리기](#ex3)
---
### 기본함수
#### `.es()` <a name="es"></a>

모든 index의 모든 document의 개수를 y축으로 갖고, `@timestamp`를 x축으로 갖는 그래프가 생성된다

| argument|  설명  | 예시| 의미 |
| ------- |-------|----|---|
| index  | index 선택 | `.es(index=shopping)` | `shopping`라는 index 사용 |
| q      | document 선택 [(문법)](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax) | `.es(q=고객성별:여성)`| `고객성별이`이 여성인 document 사용
| timefield | timefield 선택 (timelion x축) | `.es(timefield=주문시간)` | `주문시간` field를 tiemfield(x축)으로 사용 |
| metric | aggregation 선택 (timelion y축)  | `.es(metric=sum:상품가격)`| `price` field의 sum 값을 y축에 표시 |
| offset | 설정한 시간만큼 전의 값을 표시 <br /> (형식: 1h, 1d, 1w, 1M, 1y)  | `.es(offset=-1M)`| 한 달 전 값을 표시|
| split | .es()를 조건에 맞게 분할  | `.es(split=상품분류:3)`| 기존 .es()를 `상품분류`을 기준으로 3개로 분할|

[[ images/visualize/timelion/es.png | height = 500px | width = 1024px]]

---
### 조건함수
#### `.if()` <a name="if"></a>

.es()의 각 point를 설정한 조건과 비교하여 참(거짓)일 경우 설정한 값을 반환한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| operator| eq, ne, gt(e), lt(e) | `.es().if(gt, 5, 10, 0)` <br/> `.es.().if(lt, .es(q=고객성별:여성), 1, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정  <br/> .es() 값이 .es(q=고객성별:여성)보다 작으면 1, 크면 0으로 설정 |
| if | 비교할 값 | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정 |
| then | 조건이 참일 경우 표시할 값  | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정|
| else | 조건이 거짓일 경우 표시할 값  | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정 |

[[ images/visualize/timelion/if.png | height = 500px | width = 1024px]]

---
### 수학함수
#### `.es().multiply()` <a name="mul"></a>

.es() 값에 상수/그래프(값)을 곱한다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| multiplier	| 곱할 값 | `.es().multiply(10)` <br /> `.es().multiply(.es())` | .es() * 10 <br /> .es() * .es()

[[ images/visualize/timelion/multiply.png | height = 500px | width = 1024px]]

#### `.es().divide()`<a name="div"></a>

.es() 값에 상수/그래프(값)으로 나눈다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| divisor	| 나눌 값 | `.es().divide(10)` <br /> `.es().divide(.es())` | .es() / 10 <br /> .es() / .es()

[[ images/visualize/timelion/divide.png | height = 500px | width = 1024px]]

#### `.es().subtract()`<a name="sub"></a>

.es() 값에 상수/그래프(값)으로 뺀다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| term	| 뺄 값| `.es().subtrat(10)` <br /> `.es().subtract(.es())` | .es() - 10 <br /> .es() - .es()

[[ images/visualize/timelion/subtract.png | height = 500px | width = 1024px]]

#### `.es().sum()`<a name="sum"></a>

.es() 값에 상수/그래프(값)를 더한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| term	| 더할 값| `.es().sum(10)` <br /> `.es().sum(.es())` | .es() + 10 <br /> .es() + .es()

[[ images/visualize/timelion/sum.png | height = 500px | width = 1024px]]

#### `.es().abs()`<a name="abs"></a>

.es() 값에 절대값을 취한다

[[ images/visualize/timelion/abs.png | height = 500px | width = 1024px]]

#### `.es().log()`<a name="log"></a>

.es() 값에 로그를 취한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| base	| 밑| `.es().log(10)` | .es() 각 point에 밑이 10인 로그를 취한다

[[ images/visualize/timelion/log.png | height = 500px | width = 1024px]]

#### `.es().max()`<a name="max"></a>

두 값/그래프(값) 중 큰 값을 반환한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| value	| 비교값 | `.es().max(10)` <br /> `.es().max(.es(q=s:a))` | .es()와 10 중 큰 값 표시 <br /> .es()와 .es(q=s:a) 중 큰 값 표시

[[ images/visualize/timelion/max.png | height = 500px | width = 1024px]]

#### `.es().min()`<a name="min"></a>

두 값/그래프(값) 중 작은 값을 반환한다

| argument      |  설명     |  예시 | 의미 |
| ------------- |----------| -----| ----- |
| value	| 비교값 | `.es().min(10)` <br /> `.es().min(.es(q=s:a))` | .es()와 10 중 작은 값 표시 <br /> .es()와 .es(q=s:a) 중 작은 값 표시

[[ images/visualize/timelion/min.png | height = 500px | width = 1024px]]

#### `.static()`<a name="static"></a>

일정한 값을 갖는 상수함수를 생성한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| value	| 표시할 값 | `.static(10)` | y=10인 상수함수 생성

[[ images/visualize/timelion/static.png | height = 500px | width = 1024px]]

#### `.es().cusum()`<a name="cusum"></a>

.es()의 누적합을 구한다

[[ images/visualize/timelion/cusum.png | height = 500px | width = 1024px]]

#### `.es().derivative()`<a name="derivative"></a>

.es()와 .es()의 이전 값과의 차를 구한다

[[ images/visualize/timelion/derivative.png | height = 500px | width = 1024px]]

#### `.es().movingaverage()`<a name="mvavg"></a>

.es()의 이동평균값을 구한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| window | 구간 | `.es().movingaverage(10)` | .es()의 10개 point 마다 평균 계산
| position | 위치 (left, right, center)| `.es().movingaverage(left, 10)` | 특정 point 기준 왼쪽 10개씩 평균 계산

[[ images/visualize/timelion/moving_average.png | height = 500px | width = 1024px]]

#### `.es().scale_interval()`<a name="si"></a>

.es()의 interval을 조정한다

| argument      |  설명     |  예시 | 의미 |
| ------------- |----------| -----| ----- |
| interval | 조정하려는 시간 단위 <br />(1s, 1m, 1h, 1M, 1w, 1y) | `.es().scale_interval(1h)` | .es()의 값을 1h 기준으로 조정해서 표시 <br /> 예) default가 daily라면 24로 나눈 값이 표시

[[ images/visualize/timelion/scale_interval.png | height = 500px | width = 1024px]]

#### `.es().range()`<a name="rg"></a>

.es()의 값을 특정 범위 내의 값으로 조정한다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| min | 최소값 | `.es().range(1, 10)` | .es()의 값을 1~10 사이 값으로 조정
| max | 최대값 | `.es().range(1, 10)` | .es()의 값을 1~10 사이 값으로 조정

[[ images/visualize/timelion/range.png | height = 500px | width = 1024px]]

#### `.es().trend()`<a name="trend"></a>

.es()의 추세선을 그려준다.

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| mode | 어떤 방식으로 추세선 그릴 지 서택 `linear(선형)`, `log` | `.es().trend(mode=linear)` | .es()의 선형 추세선을 그린다
| start | 추세선 작성을 위해 사용할 데이터의 시작점 정의 | `.es().trend(start=5)` <br> `.es().trend(start=-5)`| 처음 15번째 데이터부터 반영 <br> 뒤에서 15번째 데이터부터 반영
| end | 추세선 작성을 위해 사용할 데이터의 끝점 정의 | `.es().trend(end=5)` <br> `.es().trend(end=-5)`| 처음 5번째 데이터까지 반영 <br> 뒤에서 5번째 데이터까지 반영

[[ images/visualize/timelion/trend.png | height = 500px | width = 1024px]]

---

### 스타일 함수
#### `.es().bars()`<a name="bar"></a>

.es()를 막대 그래프로 나타낸다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| width | 굵기 | `.es().bars(5)` | .es()를 막대 그래프로 나타내고 굵기는 5로 설정

[[ images/visualize/timelion/bars.png | height = 500px | width = 1024px]]

#### `.es().lines()`<a name="ln"></a>

.es()를 라인 그래프로 나타낸다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| width | 굵기 | `.es().lines(width=5)` | .es()를 라인 그래프로 나타내고 굵기는 5로 설정
| fill | 농도 | `.es().lines(fill=5)` | .es()의 라인 아래 면적을 농도 5만큼 채우기 (0: 투명, 10: 불투명 ) 

[[ images/visualize/timelion/lines.png | height = 500px | width = 1024px]]

#### `.es().points()`<a name="poi"></a>

.es()를 점 그래프로 나타낸다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| radius | 반경 | `.es().points(radius=5)` | .es()의 점 반경을 5로 설정
| weight | 굵기 | `.es().points(weight=5)` | .es()의 점 원주 굵기를 5로 설정
| fill | 농도 | `.es().points(fill=5)` | .es()의 점의 농도를 5로 설정
| fillColor | 내부 색깔 | `.es().points(fillColor=red)` | .es()의 점 색깔을 red로 설정
| symbol | 모양 (cross, circle, triangle, square, diamond) | `.es().points(symbol=cross)` | .es()의 점 모양을 cross로 설정

[[ images/visualize/timelion/points.png | height = 500px | width = 1024px]]

#### `.es().label()`<a name="lb"></a>

legend에 각 .es()를 나타낼 이름을 정한다

[[ images/visualize/timelion/label.png | height = 500px | width = 1024px]]

#### `.es().color()`<a name="cl"></a>

.es()를 나타낼 색깔을 정한다

[[ images/visualize/timelion/color.png | height = 500px | width = 1024px]]

#### `.es().yaxis()`<a name="yaxis"></a>

.es()의 y축 정보를 설정한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| yaxis | n 번째 y축 | `.es().yaxis(yaxis=2)` | 2번째 y축에 표시
| min | 최소값 | `.es().yaxis(min=1)` | y축의 최소값을 1로 설정
| max | 최소값 | `.es().yaxis(max=10)` | y축의 최대값을 10으로 설정
| label | 축 이름| `.es().yaxis(label=hello)` | y축의 이름을 hello로 설정
| color | 축 색깔| `.es().yaxis(label=hello, color=red)` | y축의 이름을 hello로 하고 빨간색으로 표시
| position | 위치 (left, right) | `.es().yaxis(position)` | y축을 왼쪽에 표시

[[ images/visualize/timelion/yaxis.png | height = 500px | width = 1024px]]

#### `.es().title()`<a name="title"></a>

.es() 제목을 설정한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| title | 제목 | `.es().title(title='hello world')` | `title`에 hello world 출력

[[ images/visualize/timelion/title.png | height = 500px | width = 1024px]]

---

### 예제

<a name='ex1'></a>
#### 전년 대비 매출이 50,000 이상 상승 구간 하이라이트

[[ images/visualize/timelion/ex1.png | height = 500px | width = 1024px]]

```
.es(metric=sum:상품가격).label('올해 매출 (=상품가격의 합)'),  
.es(metric=sum:상품가격, offset=-1y).label('작년 매출').color(#00b8ff), 
.es(metric=sum:상품가격).subtract(.es(metric=sum:상품가격, offset=-1y)).if(gte, 50000, .es(metric=sum:상품가격), null).lines(fill=5, width=2).color(#fd8282).label('전년 대비 50,000 이상 상승 구간').yaxis(label=매출)
```


<a name='ex2'></a>
#### 매출 7일 이동평균선 대비 14일 이동평균선이 1보다 큰 구간 하이라이트

[[ images/visualize/timelion/ex2.png | height = 500px | width = 1024px]]

```
.es(metric=sum:상품가격).movingaverage(7).divide(.es(metric=sum:상품가격).movingaverage(14)).label('매출 7일 이동평균선 대비 14일 이동평균선').color('#00ccff'),  
.es(metric=sum:상품가격).movingaverage(7).divide(.es(metric=sum:상품가격).movingaverage(14)).if(gte, 1.0, .es(metric=sum:상품가격).movingaverage(7).divide(.es(metric=sum:상품가격).movingaverage(14)), null).lines(fill=3).label('1.0보다 큰 경우').color('#fb8cb5')
```

<a name='ex3'></a>
#### 평균 매출 추세선, 평균 매출, 매출 5일 이동평균선 한 번에 그리기

[[ images/visualize/timelion/ex3.png | height = 500px | width = 1024px]]

```
.es(metric=avg:상품가격).trend().label('평균 매출 추세선').color(#7EC327), 
.es(metric=avg:상품가격).label('평균 매출').points().color(#cf5297), 
.es(metric=avg:상품가격).movingaverage(5).label('매출 5일 이동평균').color(#5297cf)
```


### source
* [timelion github](https://github.com/elastic/timelion/blob/master/FUNCTIONS.md)
* [timelion youtube](https://www.youtube.com/watch?v=-sgZdW5k7eQ)
* [timelion elastic blog](https://www.elastic.co/blog/timeseries-if-then-else-with-timelion)
* [timelion tutorial - timroes](https://www.timroes.de/2017/08/02/timelion-tutorial-from-zero-to-hero/)

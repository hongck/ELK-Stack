### 주의
* 그래프를 여러개 생성하고 싶으면 ,로 구분해라 `.es(), es()`
* `@timestamp` 외의 `timefield`를 사용할 시 명시하지 않으면 아무것도 안 나타난다. `.es(timefield=newdate)`
* 기본함수 이외에는 (일반적으로) 기본함수와 chain해서 사용한다. ~~`.abs()`~~, `es.().abs()`
* `.label()`에 띄어쓰기가 있을 땐 ' '로 감싸줘야 한다. `.es().lable('hello world')`
* chain을 잘 사용하는 것이 point! 대부분의 경우 chain, chain해서 사용한다 `es().sum(10).scale_interval(1h).label('chain..')`

---
### 기본함수
#### `.es()`

모든 index의 모든 document의 개수를 y축으로 갖고, `@timestamp`를 x축으로 갖는 그래프가 생성된다

| argument|  설명  | 예시| 의미 |
| ------- |-------|----|---|
| index  | index 선택 | `.es(index=hello)` | `hello`라는 index 사용 |
| q      | document 선택 [(문법)](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax) | `.es(q=name:higee)`| `name`이 higee인 document 사용
| timefield | timefield 선택 (timelion x축) | `.es(timefield=newdate)` | `newdate` field를 tiemfield(x축)으로 사용 |
| metric | aggregation 선택 (timelion y축)  | `.es(metric=sum:price)`| `price` field의 sum 값을 y축에 표시 |
| offset | 설정한 시간만큼 전의 값을 표시 <br /> (형식: 1h, 1d, 1w, 1M, 1y)  | `.es(offset=-1M)`| 한 달 전 값을 표시|
| split | .es()를 조건에 맞게 분할  | `.es(split=name:3)`| 기존 .es()를 `name`을 기준으로 3개로 분할|

[[ images/timelion_es.png | height = 500px | width = 1024px]]

---
### 조건함수
#### `.es().if()`

.es()의 각 point를 설정한 조건과 비교하여 참(거짓)일 경우 설정한 값을 반환한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| operator| eq, ne, gt(e), lt(e) | `.es().if(gt, 5, 10, 0)` <br/> `.es.().if(eq, .es(q=s:a), 1, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정  <br/> .es() 값이 .es(q=s:a)과 같으면 1, 다르면 0으로 설정 |
| if | 비교할 값 | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정 |
| then | 조건이 참일 경우 표시할 값  | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정|
| else | 조건이 거짓일 경우 표시할 값  | `.es().if(gt, 5, 10, 0)` | .es() 값이 5보다 크면 10으로, 작으면 0으로 설정 |

[[ images/timelion_if.png | height = 500px | width = 1024px]]

---
### 수학함수
#### `.es().multiply()`

.es() 값에 상수/그래프(값)을 곱한다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| multiplier	| 곱할 값 | `.es().multiply(10)` <br /> `.es().multiply(.es())` | .es() * 10 <br /> .es() * .es()

[[ images/timelion_multiply.png | height = 500px | width = 1024px]]

#### `.es().divide()`

.es() 값에 상수/그래프(값)으로 나눈다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| divisor	| 나눌 값 | `.es().divide(10)` <br /> `.es().divide(.es())` | .es() / 10 <br /> .es() / .es()

[[ images/timelion_divide.png | height = 500px | width = 1024px]]

#### `.es().subtract()`

.es() 값에 상수/그래프(값)으로 뺀다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| term	| 뺄 값| `.es().subtrat(10)` <br /> `.es().subtract(.es())` | .es() - 10 <br /> .es() - .es()

[[ images/timelion_subtract.png | height = 500px | width = 1024px]]

#### `.es().sum()`

.es() 값에 상수/그래프(값)를 더한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| term	| 더할 값| `.es().sum(10)` <br /> `.es().sum(.es())` | .es() + 10 <br /> .es() + .es()

[[ images/timelion_sum.png | height = 500px | width = 1024px]]

#### `.es().abs()`

.es() 값에 절대값을 취한다

[[ images/timelion_abs.png | height = 500px | width = 1024px]]

#### `.es().log()`

.es() 값에 로그를 취한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| base	| 밑| `.es().log(10)` | .es() 각 point에 밑이 10인 로그를 취한다

[[ images/timelion_log.png | height = 500px | width = 1024px]]

#### `.es().max()`

두 값/그래프(값) 중 큰 값을 반환한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| value	| 비교값 | `.es().max(10)` <br /> `.es().max(.es(q=s:a))` | .es()와 10 중 큰 값 표시 <br /> .es()와 .es(q=s:a) 중 큰 값 표시

[[ images/timelion_max.png | height = 500px | width = 1024px]]

#### `.es().min()`

두 값/그래프(값) 중 작은 값을 반환한다

| argument      |  설명     |  예시 | 의미 |
| ------------- |----------| -----| ----- |
| value	| 비교값 | `.es().min(10)` <br /> `.es().min(.es(q=s:a))` | .es()와 10 중 작은 값 표시 <br /> .es()와 .es(q=s:a) 중 작은 값 표시

[[ images/timelion_min.png | height = 500px | width = 1024px]]

#### `.static()`

일정한 값을 갖는 상수함수를 생성한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| value	| 표시할 값 | `.static(10)` | y=10인 상수함수 생성

[[ images/timelion_static.png | height = 500px | width = 1024px]]

#### `.es().cusum()`

.es()의 누적합을 구한다

[[ images/timelion_cusum.png | height = 500px | width = 1024px]]

#### `.es().derivative()`

.es()와 .es()의 이전 값과의 차를 구한다

[[ images/timelion_derivative.png | height = 500px | width = 1024px]]

#### `.es().movingaverage()`

.es()의 이동평균값을 구한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| window | 구간 | `.es().movingaverage(10)` | .es()의 10개 point 마다 평균 계산
| position | 위치 (left, right, center)| `.es().movingaverage(left, 10)` | 특정 point 기준 왼쪽 10개씩 평균 계산

[[ images/timelion_movingaverage.png | height = 500px | width = 1024px]]

#### `.es().scale_interval()`

.es()의 interval을 조정한다

| argument      |  설명     |  예시 | 의미 |
| ------------- |----------| -----| ----- |
| interval | 조정하려는 시간 단위 <br />(1s, 1m, 1h, 1M, 1w, 1y) | `.es().scale_interval(1h)` | .es()의 값을 1h 기준으로 조정해서 표시 <br /> 예) default가 daily라면 24로 나눈 값이 표시

[[ images/timelion_scale_interval.png | height = 500px | width = 1024px]]

#### `.es().range()`

.es()의 값을 특정 범위 내의 값으로 조정한다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| min | 최소값 | `.es().range(1, 10)` | .es()의 값을 1~10 사이 값으로 조정
| max | 최대값 | `.es().range(1, 10)` | .es()의 값을 1~10 사이 값으로 조정

[[ images/timelion_range.png | height = 500px | width = 1024px]]

---

### 스타일 함수
#### `.es().bars()`

.es()를 막대 그래프로 나타낸다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| width | 굵기 | `.es().bars(5)` | .es()를 막대 그래프로 나타내고 굵기는 5로 설정

[[ images/timelion_bars.png | height = 500px | width = 1024px]]

#### `.es().lines()`

.es()를 라인 그래프로 나타낸다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| width | 굵기 | `.es().lines(width=5)` | .es()를 라인 그래프로 나타내고 굵기는 5로 설정
| fill | 농도 | `.es().lines(fill=5)` | .es()의 라인 아래 면적을 농도 5만큼 채우기 (0: 투명, 10: 불투명 ) 

[[ images/timelion_lines.png | height = 500px | width = 1024px]]

#### `.es().points()`

.es()를 점 그래프로 나타낸다

| argument      |  설명       |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| radius | 반경 | `.es().points(radius=5)` | .es()의 점 반경을 5로 설정
| weight | 굵기 | `.es().points(weight=5)` | .es()의 점 원주 굵기를 5로 설정
| fill | 농도 | `.es().points(fill=5)` | .es()의 점의 농도를 5로 설정
| fillColor | 내부 색깔 | `.es().points(fillColor=red)` | .es()의 점 색깔을 red로 설정
| symbol | 모양 (cross, circle, triangle, square, diamond) | `.es().points(symbol=cross)` | .es()의 점 모양을 cross로 설정

[[ images/timelion_points.png | height = 500px | width = 1024px]]

#### `.es.label()`

legend에 각 .es()를 나타낼 이름을 정한다

[[ images/timelion_label.png | height = 500px | width = 1024px]]

#### `.es().color()`

.es()를 나타낼 색깔을 정한다

[[ images/timelion_color.png | height = 500px | width = 1024px]]

#### `.es().yaxis()`

.es()의 y축 정보를 설정한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| yaxis | n 번째 y축 | `.es().yaxis(yaxis=2)` | 2번째 y축에 표시
| min | 최소값 | `.es().yaxis(min=1)` | y축의 최소값을 1로 설정
| max | 최소값 | `.es().yaxis(max=10)` | y축의 최대값을 10으로 설정
| label | 축 이름| `.es().yaxis(label=hello)` | y축의 이름을 hello로 설정
| color | 축 색깔| `.es().yaxis(label=hello, color=red)` | y축의 이름을 hello로 하고 빨간색으로 표시
| position | 위치 (left, right) | `.es().yaxis(position)` | y축을 왼쪽에 표시

[[ images/timelion_yaxis.png | height = 500px | width = 1024px]]

#### `.es().title()`

.es() 제목을 설정한다

| argument      |  설명          |  예시 | 의미 |
| ------------- |------------| -----| ----- |
| title | 제목 | `.es().title(title='hello world')` | `title`에 hello world 출력

[[ images/timelion_title.png | height = 500px | width = 1024px]]

### source
* [timelion](https://github.com/elastic/timelion/blob/master/FUNCTIONS.md)
* [timelion youtube](https://www.youtube.com/watch?v=-sgZdW5k7eQ)
* [tim roes](https://www.timroes.de/2017/08/02/timelion-tutorial-from-zero-to-hero/)
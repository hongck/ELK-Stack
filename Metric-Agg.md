### 개요

* (Bucket 등으로) Aggregate된 값들에 대해 특정 연산을 수행한다.
* 오직 Number Field에 대해서만 사용 가능하다.
* Extended Stats Aggregation 및 Percentiles Aggregation 등은 multi-value를 반환한다.

### 주의
* 백분위수 (Percentile) : 특정 위치에 어느 값이 있는지 궁금할 떄
* 백분위 (Percentile Rank) : 특정 값이 어느 정도에 위치하는지 궁금할 때
* 예) 80 백분위가 90점이면, 90점을 받은 사람의 백분위수는 80이다. [참고](https://ko.wikipedia.org/wiki/%EB%B0%B1%EB%B6%84%EC%9C%84%EC%88%98)

### 종류

* Value Count Aggregation <a name="vc"></a>
* Avg Aggregation <a name="avg"></a>
* Sum Aggregation <a name="sum"></a>
* Min Aggregation <a name="min"></a>
* Max Aggregation <a name="max"></a>
* Extended Stats Aggregation <a name="es"></a>
* Cardinality Aggregation <a name="cd"></a>
* Percentiles Aggregation <a name="pa"></a>
* Percentile Ranks Aggregation <a name="pr"></a>
* Top Hits Aggregation <a name="th"></a>

---

`Value Count Aggregation` <a name="vc"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| (aggregate된) documents 개수 반환 |  |

[[ images/metric_agg_count.png | height = 500px | width = 1024px]]

`Avg Aggregation` <a name="avg"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field 값의 평균반환 | Field : Avg Agg 적용할 Field 선택 |

[[ images/metric_agg_avg.gif | height = 500px | width = 1024px]]

`Sum Aggregation` <a name="sum"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field 값의 총합 반환 | Field : Sum Agg 적용할 Field 선택  |

[[ images/metric_agg_sum.gif | height = 500px | width = 1024px]]

`Min Aggregation` <a name="min"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field의 최소값 반환 | Field : Min Agg 적용할 Field 선택 |

[[ images/metric_agg_min.gif | height = 500px | width = 1024px]]

`Max Aggregation` <a name="max"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field의 최대값 반환 | Field : Max Agg 적용할 Field 선택 |

[[ images/metric_agg_max.gif | height = 500px | width = 1024px]]

`Extended Stats Aggregation` <a name="es"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field의 각종 통계 자료 반환 | Field : Extended Agg 적용할 Field 선택 |

[[ images/metric_agg_std.gif | height = 500px | width = 1024px]]

`Cardinality Aggregation` <a name="cd"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 해당 Field의 고유한 값의 개수 반환 | Field : Cardinality Agg 적용할 Field 선택  |

[[ images/metric_agg_cardinality.gif | height = 500px | width = 1024px]]

`Percentiles Aggregation` <a name="pa"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 백분위를 입력하면 백분위수 반환 | Field : Percentiles Agg을 적용할 Field 선택 <br /> Percents : 백분위 값|

[[ images/metric_agg_percentiles.gif | height = 500px | width = 1024px]]

`Percentile Ranks Aggregation` <a name="pr"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 백분위수를 입력하면 백분위 반환 | Field : Percentile Ranks Agg을 적용할 Field 선택 <br /> Values : 백분위수 |

[[ images/metric_agg_percentile_ranks.gif | height = 500px | width = 1024px]]

`Top Hits Aggregation` <a name="th"></a>

| 역할  | 주요 옵션 | 
|-------------------|----------|
| 조건을 만족하는 Document의 Field의 Agg 반환 | Field : Top Hits Agg 적용할 Field 선택 <br /> Aggregate With : Value들을 어떻게 Aggregate 할 지 결정 <br /> Size : Value 몇 개를 가지고 올 지 결정 <br /> Sort On : 정렬할 기준 <br /> Order : 오름/내림차순 |

[[ images/metric_agg_top_hit_1.gif | height = 500px | width = 1024px]]
[[ images/metric_agg_top_hit_2.gif | height = 500px | width = 1024px]]
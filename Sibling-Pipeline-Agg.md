### 개요

* 실제로 어떤 과정으로 계산이 이루어지는지의 과정은 [여기](https://github.com/higee/elastic/blob/master/Week1_Kibana/aggregation/sibling_pipeline_aggregation.ipynb)를 참고해주세요.
* 기본적으로 이미 (parent) aggregation된 결과물에 대해 aggregate 수행 후 하나의 값을 반환한다
* sibling aggr와 metric agg을 같은 걸 사용할 경우, sibling agg 의미가 없다.
    * 예: sum bucket agg(sibling pipeline)과 sum agg(metric agg)을 같이 쓸 경우 그냥 전체 값을 더하는 것과 같다.

### 목록

* [Average Bucket Aggregation](#avg)
* [Sum Bucket Aggregation](#sum)
* [Min Bucket Aggregation](#min)
* [Max Bucket Aggregation](#max)

---

#### Average Bucket Aggregation <a name="avg"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 평균 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/aggregation/sibling/avg.gif | height = 500px | width = 1024px]]

#### Sum Bucket Aggregation <a name="sum"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 합 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/aggregation/sibling/sum.gif | height = 500px | width = 1024px]]

#### Min Bucket Aggregation <a name="min"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 최소값 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/aggregation/sibling/min.gif | height = 500px | width = 1024px]]

#### Max Bucket Aggregation <a name="max"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 최대값 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택


[[ images/aggregation/sibling/max.gif | height = 500px | width = 1024px]]
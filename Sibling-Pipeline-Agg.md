### 개요

* 기본적으로 이미 (parent) aggregation된 결과물에 대해 aggregate 수행
* 결과적으로 하나의 값 반환

### 주의

### 목록

* [Average Bucket Aggregation](#avg)
* [Sum Bucket Aggregation](#sum)
* [Min Bucket Aggregation](#min)
* [Max Bucket](#max)

---

#### Average Bucket Aggregation <a name="avg"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 평균 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/sb_agg_avg.gif | height = 500px | width = 1024px]]

#### Sum Bucket Aggregation <a name="sum"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 합 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/sb_agg_sum.gif | height = 500px | width = 1024px]]

#### Min Bucket Aggregation <a name="min"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 최소값 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택

[[ images/sb_agg_min.gif | height = 500px | width = 1024px]]

#### Max Bucket Aggregation <a name="max"></a>

* 역할 : Bucket별 Metric Aggregation 적용 후 (= bucket 별 대표값 1개 생성 후) 그 값들의 최대값 반환
* 주요 옵션
    * Bucket
        * Aggregation : 적용할 Agg 선택
    * Metric
        * Aggregation : 적용할 Agg 선택


[[ images/sb_agg_max.gif | height = 500px | width = 1024px]]
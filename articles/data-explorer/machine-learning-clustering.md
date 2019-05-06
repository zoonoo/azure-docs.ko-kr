---
title: Azure 데이터 탐색기의 기능을 학습 하는 컴퓨터
description: Azure 데이터 탐색기에서 근본 원인 분석에 대 한 클러스터링 하는 machine learning을 사용 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 2358cb2ea411a0077f34798183da30bd32ae067b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925120"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Azure 데이터 탐색기의 기능을 학습 하는 컴퓨터

빅 데이터 분석 플랫폼을 azure 데이터 탐색기는 서비스 상태, QoS, 또는 기본 제공을 사용 하 여 비정상적인 동작에 대 한 제대로 작동 하지 않는 장치를 모니터링 데 [변칙 검색 및 예측](/azure/data-explorer/anomaly-detection) 함수입니다. 비정상적인 패턴 검색 되 면 하 게 완화 하거나 해결 변칙을 RCA 근본 원인 분석 ()이 수행 됩니다.

진단 프로세스가 복잡 하 고 시간이 오래 걸리는 도메인 전문가 의해 수행 됩니다. 프로세스 포함 인출 및 차트 추가 변수를 여러 차원에서 값의 분포에 대 한 변경 내용을 찾고 동일한 시간 프레임에 대 한 다양 한 원본에서 추가 데이터를 조인 하 고 도메인 정보를 기반으로 다른 기술 및 개발자입니다. 이러한 진단 시나리오는 Azure 데이터 탐색기에서 일반적으로, machine learning 플러그 인 진단 단계를 쉽게는 RCA 기간을 단축 하 사용할 수 있습니다.

Azure 데이터 탐색기는 세 가지 Machine Learning 플러그 인: [ `autocluster` ](/azure/kusto/query/autoclusterplugin)합니다 [ `basket` ](/azure/kusto/query/basketplugin), 및 [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin)합니다. 클러스터링 알고리즘을 구현 하는 모든 플러그 인입니다. 합니다 `autocluster` 하 고 `basket` 플러그 인 클러스터는 단일 레코드 집합 및 `diffpatterns` 플러그 인 두 레코드 집합 간의 차이 클러스터 합니다.

## <a name="clustering-a-single-record-set"></a>단일 레코드 집합을 클러스터링

일반적인 시나리오에는 비정상적인 동작, 최고 기온 장치 판독값, 긴 기간 명령 및 상위 사용자가 지출 한도 보여 주는 시간 창과 같은 특정 기준에 따라 선택한 데이터 집합을 포함 합니다. 데이터의 일반적인 패턴 (세그먼트)를 찾을 수는 쉽고 빠른 방법을 싶습니다. 패턴은 레코드가 여러 차원 (범주 열)을 통해 동일한 값을 공유 데이터 집합의 하위 집합입니다. 다음 쿼리는 작성 하 고 10 분 bin의 한 주 동안 시간 일련의 서비스 예외를 보여 줍니다.

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![서비스 예외 시간 차트](media/machine-learning-clustering/service-exceptions-timechart.png)

전체를 사용 하 여 서비스 예외 수가 상호 연결 트래픽을 서비스 합니다. 월요일부터 금요일 일 이내에 대 한 일일 패턴을 확인할 수 있습니다, 서비스의 증가 사용 하 여 예외 중간 일 수를 계산 하 고 개수 밤 중에 삭제 합니다. 주말 플랫 낮은 수가 표시 됩니다. 사용 하 여 예외가 급증을 검색할 수 있습니다 [시계열 변칙 검색 시간](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) Azure 데이터 탐색기에서.

데이터의 두 번째 스파이크 화요일 오후에 발생합니다. 다음 쿼리를이 급증을 세부적으로 진단 사용 됩니다. 쿼리를 사용 하 여 날카로운 급증을 확인 하기 위해 더 높은 해상도 (1 분 bin에서 8 시간) 높아지는 관련 차트를 다시 그려야 하 고 해당 테두리를 봅니다.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![급증 시간 차트에 집중](media/machine-learning-clustering/focus-spike-timechart.png)

15시 02분을 좁은 2 분 급증 15:00에서 볼 수 있습니다. 다음 쿼리에서이 분 창에서 예외를 계산 합니다.

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|카운트 |
|---------|
|972    |

다음 쿼리에서 샘플 972에서 20 예외:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | 지역 | ScaleUnit | DeploymentId                     | 추적점 | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>사용 하 여 `autocluster()` 클러스터링을 설정 하는 단일 레코드에 대 한

천 미만 예외 인 경우에 여전히 하기 어렵습니다 일반적인 세그먼트를 찾거나 각 열에 여러 값이 있습니다. 사용할 수 있습니다 [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) 다음 쿼리에서와에서 같이 플러그 인을 즉시 몇몇 일반적인 세그먼트를 추출 하 고는 흥미로운 찾을 상용구의 2 분 내 클러스터:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | 카운트 | 백분율 | 지역 | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

가장 많이 사용 하는지 세그먼트 65.74% 총 예외 레코드를 포함 하 고 공유 하는 4 차원 위의 결과에서 볼 수 있습니다. 다음 세그먼트는 훨씬 덜 일반적인 레코드 9.67%만 있고 3 차원을 공유 합니다. 다른 세그먼트는 덜 일반적입니다. 

Autocluster는 여러 차원이 마이닝 및 흥미로운 세그먼트를 추출 소유 알고리즘을 사용 합니다. "흥미로운" 크게 확장 기능 집합 및 레코드 집합의 각 세그먼트에 하는 것을 의미 합니다. 세그먼트도 달라졌는지를 각각 다른 크게 다르다는 것을 의미 합니다. 이러한 세그먼트 중 하나 이상이 RCA 프로세스에 대 한 관련 수 있습니다. 세그먼트 검토 및 평가 최소화 하려면 autocluster 작은 세그먼트 목록만 추출 합니다.

### <a name="use-basket-for-single-record-set-clustering"></a>사용 하 여 `basket()` 클러스터링을 설정 하는 단일 레코드에 대 한

사용할 수도 있습니다는 [ `basket()` ](/azure/kusto/query/basketplugin) 다음 쿼리에서와에서 같이 플러그 인:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | 카운트 | 백분율 | 지역 | ScaleUnit | DeploymentId | 추적점 | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

바구니는 마이닝을 설정 하 고 모든 세그먼트가 임계값 (기본값 5%) 보다는 레코드 집합의 해당 검사를 추출 하는 항목에 대 한 Apriori 알고리즘을 구현 합니다. 비슷한 루틴 (예 세그먼트 0, 1 또는 2, 3)를 사용 하 여 자세한 세그먼트를 추출 하는 것을 볼 수 있습니다.

플러그 인은 강력 하 고 사용 하기 쉬운 이지만 해당 중요 한 제한 사항 때문에 단일 레코드 집합 (레이블 없음)와 감독 되지 않은 방식으로 클러스터는 있습니다. 명확 하지 않습니다 따라서 추출된 패턴 특징 선택된 된 레코드 집합 (비정상적인 레코드) 또는 전역 레코드 집합을 결정 하는 여부.

## <a name="clustering-the-difference-between-two-records-sets"></a>두 레코드 집합 간의 차이 클러스터링합니다.

합니다 [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) 플러그 인에 대 한 제한 극복 `autocluster` 고 `basket`입니다. `Diffpatterns` 두 레코드 집합을 사용 하 고 서로 다른 기본 세그먼트를 추출 합니다. 하나의 집합에는 일반적으로 비정상적인 레코드 집합 조사 중에 (하나 사용 하 여 분석할 `autocluster` 고 `basket`). 다른 집합 참조 레코드 집합 (기준)를 포함합니다. 

아래 쿼리에서 사용 하 여 `diffpatterns` 흥미로운 클러스터 기준 내에 있는 클러스터를 다르게 상용구의 2 분 내에서 찾을 수 있습니다. 15:00 전에 8 분으로 기준 창 정의 (때 급증 시작)입니다. 또한 특정 레코드를 기준으로 또는 비정상적인 집합에 속하는지 여부를 지정 하는 이진 열 (AB)로 확장 해야 합니다. `Diffpatterns` 두 클래스 레이블을 기준 플래그 (AB) 및 비정상적인에 의해 생성 된 위치 감독된 학습 알고리즘을 구현 합니다.

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | 지역 | ScaleUnit | DeploymentId | 추적점 |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

가장 많이 사용 하는지 세그먼트는를 통해 추출 되었으면 하는 동일한 세그먼트 `autocluster`, 2 분 비정상적인 창에서 해당 검사 65.74% 이기도 합니다. 하지만 8 분 기준 창에서 해당 검사는 1.7%만 합니다. 차이점은 64.04%입니다. 이러한 차이 비정상적인 급증와 관련이 있을 것 같습니다. 이 가정은 아래 쿼리 에서처럼 다른 세그먼트와 문제가이 세그먼트에 속하는 레코드를 원래 차트를 분할 하 여 확인할 수 있습니다.

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![유효성 검사 'diffpattern' 세그먼트 시간 차트](media/machine-learning-clustering/validating-diffpattern-timechart.png)

이 차트를이 특정 세그먼트를 사용 하 여 검색에서 예외 때문에 화요일 오후에 급증 했음을 볼 수 있습니다는 `diffpatterns` 플러그 인입니다.

## <a name="summary"></a>요약

Azure 데이터 탐색기 Machine Learning 플러그 인은 대부분의 시나리오에 유용 합니다. 합니다 `autocluster` 고 `basket` 사용 하기 쉬운 되며 자율된 학습 알고리즘을 구현 합니다. `Diffpatterns` 구현 감독 학습 알고리즘 이지만 더 복잡 한 것이 더 강력한 RCA에 대 한 차별화 세그먼트를 추출 합니다.

이러한 플러그 인-임시 시나리오에 거의 실시간 모니터링 서비스는 자동 대화형으로 사용 됩니다. 시계열 변칙 검색 Azure 데이터 탐색기에서 필요한 성능 표준에 맞게 고도로 최적화 되는 진단 프로세스를 다음 표시 됩니다.

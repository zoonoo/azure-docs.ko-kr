---
title: "Azure 사용 가이드에서 변칙 검색(미리 보기) | Microsoft Docs"
description: "Stream Analytics 및 Machine Learning을 사용하여 변칙을 검색합니다."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: 43a2a9784668fad2aa5b1441cfd37751c0c240b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-anomalydetection-operator"></a>ANOMALYDETECTION 연산자 사용

> [!IMPORTANT]
> 이 기능은 미리 보기 상태입니다. 프로덕션 환경에서는 사용하지 않는 것이 좋습니다.

**ANOMALYDETECTION** 연산자는 이벤트 스트림에서 변칙을 검색하는 데 사용할 수 있습니다.
예를 들어 장시간에 걸쳐 사용 가능한 메모리가 천천히 감소하는 것은 메모리 누수를 암시할 수 있으며, 일정 범위에서 안정적인 상태를 유지하던 웹 서비스 요청 수가 갑자기 증가하거나 늘어날 수 있습니다.

지정된 기간에 대해 다음과 같은 유형의 변칙이 확인됩니다.

- 양방향 수준 변화
- 느린 긍정 추세
- 느린 부정 추세

현재 이벤트에 대해 얼마나 오래된 이전 기록을 확인해야 하는지에 대한 시간 간격은 **LIMIT DURATION** 절을 사용하여 제한합니다. **ANOMALYDETECTION**은 경우에 따라 **WHEN** 절을 사용하여 특정 속성 또는 조건과 일치하는 이벤트로만 제한할 수 있습니다.

또한 **PARTITION BY** 절에 지정된 키를 기준으로 이벤트 그룹을 따로 처리할 수도 있습니다. 학습 및 예측은 각 파티션에서 독립적으로 발생합니다.

## <a name="syntax"></a>구문

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>사용 예

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>인수

- **scalar_expression**

  변칙 검색을 수행할 스칼라 식입니다. 단일(스칼라) 값을 반환하는 float 또는 bigint 형식의 식입니다. 와일드 카드 식 **\***는 허용되지 않습니다. **scalar_expression** 은 다른 분석 함수 또는 외부 함수를 포함할 수 없습니다.

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  `PARTITION BY \<partition key\>` 절은 학습 및 훈련을 별도 파티션으로 나눕니다. 즉, `\<partition key\>` 값별로 별도 모델을 사용하고, 해당 값을 갖는 이벤트만 해당 모델의 학습 및 훈련에 사용합니다. 예를 들면 다음과 같습니다.

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  같은 센서의 다른 판독값을 기준으로 판독값을 학습하고 점수를 매깁니다.

- **limit_duration clause** DURATION(\<unit\>, \<length\>)

  **ANOMALYDETECTION** 계산에서 고려되는 현재 이벤트의 기록 양을 지정합니다. 지원되는 단위와 해당 약어에 대한 자세한 설명은 DATEDIFF를 참조하세요.

- **when_clause** 

  **ANOMALYDETECTION** 계산에서 고려되는 이벤트에 대한 부울 조건을 지정합니다.

## <a name="return-types"></a>반환 형식

이 함수는 3가지 점수 모두를 포함하는 레코드를 출력으로 반환합니다. 다양한 형식의 변칙 검색과 연결된 속성이 호출됩니다.

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

레코드에서 개별 값을 추출하려면 **GetRecordPropertyValue** 함수를 사용합니다. 예:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


이러한 변칙 점수 중 하나가 임계값을 초과할 때 특정 형식의 변칙이 검색됩니다. 임계값은 \>= 0의 임의 부동 소수점 숫자일 수 있습니다. 임계값은 민감도가 높아지면 신뢰도가 떨어지고 민감도가 낮아지면 신뢰도가 높아집니다. 예를 들어 더 낮은 임계값을 사용하면 검색이 변경에 더 민감해지고 더 많은 경고를 생성하지만, 더 높은 임계값을 사용하면 검색이 변경에 덜 민감해지고 신뢰도는 높아지지만 일부 변칙은 검색하지 못합니다. 사용할 정확한 임계값은 시나리오에 따라 달라집니다. 상한은 없으나 권장되는 범위는 3.25-5입니다.

## <a name="algorithm"></a>알고리즘

**ANOMALYDETECTION**은 슬라이딩 윈도우 의미 체계를 사용합니다. 즉, 계산은 함수를 시작하는 이벤트에 따라 실행되고 해당 이벤트에 대해 점수가 생성됩니다. 계산은 이벤트 값의 분포가 변경되었는지 확인하는 방식으로 작동하는 Exchangeability Martingales를 기준으로 합니다. 이러한 경우 잠재적인 변칙이 검색된 것입니다. 반환된 점수는 해당 변칙의 신뢰 수준을 나타냅니다. 내부 최적화로서, **ANOMALYDETECTION**은 이벤트의 *2d*에 대한 *d* 값을 기준으로 이벤트의 비정상 점수를 계산합니다. 여기서 *d*는 지정된 검색 기간 크기입니다.

**ANOMALYDETECTION**은 입력 시계열이 균일할 것으로 예상합니다. 연속 또는 도약 기간에 대해 집계를 수행하여 이벤트 스트림을 균일하게 만들 수 있습니다. 이벤트 간 간격이 항상 집계 기간보다 작은 시나리오에서는 연속 기간만으로 충분히 시계열을 균일하게 만들 수 있습니다. 간격은 더 커질 수 있는 경우 도약 기간을 사용하여 마지막 값을 반복함으로써 간격을 채울 수 있습니다. 이러한 두 시나리오는 다음에 나오는 예제를 통해 처리할 수 있습니다. 현재 `FillInMissingValuesStep` 단계는 건너뛸 수 없습니다. 이 단계를 생략하면 컴파일 오류가 발생합니다.

## <a name="performance-guidance"></a>성능 지침

- 작업에 대한 6 SU를 사용합니다. 
- 1초 이상 간격으로 이벤트를 전송합니다.
- **ANOMALYDETECTION** 함수를 사용하는 분할되지 않은 쿼리는 평균적으로 약 25ms의 계산 대기 시간으로 결과를 생성할 수 있습니다.
- 분할된 쿼리에서 발생하는 대기 시간은 파티션 수에 따라 약간 달라집니다. 계산 수가 더 높아지기 때문입니다. 그러나 대기 시간은 모든 파티션에서 총 이벤트 수가 비슷할 경우 분할되지 않은 경우와 거의 같습니다.
- 실시간이 아닌 데이터를 읽는 동안 많은 양의 데이터가 빠르게 수집됩니다. 이 데이터의 처리는 현재 훨씬 더 느립니다. 이러한 시나리오의 대기 시간은 기간 크기 또는 이벤트 간격 자체보다 기간 내의 데이터 요소 수에 비례해서 증가하는 것으로 확인되었습니다. 실시간 이외의 경우에서 대기 시간을 줄이기 위해서는 좀 더 작은 기간 크기를 사용하는 것이 좋습니다. 또는 현재 시간에서 작업을 시작하는 것이 좋습니다. 분할되지 않은 쿼리의 일부 대기 시간 예제: 
    - 검색 기간에 데이터 요소가 60개 있으면 3MBps의 처리량으로 10초가 지연될 수 있습니다. 
    - 600개의 데이터 요소에서는 0.4MBps의 처리량으로 약 80초가 지연될 수 있습니다.

## <a name="example"></a>예제

다음 쿼리는 변칙이 검색될 경우 경고를 출력하는 데 사용할 수 있습니다.
입력 스트림이 균일하지 않은 경우 집계 단계를 통해 균일한 시계열로 변환할 수 있습니다. 이 예제에서는 **AVG**를 사용하지만 구체적인 집계 형식은 사용자 시나리오에 따라 다릅니다. 또한 시계열에 집계 기간보다 큰 간격이 있으면 시계열이 변칙 검색을 트리거하는 이벤트가 없게 됩니다(슬라이딩 윈도우 의미 체계에 따름). 결과적으로 다음 이벤트가 도착할 때 균일할 것이라는 가정이 중단됩니다. 이러한 경우에는 시계열의 간격을 채울 방법이 필요합니다. 가능한 한 가지 방법은 아래와 같이 모든 도약 기간에 마지막 이벤트를 가져오는 것입니다.

앞에서 설명한 것처럼 지금은 `FillInMissingValuesStep` 단계를 건너뛰지 않도록 합니다. 이 단계를 생략하면 컴파일 오류가 발생합니다.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>참조

* [변칙 검색 - Machine Learning을 사용하여 시계열 데이터에서 변칙 검색](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning 이상 감지 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [시계열 변칙 검색](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


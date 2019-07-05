---
title: Azure Stream Analytics의 변칙 검색
description: 이 아티클에서는 Azure Stream Analytics 및 Azure Machine Learning을 함께 사용하여 변칙을 검색하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 706311e2895f311c228b55db971eb88a859530f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441675"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics의 변칙 검색

클라우드와 Azure IoT Edge 둘 다에서 사용할 수 있는 Azure Stream Analytics는 가장 일반적으로 발생하는 두 가지 변칙(임시 및 영구적)을 모니터링하는 데 사용할 수 있는 기계 학습 기반의 기본 제공 변칙 검색 기능을 제공합니다. **AnomalyDetection_SpikeAndDip** 및 **AnomalyDetection_ChangePoint** 함수를 사용하여 Stream Analytics 작업에서 직접 변칙 검색을 수행할 수 있습니다.

기계 학습 모델은 균일하게 샘플링된 시계열을 가정합니다. 시계열이 균일하지 않으면 변칙 검색을 호출하기 전에 연속 창을 사용하여 집계 단계를 삽입할 수 있습니다.

Machine learning 작업 지금은 계절성 추세 또는 다중 variate 상관 관계를 지원 하지 않습니다.

## <a name="model-behavior"></a>모델의 동작

일반적으로 모델 정확도는 슬라이딩 윈도우의 데이터가 많을수록 향상됩니다. 지정된 슬라이딩 윈도우의 데이터는 해당 시간 프레임 동안 정상 범위의 일부로 처리됩니다. 모델은 슬라이딩 윈도우의 이벤트 기록만 고려하여 현재 이벤트가 비정상인지 확인합니다. 슬라이딩 윈도우가 이동하면 이전 값이 모델 학습에서 제거됩니다.

함수는 지금까지 확인한 내용을 기반으로 해서 특정 표준을 설정하여 작동합니다. 신뢰 수준 내에서 설정된 표준과 비교하여 이상값을 식별합니다. 변칙이 발생할 경우 인식할 수 있도록 정상 동작에 대한 모델 학습에 필요한 최소 이벤트 수를 기준으로 윈도우 크기를 설정해야 합니다.

지난 이벤트의 더 큰 값을 비교 하기 때문에 기록 크기를 사용 하 여 모델의 응답 시간이 증가 합니다. 성능 향상을 위해 필요한 개수의 이벤트만 포함하는 것이 좋습니다.

시계열의 간격은 모델이 특정 시점에 이벤트를 수신하지 않아서 발생하는 결과일 수 있습니다. 이 경우 대체 논리를 사용 하 여 Stream Analytics에 의해 처리 됩니다. 동일한 슬라이딩 윈도우의 기간뿐만 아니라 기록 크기는 이벤트가 도착하는 예상 평균 속도를 계산하는 데 사용됩니다.

변칙 생성기를 사용할 수 있습니다 [여기](https://aka.ms/asaanomalygenerator) 다른 비정상 패턴을 사용 하 여 데이터를 사용 하 여 Iot Hub를 피드로 사용할 수 있습니다. 이 Iot Hub에서 읽고 변칙을 검색 하기 위해 이러한 변칙 검색 함수를 사용 하 여 ASA 작업을 설정할 수 있습니다.

## <a name="spike-and-dip"></a>급증 및 급감

시계열 이벤트 스트림의 일시적인 변칙을 급증 및 급감이라고 합니다. 급증 및 급감은 기계 학습 기반 연산자인 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)을 사용하여 모니터할 수 있습니다.

![급증 및 급감 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

동일한 슬라이딩 윈도우에서 두 번째 급증이 첫 번째 급증보다 작을 경우 더 작은 급증에 대해 계산된 점수가 지정된 신뢰 수준 내에서 첫 번째 급증의 점수와 비교하여 유의미하지 않을 수 있습니다. 이러한 변칙을 검색 하는 모델의 신뢰도 줄이면 시도할 수 있습니다. 그러나 너무 많은 경고를 받기 시작하면 더 높은 신뢰 구간을 사용할 수 있습니다.

다음 예제 쿼리는 2분 슬라이딩 윈도우에서 초당 1개 이벤트의 균일한 입력 속도와 120개 이벤트의 기록을 가정합니다. 최종 SELECT 문은 신뢰 수준 95%로 점수 및 변칙 상태를 추출하고 출력합니다.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>변경 지점

시계열 이벤트 스트림의 영구적 변칙은 수준 변경 및 추세와 같은 이벤트 스트림의 값 분산 변경입니다. Stream Analytics에서 이러한 변칙은 Machine Learning 기반의 [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 연산자를 사용하여 검색됩니다.

영구적 변경은 급증 및 급감보다 훨씬 오래 지속되며 치명적인 이벤트를 나타낼 수 있습니다. 일반적으로 영구적 변경은 표시되지 않지만 **AnomalyDetection_ChangePoint** 연산자를 사용하여 검색할 수 있습니다.

다음 이미지는 수준 변경의 예입니다.

![수준 변경 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

다음 이미지는 추세 변경의 예입니다.

![추세 변경 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

다음 예제 쿼리는 20분 슬라이딩 윈도우에서 초당 1개 이벤트의 균일한 입력 속도와 1200개 이벤트의 기록 크기를 가정합니다. 최종 SELECT 문은 신뢰 수준 80%로 점수 및 변칙 상태를 추출하고 출력합니다.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>성능 특징

이러한 모델의 성능을 기록 크기, 기간, 이벤트 부하에 따라 달라 집니다 함수 수준 분할 사용 여부. 이 섹션에서는 이러한 구성에 설명 하 고 1k 5k와 초당 10 개 이벤트의 수집 속도 유지 하는 방법에 대 한 샘플을 제공 합니다.

* **기록 크기** -이러한 모델을 사용 하 여 선형으로 수행 **기록 크기**합니다. 오래 기록 크기, 더 긴 모델 점수를 새 이벤트를 수행 합니다. 각 기록 버퍼의 지난 이벤트를 사용 하 여 새 이벤트를 비교 하는 모델 때문입니다.
* **기간** - **기간** 기록 크기에 따라 지정 된 대로 많은 이벤트를 수신 하는 데 걸리는 시간을 반영 해야 합니다. 창에서 많은 이벤트 없이 Azure Stream Analytics는 누락 값을 대체 합니다. 따라서 CPU 사용 기록 크기의 함수입니다.
* **이벤트 부하** 큼-합니다 **이벤트 부하**더 작업을 수행 됩니다 모델에서 CPU 사용량에 영향을 줍니다. 병렬 더 입력된 파티션을 사용 하려면 비즈니스 논리에 적합 한 것으로 가정 하 여 작업을 스케일 아웃할 수 있습니다.
* **함수 수준 분할** - **함수 수준 분할** 사용 하 여 ```PARTITION BY``` 변칙 검색 함수 호출 내에서. 분할이 유형의 상태를 한 번에 여러 모델에 대 한 유지 해야 하는 대로 오버 헤드를 추가 합니다. 함수 수준 분할 장치 수준 분할 등의 시나리오에서 사용 됩니다.

### <a name="relationship"></a>관계
기록 크기, 기간, 및 총 이벤트 로드에는 다음과 같은 방법으로 관련이 있습니다.

windowDuration (in ms) = 1000 * historySize / (Total Input Events Per Sec / Input Partition Count)

함수에서 deviceId로 분할할 때 "분할 deviceId 하 여" 변칙 검색 함수 호출에 추가 합니다.

### <a name="observations"></a>관찰
다음 테이블은 분할 되지 않은 경우에 대 한 단일 노드 (6 개 SU)에 대 한 처리량 관찰을 포함 합니다.

| 기록 크기 (이벤트) | 기간 (ms) | 총 입력된 이벤트 수 / 초 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

다음 표에서 분할 된 경우에 대 한 단일 노드 (6 개 SU)에 대 한 처리량 관찰 합니다.

| 기록 크기 (이벤트) | 기간 (ms) | 총 입력된 이벤트 수 / 초 | 장치 수 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

위의 분할 되지 않은 구성을 실행 하는 샘플 코드에는 [확장에서 스트리밍 리포지토리](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) Azure 샘플입니다. 코드는 입력 및 출력으로 이벤트 허브를 사용 하는 분할 안 함 함수 수준에서 사용 하 여 stream analytics 작업을 만듭니다. 입력된 부하 테스트 클라이언트를 사용 하 여 생성 됩니다. 각 입력된 이벤트는 1KB json 문서입니다. 이벤트는 최대 1 K 장치에 JSON 데이터를 보내는 IoT 장치를 시뮬레이션 합니다. 입력된 파티션 2 개를 통해 기록 크기, 기간, 및 총 이벤트 로드 변화 합니다.

> [!Note]
> 더 정확한 예상을 시나리오에 맞게 샘플을 사용자 지정 합니다.

### <a name="identifying-bottlenecks"></a>병목 상태 식별
창을 사용 하 여 메트릭을 Azure Stream Analytics 작업의 파이프라인에서 병목 상태를 식별 합니다. 검토 **입/출력 이벤트가** 처리량에 대 한 및 ["워터 마크 지연"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) 또는 **백로그 된 이벤트** 경우 작업을 따라간다 입력된 속도 확인 합니다. 검색할 Event Hub 메트릭에 대 한 **요청 제한** 임계값 단위를 적절 하 게 조정 합니다. Cosmos DB 메트릭에 대 한 검토 **파티션 키 범위 별로 사용 된 최대 RU/s** 파티션 키 범위를 확인 하는 처리량에서 균일 하 게 사용 합니다. Azure SQL DB에 대 한 모니터링 **로그 IO** 하 고 **CPU**합니다.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Machine learning을 사용 하 여 Azure Stream Analytics의 변칙 검색

다음 비디오는 Azure Stream Analytics에서 machine learning 함수를 사용 하 여 실시간으로에서 변칙을 검색 하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


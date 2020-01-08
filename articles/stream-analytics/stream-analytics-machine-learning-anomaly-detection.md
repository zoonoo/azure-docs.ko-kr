---
title: Azure Stream Analytics의 변칙 검색
description: 이 아티클에서는 Azure Stream Analytics 및 Azure Machine Learning을 함께 사용하여 변칙을 검색하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e29ac6671d71ea02b432c9843541796984737c8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459618"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics의 변칙 검색

클라우드와 Azure IoT Edge 둘 다에서 사용할 수 있는 Azure Stream Analytics는 가장 일반적으로 발생하는 두 가지 변칙(임시 및 영구적)을 모니터링하는 데 사용할 수 있는 기계 학습 기반의 기본 제공 변칙 검색 기능을 제공합니다. **AnomalyDetection_SpikeAndDip** 및 **AnomalyDetection_ChangePoint** 함수를 사용하여 Stream Analytics 작업에서 직접 변칙 검색을 수행할 수 있습니다.

기계 학습 모델은 균일하게 샘플링된 시계열을 가정합니다. 시계열이 균일하지 않으면 변칙 검색을 호출하기 전에 연속 창을 사용하여 집계 단계를 삽입할 수 있습니다.

Machine learning 작업은 현재 계절성 추세 또는 여러 번의 상관 관계를 지원 하지 않습니다.

## <a name="model-behavior"></a>모델 동작

일반적으로 모델 정확도는 슬라이딩 윈도우의 데이터가 많을수록 향상됩니다. 지정된 슬라이딩 윈도우의 데이터는 해당 시간 프레임 동안 정상 범위의 일부로 처리됩니다. 모델은 슬라이딩 윈도우의 이벤트 기록만 고려하여 현재 이벤트가 비정상인지 확인합니다. 슬라이딩 윈도우가 이동하면 이전 값이 모델 학습에서 제거됩니다.

함수는 지금까지 확인한 내용을 기반으로 해서 특정 표준을 설정하여 작동합니다. 신뢰 수준 내에서 설정된 표준과 비교하여 이상값을 식별합니다. 변칙이 발생할 경우 인식할 수 있도록 정상 동작에 대한 모델 학습에 필요한 최소 이벤트 수를 기준으로 윈도우 크기를 설정해야 합니다.

더 많은 수의 과거 이벤트를 비교 해야 하기 때문에 모델의 응답 시간이 기록 크기로 증가 합니다. 성능 향상을 위해 필요한 개수의 이벤트만 포함하는 것이 좋습니다.

시계열의 간격은 모델이 특정 시점에 이벤트를 수신하지 않아서 발생하는 결과일 수 있습니다. 이 상황은 대체 논리를 사용 하 여 Stream Analytics에서 처리 됩니다. 동일한 슬라이딩 윈도우의 기간뿐만 아니라 기록 크기는 이벤트가 도착하는 예상 평균 속도를 계산하는 데 사용됩니다.

[여기](https://aka.ms/asaanomalygenerator) 에서 사용 가능한 변칙 생성기를 사용 하 여 다른 변칙 패턴의 데이터로 Iot Hub를 공급할 수 있습니다. 이러한 변칙 검색 기능을 사용 하 여이 Iot Hub에서 읽고 변칙을 검색할 수 있습니다.

## <a name="spike-and-dip"></a>급증 및 급감

시계열 이벤트 스트림의 일시적인 변칙을 급증 및 급감이라고 합니다. 급증 및 급감은 기계 학습 기반 연산자인 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)을 사용하여 모니터할 수 있습니다.

![급증 및 급감 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

동일한 슬라이딩 윈도우에서 두 번째 급증이 첫 번째 급증보다 작을 경우 더 작은 급증에 대해 계산된 점수가 지정된 신뢰 수준 내에서 첫 번째 급증의 점수와 비교하여 유의미하지 않을 수 있습니다. 모델의 신뢰도를 줄여 이러한 변칙을 검색할 수 있습니다. 그러나 너무 많은 경고를 받기 시작하면 더 높은 신뢰 구간을 사용할 수 있습니다.

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

이러한 모델의 성능은 기록 크기, 기간, 이벤트 로드 및 함수 수준 분할 사용 여부에 따라 달라 집니다. 이 섹션에서는 이러한 구성에 대해 설명 하 고 초당 1K, 5K 및 10K 이벤트 수집 속도를 유지 하는 방법에 대 한 샘플을 제공 합니다.

* **기록 크기** -이러한 모델은 **기록 크기로**선형으로 수행 됩니다. 기록 크기가 길수록 모델이 새 이벤트 점수를 매기는 데 걸리는 시간이 길어집니다. 이는 모델이 새 이벤트를 기록 버퍼의 각 이전 이벤트와 비교 하기 때문입니다.
* **기간** - **창 지속 시간은** 기록 크기에 지정 된 수 만큼 이벤트를 수신 하는 데 걸리는 시간을 반영 해야 합니다. 창에 이벤트가 없으면 Azure Stream Analytics에서 누락 된 값을 돌립니다 수 있습니다. 따라서 CPU 소비는 기록 크기의 함수입니다.
* **이벤트 로드** - **이벤트 로드가**많을 수록 CPU 소비에 영향을 주는 모델에 의해 수행 되는 작업이 더 많이 발생 합니다. 작업은 더 많은 입력 파티션을 사용 하는 비즈니스 논리에 적합 하다 고 가정 하 고 처리가 적합 병렬로 만들어 확장할 수 있습니다.
* 함수 **수준 분할 - 함수** 수준 분할은 변칙 검색 함수 호출 내에서 ```PARTITION BY```를 사용 하 여 **수행 됩니다.** 이러한 유형의 분할은 여러 모델에 대 한 상태를 동시에 유지 해야 하므로 오버 헤드를 추가 합니다. 함수 수준 분할은 장치 수준 분할과 같은 시나리오에서 사용 됩니다.

### <a name="relationship"></a>관계
기록 크기, 기간 및 총 이벤트 로드는 다음과 같은 방식으로 관련 됩니다.

windowDuration (밀리초) = 1000 * historySize/(초당 총 입력 이벤트 수/입력 파티션 수)

DeviceId로 함수를 분할 하는 경우 변칙 검색 함수 호출에 "PARTITION BY deviceId"를 추가 합니다.

### <a name="observations"></a>결과
다음 표에는 분할 되지 않은 경우에 단일 노드 (6 SU)의 처리량 관찰이 포함 되어 있습니다.

| 기록 크기 (이벤트) | 기간 (밀리초) | 초당 총 입력 이벤트 수 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1650 |
| 6,000 | 10910 | 1,100 |

다음 표에는 분할 된 사례에 대 한 단일 노드 (6 SU)의 처리량 관찰이 포함 되어 있습니다.

| 기록 크기 (이벤트) | 기간 (밀리초) | 초당 총 입력 이벤트 수 | 디바이스 수 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1091 | 1,100 | 10 |
| 600 | 10910 | 1,100 | 10 |
| 6,000 | 218182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218182 | 550 | 100 |
| 6,000 | 2181819 | <550 | 100 |

위에서 분할 되지 않은 구성을 실행 하는 샘플 코드는 Azure 샘플의 [스트리밍 규모](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) 리포지토리에 있습니다. 이 코드는 이벤트 허브를 입력 및 출력으로 사용 하는 함수 수준 분할 없이 stream analytics 작업을 만듭니다. 입력 부하는 테스트 클라이언트를 사용 하 여 생성 됩니다. 각 입력 이벤트는 1KB json 문서입니다. 이벤트는 JSON 데이터를 전송 하는 IoT 장치를 시뮬레이션 합니다 (최대 1K 장치). 기록 크기, 기간 및 총 이벤트 로드는 2 개의 입력 파티션에 따라 달라 집니다.

> [!Note]
> 보다 정확한 예측을 위해 시나리오에 맞게 예제를 사용자 지정 합니다.

### <a name="identifying-bottlenecks"></a>병목 상태 식별
Azure Stream Analytics 작업의 메트릭 창을 사용 하 여 파이프라인의 병목 상태를 식별할 수 있습니다. 처리량 및 ["워터 마크 지연"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) 또는 **백로그 이벤트** 에 대 한 **입/출력 이벤트** 를 검토 하 여 작업이 입력 속도를 유지 하는지 확인 합니다. 이벤트 허브 메트릭에 대해 **제한 된 요청** 을 찾아 임계값 단위를 적절 하 게 조정 합니다. Cosmos DB 메트릭은 처리량에서 **파티션 키 범위별 최대** 사용량을 검토 하 여 파티션 키 범위를 균등 하 게 사용 하는지 확인 합니다. Azure SQL DB의 경우 **로그 IO** 및 **CPU**를 모니터링 합니다.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Azure Stream Analytics에서 machine learning을 사용 하 여 변칙 검색

다음 비디오는 Azure Stream Analytics에서 기계 학습 함수를 사용 하 여 실시간으로 변칙을 검색 하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


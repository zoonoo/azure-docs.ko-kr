---
title: Anomaly Detector API를 사용하는 경우 모범 사례
titleSuffix: Azure Cognitive Services
description: 변칙 탐지기 API를 사용 하 여 변칙을 검색할 때 모범 사례에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "67721631"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>변칙 탐지기 API 사용에 대 한 모범 사례

변칙 탐지기 API는 상태 비저장 변칙 검색 서비스입니다. 결과의 정확성과 성능은 다음과 같은 영향을 받을 수 있습니다.

* 시계열 데이터를 준비 하는 방법입니다.
* 사용 된 변칙 탐지기 API 매개 변수입니다.
* API 요청의 데이터 요소 수입니다. 

이 문서에서는 API를 사용 하 여 데이터에 대 한 최상의 결과를 얻는 모범 사례에 대해 알아봅니다. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>일괄 처리 (전체) 또는 최신 (마지막) 지점 비정상 검색을 사용 하는 경우

변칙 탐지기 API의 일괄 처리 검색 끝점을 사용 하면 전체 시계열 데이터를 통해 변칙을 검색할 수 있습니다. 이 검색 모드에서는 단일 통계 모델이 만들어지고 데이터 집합의 각 지점에 적용 됩니다. 시계열에 다음과 같은 특징이 있는 경우 batch 검색을 사용 하 여 하나의 API 호출로 데이터를 미리 보는 것이 좋습니다.

* 때때로 비정상 상황을 포함 하는 계절 시계열.
* 가끔 급증 하는 급수/dip가 있는 평면 추세 시계열. 

실시간 데이터 모니터링을 위해 일괄 처리 변칙 검색을 사용 하거나 위의 특성이 없는 시계열 데이터에는 사용 하지 않는 것이 좋습니다. 

* 일괄 처리 검색은 하나의 모델만 만들고 적용 하며, 각 지점의 검색은 전체 계열의 컨텍스트에서 수행 됩니다. 계절성 하지 않고 시계열 데이터의 추세가 증가 하거나 감소 하는 경우 모델에서 일부 변경 사항 (데이터의 dip 및 스파이크)이 누락 될 수 있습니다. 마찬가지로 데이터 집합에서 나중에 보다 중요 한 변경 사항 중 일부는 모델에 통합할 만큼 크게 계산 되지 않을 수 있습니다.

* 일괄 처리 검색은 분석 되는 지점 수 때문에 실시간 데이터 모니터링을 수행할 때 최근 지점의 변칙 상태를 검색 하는 것 보다 느립니다.

실시간 데이터 모니터링을 위해 최신 데이터 요소의 변칙 상태만 검색 하는 것이 좋습니다. 최신 지점 검색을 지속적으로 적용 하 여 스트리밍 데이터 모니터링을 보다 효율적이 고 정확 하 게 수행할 수 있습니다.

아래 예제에서는 이러한 검색 모드가 성능에 미칠 수 있는 영향을 설명 합니다. 첫 번째 그림은 이전에 표시 된 데이터 요소 28 개를 따라 변칙 상태 최신 지점을 지속적으로 검색 한 결과를 보여 줍니다. 빨간색 점은 비정상입니다.

![최근 지점을 사용 하 여 변칙 검색을 보여 주는 이미지](../media/last.png)

다음은 일괄 처리 변칙 검색을 사용 하는 동일한 데이터 집합입니다. 작업에 대해 작성 된 모델이 사각형으로 표시 된 여러 가지 비정상 작업을 무시 했습니다.

![Batch 메서드를 사용 하 여 변칙 검색을 보여 주는 이미지](../media/entire.png)

## <a name="data-preparation"></a>데이터 준비

변칙 탐지기 API는 JSON 요청 개체에 형식이 지정 된 시계열 데이터를 허용 합니다. 시계열은 시간에 따라 순차적으로 기록 되는 모든 숫자 데이터를 사용할 수 있습니다. 시계열 데이터의 windows를 변칙 탐지기 API 끝점으로 보내 API의 성능을 향상 시킬 수 있습니다. 전송할 수 있는 데이터 요소의 최소 수는 12이 고 최대값은 8640입니다. [세분성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) 은 데이터가 샘플링 되는 속도로 정의 됩니다. 

변칙 탐지기 API로 전송 되는 데이터 요소에는 유효한 UTC (협정 세계시) 타임 스탬프 및 숫자 값이 있어야 합니다. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

데이터가 비표준 시간 간격으로 샘플링 되는 경우 요청에 `customInterval` 특성을 추가 하 여 지정할 수 있습니다. 예를 들어 계열이 5 분 마다 샘플링 되는 경우 JSON 요청에 다음을 추가할 수 있습니다.

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>누락 된 데이터 요소

누락 된 데이터 요소는 균일 하 게 분산 된 시계열 데이터 집합, 특히 매우 세분화 된 시간 계열 데이터 집합에 일반적입니다 (작은 샘플링 간격). 예를 들어 몇 분 마다 샘플링 된 데이터입니다. 데이터의 예상 되는 요소 수의 10% 미만 누락은 검색 결과에 부정적인 영향을 주지 않아야 합니다. 이전 기간, 선형 보간 또는 이동 평균의 데이터 요소 대체와 같은 특성을 기반으로 데이터의 간격을 채우는 것이 좋습니다.

### <a name="aggregate-distributed-data"></a>분산 데이터 집계

변칙 탐지기 API는 균일 하 게 분산 된 시계열에서 가장 잘 작동 합니다. 데이터가 무작위로 분산 된 경우, 예를 들어 분당, 매시간 또는 매일 같은 시간 단위로 집계 해야 합니다.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>계절 패턴을 사용 하 여 데이터에 대 한 변칙 검색

시계열 데이터에 일정 한 간격으로 발생 하는 계절 패턴이 있는 경우 정확도 및 API 응답 시간을 향상 시킬 수 있습니다. 

JSON 요청 `period` 을 생성할 때를 지정 하면 변칙 검색 대기 시간을 최대 50%까지 줄일 수 있습니다. 는 `period` 시계열이 패턴을 반복 하는 데 걸리는 대략적인 데이터 요소 수를 지정 하는 정수입니다. 예를 들어, 하루에 한 개의 데이터 요소를 포함 하는 시계열 `period` 에 `7`는가 포함 되 고 시간당 한 지점을 포함 하는 시계열 (주간 패턴은 동일)은 `period` 의 `7*24`이 됩니다. 데이터의 패턴을 잘 모를 경우에는이 매개 변수를 지정할 필요가 없습니다.

최상의 결과를 위해 4 `period`가지 데이터 요소와 추가 데이터 요소를 제공 합니다. 예를 들어 위에서 설명한 대로 주간 패턴이 있는 매시간 데이터는 요청 본문 (`7 * 24 * 4 + 1`)에 673 데이터 요소를 제공 해야 합니다.

### <a name="sampling-data-for-real-time-monitoring"></a>실시간 모니터링을 위한 데이터 샘플링

스트리밍 데이터가 짧은 간격 (예: 초 또는 분)으로 샘플링 되는 경우 권장 되는 데이터 요소 수를 보내면 변칙 탐지기 API의 허용 되는 최대 수 (8640 데이터 요소)를 초과할 수 있습니다. 데이터가 안정적인 계절 패턴을 표시 하는 경우 시간 등의 시간 간격으로 시계열 데이터 샘플을 전송 하는 것이 좋습니다. 이러한 방식으로 데이터를 샘플링 하면 API 응답 시간도 현저 하 게 향상 됩니다. 

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../overview.md)
* [빠른 시작: 변칙 탐지기를 사용 하 여 시계열 데이터의 변칙 검색 REST API](../quickstarts/detect-data-anomalies-csharp.md)

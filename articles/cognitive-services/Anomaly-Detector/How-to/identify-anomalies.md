---
title: 타임시리즈 데이터에 변칙 탐지기 API를 사용하는 방법
titleSuffix: Azure Cognitive Services
description: 일괄 처리 또는 스트리밍 데이터에서 데이터의 이상 징후를 감지하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840217"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>방법: 타임시리즈 데이터에 변칙 탐지기 API 사용  

[변칙 검출기 API는](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) 변칙 검색의 두 가지 방법을 제공합니다. 시간 계열 전체에서 이상 징후를 일괄 처리로 검색하거나 최신 데이터 포인트의 변칙 상태를 감지하여 데이터가 생성될 때 이상 징후를 감지할 수 있습니다. 검색 모델은 각 데이터 포인트의 예상 값 및 상한 및 하한 변칙 검색 경계와 함께 변칙 결과를 반환합니다. 이러한 값을 사용하여 정상 값의 범위와 데이터의 이상 징후를 시각화할 수 있습니다.

## <a name="anomaly-detection-modes"></a>변칙 검색 모드 

변칙 검출기 API는 일괄 처리 및 스트리밍과 같은 탐지 모드를 제공합니다.

> [!NOTE]
> 다음 요청 URL을 구독에 적합한 끝점과 결합해야 합니다. 예: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>일괄 검색

지정된 시간 범위에 걸친 데이터 포인트 일괄 처리에서 이상 징후를 감지하려면 다음 요청 URI를 열렬 데이터와 함께 사용하십시오. 

`/timeseries/entire/detect`. 

API는 한 번에 열렬 데이터를 전송하여 전체 계열을 사용하여 모델을 생성하고 각 데이터 포인트를 분석합니다.  

### <a name="streaming-detection"></a>스트리밍 감지

스트리밍 데이터에 대한 이상 징후를 지속적으로 감지하려면 최신 데이터 포인트에서 다음 요청 URI를 사용합니다. 

`/timeseries/last/detect'`. 

새 데이터 포인트를 생성할 때 전송하여 실시간으로 데이터를 모니터링할 수 있습니다. 보내는 데이터 포인트로 모델이 생성되고 API는 타임계의 최신 점이 변칙인지 여부를 결정합니다.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>하부 및 상위 변칙 검색 경계 조정

기본적으로 변칙 검색의 상한 및 하한 `expectedValue`경계는 을 `upperMargin`사용하여 계산됩니다. `lowerMargin` 다른 경계가 필요한 경우 `marginScale` 를 `upperMargin` 적용하거나 `lowerMargin`을 적용하는 것이 좋습니다. 경계는 다음과 같이 계산됩니다.

|경계  |계산  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

다음 예제에서는 서로 다른 감도에서 변칙 검출기 API 결과를 보여 준다.

### <a name="example-with-sensitivity-at-99"></a>민감도 99의 예

![기본 민감도](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>민감도 95의 예

![99 감도](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>감도 85의 예

![85 감도](../media/sensitivity_85.png)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../overview.md)
* [빠른 시작: 변칙 검출기 REST API를 사용하여 열렬 데이터의 이상 징후 감지](../quickstarts/detect-data-anomalies-csharp.md)

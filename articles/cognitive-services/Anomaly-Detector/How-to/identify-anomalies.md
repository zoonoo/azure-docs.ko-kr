---
title: 시계열 데이터에서 변칙 검색기 API를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: 일괄 처리 또는 데이터를 스트리밍 데이터에서 변칙을 검색 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 551196815004cb047680e2ae2f8dbe32186c1a0c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721799"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>방법: 비정상 탐지기 API를 사용 하 여 시계열 데이터에서  

합니다 [비정상 탐지기 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) 변칙 검색의 두 가지 방법을 제공 합니다. 있음에 전체 일괄 처리로 변칙을 검색 하거나 계열 또는 데이터는 최신 데이터 요소의 비정상 상태를 감지 하 여 생성 됩니다. 감지 모델을 검색 경계와 함께 각 데이터 요소의 예상 값과 상한 및 하 한 변칙 이상 결과 반환합니다. 폭넓은 정상 값 및 데이터에서 잘못 된 부분을 시각화 하려면이 값을 사용할 수 있습니다.

## <a name="anomaly-detection-modes"></a>변칙 검색 모드 

비정상 탐지기 API는 검색 모드를 제공 합니다: 일괄 처리 및 스트리밍을 합니다.

> [!NOTE]
> 다음 요청 Url에 구독에 대 한 적절 한 끝점을 사용 하 여 결합 해야 합니다. 예: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>일괄 검색

지정 된 시간 범위 동안 전체 데이터 요소의 일괄 처리에서 변칙을 검색, 하려면 시계열 데이터를 사용 하 여 다음 요청 URI를 사용 합니다. 

`/timeseries/entire/detect`. 

시계열 데이터를 한 번에 전송 하 여 API를 전체 시리즈를 사용 하 여 모델을 생성 되며이 사용 하 여 각 데이터 요소를 분석 합니다.  

### <a name="streaming-detection"></a>스트리밍 검색

스트리밍 데이터에서 변칙을 지속적으로 검색 하려면 다음 요청 URI에 최신 데이터 요소를 사용 하 여 사용 합니다. 

`/timeseries/last/detect'`. 

사용자가 생성 하는 경우 새 데이터 지점을 보내 실시간으로 데이터를 모니터링할 수 있습니다. 모델에 보내는 데이터 요소를 사용 하 여 생성 되 고 API는 시계열의 최근 시점 비정상 인지 확인 됩니다.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>한 및 상한 변칙 검색 경계를 조정합니다.

기본적으로 변칙 검색에 대 한 상한 및 하 한 경계를 사용 하 여 계산 됩니다 `expectedValue`하십시오 `upperMargin`, 및 `lowerMargin`합니다. 다른 경계를 필요로 하는 경우 적용는 것이 좋습니다는 `marginScale` 하 `upperMargin` 또는 `lowerMargin`합니다. 경계는 다음과 같이 계산 됩니다.

|경계  |계산  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

다음 예제에서는 다른 구분 정보가 들어 있는 비정상 탐지기 API 결과를 보여 줍니다.

### <a name="example-with-sensitivity-at-99"></a>99에 대 한 민감도 사용 하 여 예제

![기본 민감도](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>95에서 민감도 사용 하 여 예제

![99 민감도](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>85의 민감도 사용 하 여 예제

![85 민감도](../media/sensitivity_85.png)

## <a name="next-steps"></a>다음 단계

* [비정상 탐지기 API 란 무엇 인가요?](../overview.md)
* [빠른 시작: 비정상 탐지기 REST API를 사용 하 여 시계열 데이터에서 변칙을 검색 합니다.](../quickstarts/detect-data-anomalies-csharp.md)
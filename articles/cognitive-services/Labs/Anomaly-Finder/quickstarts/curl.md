---
title: cURL 및 Anomaly Finder API를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services에서 cURL 및 Anomaly Finder API를 빠르게 사용하는 데 도움이 되는 정보를 가져옵니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 9f47a49b41156967e0d4ef1c729a6bbb1cca1770
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456306"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>cURL 및 Anomaly Finder API 사용

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

이 문서에서 제공하는 정보 및 코드 샘플을 통해 cURL과 함께 Anomaly Finder API 사용을 빠르게 시작하여 시계열 데이터의 변칙 결과를 가져오는 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>cURL을 사용하여 Anomaly Finder API로 변칙 요소 가져오기 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>시계열 데이터의 예

시계열 데이터 요소의 예는 다음과 같습니다.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>데이터 분석 및 변칙 요소 cURL 예제 가져오기

예제 사용 단계는 다음과 같습니다.

1. `[YOUR_SUBSCRIPTION_KEY]` 값을 유효한 구독 키로 바꿉니다.
2. 구독 키를 획득한 위치를 사용하도록 `[YOUR_REGION]`을 바꿉니다.
3. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]`를 예제 또는 사용자 고유의 데이터 요소로 바꿉니다.
4. 응답을 실행하고 확인합니다.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>예제 응답
성공적인 응답이 JSON을 통해 반환됩니다. 예제 응답은 다음과 같습니다. [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

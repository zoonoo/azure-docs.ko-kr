---
title: PHP 및 Anomaly Finder API를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services에서 PHP와 함께 Anomaly Finder 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: e5262de518e6cdf5a131fa9a84a3aa8ce9ce9236
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213687"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>PHP 및 Anomaly Finder API 사용

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

이 문서에서 제공하는 정보 및 코드 샘플을 통해 PHP와 함께 Anomaly Finder API 사용을 빠르게 시작하여 시계열 데이터의 변칙 결과를 가져오는 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>PHP를 사용하여 Anomaly Finder API로 변칙 요소 가져오기
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>시계열 데이터의 예
시계열 데이터의 예는 다음과 같습니다.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>데이터 분석 및 변칙 요소 PHP 예제 가져오기
1. `[YOUR_SUBSCRIPTION_KEY]` 값을 유효한 구독 키로 바꿉니다.
2. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]`를 예제 또는 사용자 고유의 데이터 요소로 바꿉니다.
3. 응답을 실행하고 확인합니다.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>예제 응답

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 응답은 다음과 같습니다.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

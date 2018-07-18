---
title: Ruby 및 Anomaly Finder API를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services에서 Ruby 및 Anomaly Finder API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375327"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Ruby 및 Anomaly Finder API 사용

이 문서에서 제공하는 정보 및 코드 샘플을 통해 Ruby와 함께 Anomaly Finder API 사용을 빠르게 시작하여 시계열 데이터의 변칙 검색 결과를 가져오는 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Ruby를 사용하여 Anomaly Finder API로 변칙 요소 가져오기 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>시계열 데이터의 예
시계열 데이터 요소의 예는 다음과 같습니다. [!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>데이터 분석 및 변칙 요소 Ruby 예제 가져오기

예제 사용 단계는 다음과 같습니다.

1. ‘gem install rest-client’를 실행하여 [rest-client](https://github.com/rest-client/rest-client)를 설치합니다.
2. 아래 코드를 .rb 파일로 저장합니다.
3. `[YOUR_SUBSCRIPTION_KEY]` 값을 유효한 구독 키로 바꿉니다.
4. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]`를 예제 또는 사용자 고유의 데이터 요소로 바꿉니다.
5. 응답을 실행하고 확인합니다.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>예제 응답

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 응답은 다음과 같습니다.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

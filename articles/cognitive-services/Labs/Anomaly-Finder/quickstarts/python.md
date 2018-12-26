---
title: Python 및 Anomaly Finder API를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services에서 Python과 함께 Anomaly Finder 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: eb64bb4daece44d33775dca7417c51b216e04614
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455439"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Python 및 Anomaly Finder API 사용

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

이 문서에서 제공하는 정보 및 코드 샘플을 통해 Python과 함께 Anomaly Finder API 사용을 빠르게 시작하여 시계열 데이터의 변칙 결과를 가져오는 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Python을 사용하여 Anomaly Finder API로 변칙 요소 가져오기 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>시계열 데이터의 예

시계열 데이터 요소의 예는 다음과 같습니다.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>데이터 분석 및 변칙 요소 Python 예제 가져오기

python3을 설치했는지 확인하고 detect.py라는 Python 실행 파일을 만듭니다. detect.py에서 아래 코드를 포함해야 합니다. 코드를 실행하기 전에 `[YOUR_SUBSCRIPTION_KEY]` 값을 유효한 구독 키로 바꾸어야 합니다.
`[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]`를 데이터 요소로 바꿉니다.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>예제 응답

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 응답은 다음과 같습니다.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Python 앱](../tutorials/python-tutorial.md)

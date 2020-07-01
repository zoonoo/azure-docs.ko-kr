---
title: '빠른 시작: Python을 사용하여 텍스트 분석 API 호출'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Cognitive Services에서 Text Analytics API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 가져오는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: 46fd431c14e6870058e415d619256f342d5f4ef4
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610934"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>빠른 시작: Python REST API를 사용하여 Text Analytics Cognitive Service 호출 
<a name="HOLTop"></a>

이 빠른 시작을 사용하여 Text Analytics REST API 및 Python을 통해 언어 분석을 시작합니다. 이 문서에서는 [언어 검색](#Detect), [감정 분석](#SentimentAnalysis), [핵심 구 추출](#KeyPhraseExtraction) 및 [연결된 엔터티 식별](#Entities)을 수행하는 방법을 보여줍니다.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.x](https://python.org)

* Python 요청 라이브러리
    
    이 명령을 사용하여 라이브러리를 설치할 수 있습니다.

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

즐겨 찾는 편집기 또는 IDE에서 새 Python 애플리케이션을 만듭니다. 다음 가져오기를 파일에 추가합니다.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

다음 섹션에서는 각 API 기능을 호출하는 방법을 설명합니다.

<a name="Detect"></a>

## <a name="detect-languages"></a>언어 감지

Text Analytics 기본 엔드포인트에 `/text/analytics/v3.0/languages`를 추가하여 언어 검색 URL을 형성합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

API에 대한 페이로드는 각각 `id` 및 `text` 특성이 포함된 튜플인 `documents`의 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

요청 라이브러리를 사용하여 API에 문서를 보냅니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고 `requests.post()`를 사용하여 요청을 보냅니다. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>출력

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>감정 분석

문서 세트의 감정(양수 또는 음수 사이의 범위)을 검색하려면 Text Analytics 기본 엔드포인트에 `/text/analytics/v3.0/sentiment`를 추가하여 언어 검색 URL을 형성합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

언어 검색 예제와 마찬가지로, 문서 목록으로 구성된 `documents` 키가 있는 사전을 만듭니다. 각 문서는 `id`, 분석할 `text` 및 텍스트의 `language`로 구성된 튜플입니다. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

요청 라이브러리를 사용하여 API에 문서를 보냅니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고 `requests.post()`를 사용하여 요청을 보냅니다. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>출력

문서의 감정 점수는 0.0에서 1.0 사이이며, 점수가 높을수록 보다 긍정적인 감정을 나타냅니다.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>핵심 구 추출
 
문서 세트에서 핵심 구를 추출하려면 Text Analytics 기본 엔드포인트에 `/text/analytics/v3.0/keyPhrases`를 추가하여 언어 검색 URL을 형성합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

이 문서 컬렉션은 감정 분석 예제에 사용된 것과 동일합니다.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

요청 라이브러리를 사용하여 API에 문서를 보냅니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고 `requests.post()`를 사용하여 요청을 보냅니다. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>출력

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>엔터티 식별

텍스트 문서에서 잘 알려진 엔터티(사람, 장소 및 사물)를 식별하려면 Text Analytics 기본 엔드포인트에 `/text/analytics/v3.0/entities/recognition/general`를 추가하여 언어 검색 URL을 형성합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general/recognition/general"
```

이전 예제에서와 같은 문서 컬렉션을 만듭니다. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

요청 라이브러리를 사용하여 API에 문서를 보냅니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고 `requests.post()`를 사용하여 요청을 보냅니다.

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>출력

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

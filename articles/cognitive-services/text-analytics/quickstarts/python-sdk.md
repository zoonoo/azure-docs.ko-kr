---
title: '빠른 시작: Python SDK를 사용하여 Text Analytics Service 호출'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: b319abf22f9aa4cdd9a5fef91be0628672d47bd4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297795"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>빠른 시작: Python SDK를 사용하여 Text Analytics Service 호출 
<a name="HOLTop"></a>

이 빠른 시작을 사용하여 Python용 Text Analytics SDK를 통해 언어 분석을 시작합니다. Text Analytics REST API는 대부분의 프로그래밍 언어와 호환되는 반면, SDK를 사용하면 JSON의 직렬화 및 역직렬화 없이 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Python 3.x](https://www.python.org/)

* Text Analytics [Python용 SDK](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) 다음을 사용하여 패키지를 설치할 수 있습니다.

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

등록하는 동안 생성된 [엔드포인트 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 있어야 합니다.

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

즐겨 찾는 편집기 또는 IDE에서 새 Python 애플리케이션을 만듭니다. 그런 다음, 다음 import 문을 파일에 추가합니다.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>자격 증명 인증

> [!Tip]
> 프로덕션 시스템에서의 안전한 비밀 배포를 위해 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)를 사용하는 것이 좋습니다.
>

Text Analytics 구독 키에 대한 변수를 만든 후 `CognitiveServicesCredentials` 개체를 인스턴스화합니다.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Text Analytics 클라이언트 만들기

`credentials` 및 `text_analytics_url`을 매개 변수로 사용하여 새 `TextAnalyticsClient` 개체를 만듭니다. Text Analytics 구독에 대한 올바른 Azure 지역을 사용합니다(예: `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>정서 분석

API에 대한 페이로드는 각각 `id` 및 `text` 특성이 포함된 사전인 `documents` 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하고, `id`는 임의의 값이 될 수 있습니다. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

`sentiment()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>출력

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>언어 검색

각각 분석하려는 문서가 포함된 사전들의 목록을 만듭니다. `text` 특성은 분석할 텍스트를 저장하고, `id`는 임의의 값이 될 수 있습니다. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

이전에 만든 클라이언트를 사용하여 `detect_language()`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 처음 반환된 언어를 인쇄합니다.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>출력

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>엔터티 인식

분석하려는 문서가 포함된 사전들의 목록을 만듭니다. `text` 특성은 분석할 텍스트를 저장하고, `id`는 임의의 값이 될 수 있습니다. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

이전에 만든 클라이언트를 사용하여 `entities()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 엔터티를 인쇄합니다.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>출력

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

분석하려는 문서가 포함된 사전들의 목록을 만듭니다. `text` 특성은 분석할 텍스트를 저장하고, `id`는 임의의 값이 될 수 있습니다. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

이전에 만든 클라이언트를 사용하여 `key_phrases()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 핵심 문구를 인쇄합니다.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>출력

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목

* [Text Analytics API란?](../overview.md)
* [사용자 시나리오 예제](../text-analytics-user-scenarios.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

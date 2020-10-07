---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 9a7846d1a63ee0b3042bbea473babffbe52f06a6
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779448"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

[v3.1 참조 설명서](https://docs.microsoft.com/python/api/azure-ai-textanalytics/azure.ai.textanalytics?view=azure-python-preview&preserve-view=true) | [v3.1 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3.1 패키지(PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3.1 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

[v3 참조 설명서](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 패키지(PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

[v2 참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics) | [v2 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어<span class="docon docon-navigate-external x-hidden-focus"></span></a> 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

---

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

새 Python 파일을 만들고 리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>개체 모델

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

Text Analytics 클라이언트는 Azure에 인증하는 `TextAnalyticsClient` 개체입니다. 이 클라이언트는 텍스트를 분석하는 여러 메서드를 제공합니다. 

텍스트 처리는 `documents` 목록으로 API에 전송됩니다. 이 목록에는 문자열 목록, dict와 같은 표현 목록 또는 `TextDocumentInput/DetectLanguageInput` 목록이 있습니다. `dict-like` 개체는 `id`, `text` 및 `language/country_hint`의 조합을 포함합니다. `text` 특성은 분석할 텍스트를 `country_hint` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 `TextAnalyticsClient` 개체입니다. 이 클라이언트는 텍스트를 일괄 처리로 분석하는 여러 메서드를 제공합니다. 

일괄 처리 텍스트는 API에 `documents` 목록으로 전송될 때, 사용되는 메서드에 따라 `id`, `text` 및 `language` 특성 조합을 포함하는 `dictionary` 개체로 전송됩니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석된 정보가 포함된 목록입니다. 

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient) 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다. 

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

---

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각은 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner) 
* [개인 식별 정보 인식](#personally-identifiable-information-recognition) 
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)


# <a name="version-30"></a>[버전 3.0](#tab/version-3)

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner) 
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner) 
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>클라이언트 인증

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

위에서 만든 `key` 및 `endpoint`를 사용하여 `TextAnalyticsClient` 개체를 인스턴스화하는 기능을 만듭니다. 그런 다음, 새 클라이언트를 만듭니다. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

위에서 만든 `key` 및 `endpoint`를 사용하여 `TextAnalyticsClient` 개체를 인스턴스화하는 기능을 만듭니다. 그런 다음, 새 클라이언트를 만듭니다. 버전 3.0을 사용하기 위해 `api_version=TextAnalyticsApiVersion.V3_0`을 정의해야 합니다.

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential, 
            api_version=TextAnalyticsApiVersion.V3_0)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

위에서 만든 `key` 및 `endpoint`를 사용하여 `TextAnalyticsClient` 개체를 인스턴스화하는 기능을 만듭니다. 그런 다음, 새 클라이언트를 만듭니다. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>정서 분석

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

클라이언트를 인수로 사용하는 `sentiment_analysis_example()`이라는 새 함수를 만든 다음, `analyze_sentiment()` 함수를 호출합니다. 반환된 응답 개체에는 전체 입력 문서의 감정 레이블과 점수뿐 아니라 각 문장의 감정 분석도 포함됩니다.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>출력

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>의견 마이닝

오피니언 마이닝을 사용하여 감정 분석을 수행하려면 클라이언트를 인수로 사용하는 `sentiment_analysis_with_opinion_mining_example()`이라는 새 함수를 만든 다음, 옵션 플래그 `show_opinion_mining=True`를 사용하여 `analyze_sentiment()` 함수를 호출합니다. 반환된 응답 개체에는 각 문장의 감정 분석이 포함된 전체 입력 문서의 감정 레이블과 점수뿐 아니라 양상 및 의견 수준 감정 분석도 포함됩니다.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice",
        "The rooms were beautiful but dirty. The AC was good and quiet, but the elevator was broken"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                aspect = mined_opinion.aspect
                print("......'{}' aspect '{}'".format(aspect.sentiment, aspect.text))
                for opinion in mined_opinion.opinions:
                    print("......'{}' opinion '{}'".format(opinion.sentiment, opinion.text))
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>출력

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' aspect 'food'
......'negative' opinion 'unacceptable'
......'negative' aspect 'service'
......'negative' opinion 'unacceptable'
......'positive' aspect 'concierge'
......'positive' opinion 'nice'


Document Sentiment: negative
Overall scores: positive=0.00; neutral=0.00; negative=1.00

Sentence: The rooms were beautiful but dirty.
Sentence sentiment: negative
Sentence score:
Positive=0.01
Neutral=0.00
Negative=0.99

......'mixed' aspect 'rooms'
......'positive' opinion 'beautiful'
......'negative' opinion 'dirty'
Sentence: The AC was good and quiet, but the elevator was broken
Sentence sentiment: negative
Sentence score:
Positive=0.00
Neutral=0.00
Negative=1.00

......'positive' aspect 'AC'
......'positive' opinion 'good'
......'positive' opinion 'quiet'
......'negative' aspect 'elevator'
......'negative' opinion 'broken'
```

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

클라이언트를 인수로 사용하는 `sentiment_analysis_example()`이라는 새 함수를 만든 다음, `analyze_sentiment()` 함수를 호출합니다. 반환된 응답 개체에는 전체 입력 문서의 감정 레이블과 점수뿐 아니라 각 문장의 감정 분석도 포함됩니다.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>출력

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

클라이언트 개체를 인증하고 [감정()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출합니다. 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>언어 검색

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

클라이언트를 인수로 사용하는 `language_detection_example()`이라는 새 함수를 만든 다음, `detect_language()` 함수를 호출합니다. 반환된 응답 개체에는 성공하면 `primary_language`에서 검색된 언어가 포함되고, 그렇지 않으면 `error`가 포함됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `country_hint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `country_hint : ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>출력

```console
Language:  French
```

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

클라이언트를 인수로 사용하는 `language_detection_example()`이라는 새 함수를 만든 다음, `detect_language()` 함수를 호출합니다. 반환된 응답 개체에는 성공하면 `primary_language`에서 검색된 언어가 포함되고, 그렇지 않으면 `error`가 포함됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `country_hint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `country_hint : ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>출력

```console
Language:  French
```

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

이전에 만든 클라이언트를 사용하여 [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-)를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 처음 반환된 언어를 인쇄합니다.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>출력

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

> [!NOTE]
> 버전 `3.1`에서 다음을 수행합니다. 
> * 엔터티 연결은 NER과 별개의 요청입니다.

클라이언트를 인수로 사용하는 `entity_recognition_example`이라는 새 함수를 만든 다음, `recognize_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entity`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 각 엔터티에 대해 해당 범주 및 하위 범주(있는 경우)를 인쇄합니다.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>출력

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>엔터티 연결

클라이언트를 인수로 사용하는 `entity_linking_example()`이라는 새 함수를 만든 다음, `recognize_linked_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `entity` 개체 아래에서 `match` 개체 목록으로 그룹화됩니다.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>출력

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>개인 식별 정보 인식

클라이언트를 인수로 사용하는 `pii_recognition_example`이라는 새 함수를 만든 다음, `recognize_pii_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entity`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 각 엔터티에 대해 해당 범주 및 하위 범주(있는 경우)를 인쇄합니다.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>출력

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

> [!NOTE]
> 버전 `3.0`에서 다음을 수행합니다. 
> * 엔터티 연결은 NER과 별개의 요청입니다.

클라이언트를 인수로 사용하는 `entity_recognition_example`이라는 새 함수를 만든 다음, `recognize_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entity`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 각 엔터티에 대해 해당 범주 및 하위 범주(있는 경우)를 인쇄합니다.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>출력

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>엔터티 연결

클라이언트를 인수로 사용하는 `entity_linking_example()`이라는 새 함수를 만든 다음, `recognize_linked_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `entity` 개체 아래에서 `match` 개체 목록으로 그룹화됩니다.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>출력

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

> [!NOTE]
> 버전 2.1에서 엔터티 연결은 NER 응답에 포함됩니다.

이전에 만든 클라이언트를 사용하여 [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 엔터티를 인쇄합니다.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>출력

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

### <a name="key-phrase-extraction"></a>핵심 문구 추출

# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

클라이언트를 인수로 사용하는 `key_phrase_extraction_example()`이라는 새 함수를 만든 다음, `extract_key_phrases()` 함수를 호출합니다. 결과에는 성공하면 `key_phrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 핵심 구를 출력합니다.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>출력

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

클라이언트를 인수로 사용하는 `key_phrase_extraction_example()`이라는 새 함수를 만든 다음, `extract_key_phrases()` 함수를 호출합니다. 결과에는 성공하면 `key_phrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 핵심 구를 출력합니다.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>출력

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-21"></a>[버전 2.1](#tab/version-2)

이전에 만든 클라이언트를 사용하여 [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 핵심 문구를 인쇄합니다.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>출력

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

--- 
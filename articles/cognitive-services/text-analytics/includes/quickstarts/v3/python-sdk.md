---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281176"
---
<a name="HOLTop"></a>

[참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [패키지(PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * 이 빠른 시작에서는 향상된 [감정 분석](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) 및 [NER(명명된 엔터티 인식)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)에 대한 공개 미리 보기가 포함된 `3.0-preview` 버전의 Text Analytics 클라이언트 라이브러리를 사용합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어 `azure.ai.textanalytics` 네임스페이스의 `analyze_sentiment()` 클라이언트 대신 `azure.ai.textanalytics.aio` 네임스페이스의 클라이언트를 가져오고 `analyze_sentiment()`를 호출합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

새 Python 파일을 만들고 리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 `TextAnalyticsClient` 개체입니다. 이 클라이언트는 텍스트를 일괄 처리로 분석하는 여러 메서드를 제공합니다. 이 빠른 시작에서는 함수 컬렉션을 사용하여 단일 문서를 빠르게 보냅니다.

일괄 처리 텍스트는 API에 `documents` 목록으로 전송될 때, 사용되는 메서드에 따라 `id`, `text` 및 `language` 특성 조합을 포함하는 `dictionary` 개체로 전송됩니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 단일 문서를 처리할 때 아래 예제에서 볼 수 있듯이 `text` 입력만 필요합니다.  

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각은 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [감정 분석](#sentiment-analysis)(공개 미리 보기)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-public-preview)(공개 미리 보기)
* [명명된 엔터티 인식 - 개인 정보](#named-entity-recognition---personal-information-public-preview)(공개 미리 보기)
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>정서 분석

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 감정 분석 v3용입니다.

엔드포인트와 키를 인수로 사용하는 `sentiment_analysis_example()`이라는 새 함수를 만든 다음, `single_analyze_sentiment()` 함수를 호출합니다. 반환된 응답 개체에는 전체 입력 문서의 감정 레이블과 점수뿐 아니라 각 문장의 감정 분석도 포함됩니다.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>출력

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>언어 검색

엔드포인트와 키를 인수로 사용하는 `language_detection_example()`이라는 새 함수를 만든 다음, `single_detect_languages()` 함수를 호출합니다. 반환된 응답 개체에는 성공하면 `detected_languages`에서 검색된 언어가 포함되고, 그렇지 않으면 `error`가 포함됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `country_hint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `country_hint : ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>출력

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>명명된 엔터티 인식(공개 미리 보기)

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 명명된 엔터티 인식 v3용입니다.

엔드포인트와 키를 인수로 사용하는 `entity_recognition_example`이라는 새 함수를 만든 다음, `single_recognize_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entity`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 각 엔터티에 대해 해당 형식 및 하위 형식(있는 경우)을 인쇄합니다.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>출력

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>명명된 엔터티 인식 - 개인 정보(공개 미리 보기)

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 명명된 엔터티 인식 v3을 사용하여 개인 정보를 검색합니다.

엔드포인트와 키를 인수로 사용하는 `entity_pii_example()`이라는 새 함수를 만든 다음, `single_recognize_pii_entities()` 함수를 호출하여 결과를 가져옵니다. 그런 다음, 결과를 반복하고 엔터티를 인쇄합니다.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>출력

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>엔터티 연결

엔드포인트와 키를 인수로 사용하는 `entity_linking_example()`이라는 새 함수를 만든 다음, `single_recognize_linked_entities()` 함수를 호출하여 결과를 반복합니다. 반환된 응답 개체에는 성공하면 `entities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `entity` 개체 아래에서 `match` 개체 목록으로 그룹화됩니다.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>출력

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

엔드포인트와 키를 인수로 사용하는 `key_phrase_extraction_example()`이라는 새 함수를 만든 다음, `single_extract_key_phrases()` 함수를 호출합니다. 결과에는 성공하면 `key_phrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `error`가 포함됩니다. 검색된 핵심 구를 출력합니다.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>출력

```console
    Key Phrases:
         cat
         veterinarian
```
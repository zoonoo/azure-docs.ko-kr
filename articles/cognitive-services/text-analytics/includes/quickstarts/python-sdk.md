---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3623d18e53881768ea6409c970065de231d233c3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750192"
---
<a name="HOLTop"></a>


[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [샘플](https://github.com/Azure-Samples/samples-cognitive-services-python-sdk/)


## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

[!code-python[import statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다. 환경 변수 TEXT_ANALYTICS_SUBSCRIPTION_KEY 및 TEXT_ANALYTICS_ENDPOINT에서 이러한 값을 가져옵니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-python[endpoint and key variables](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=initialVars)]

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다. 

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 포함하는 `CognitiveServicesCredentials` 개체를 사용하여 새 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python)를 만듭니다.

[!code-python[client authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

## <a name="sentiment-analysis"></a>정서 분석

클라이언트 개체를 인증하고 [감정()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출합니다. 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>언어 검색

이전에 만든 클라이언트를 사용하여 [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-)를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 처음 반환된 언어를 인쇄합니다.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>출력

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>엔터티 인식

이전에 만든 클라이언트를 사용하여 [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 엔터티를 인쇄합니다.

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

## <a name="key-phrase-extraction"></a>핵심 문구 추출

이전에 만든 클라이언트를 사용하여 [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 포함된 핵심 문구를 인쇄합니다.

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

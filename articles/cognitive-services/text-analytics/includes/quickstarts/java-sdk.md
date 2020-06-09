---
title: '빠른 시작: Java용 Text Analytics v3 클라이언트 라이브러리 | Microsoft Docs'
description: Java용 v3 Text Analytics 클라이언트 라이브러리를 시작합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: bd070300427716634d786e685cfe1cf8e45a246c
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140739"
---
<a name="HOLTop"></a>

[참조 설명서](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [패키지](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) 버전 8 이상
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어<span class="docon docon-navigate-external x-hidden-focus"></span></a> 키와 엔드포인트를 가져옵니다.  배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="add-the-client-library"></a>클라이언트 라이브러리 추가

선호하는 IDE 또는 개발 환경에서 Maven 프로젝트를 만듭니다. 그런 다음, 프로젝트의 *pom.xml* 파일에 다음 종속성을 추가합니다. 온라인에서 [다른 빌드 도구용](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) 구현 구문을 찾을 수 있습니다.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.5</version>
    </dependency>
</dependencies>
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

`TextAnalyticsSamples.java`라는 Java 파일을 만듭니다. 파일을 열고, 다음 `import` 문을 추가합니다.

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

java 파일에서 아래와 같이 새 클래스를 추가하고 Azure 리소스의 키와 엔드포인트를 추가합니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

다음 main 메서드를 클래스에 추가합니다. 나중에 여기서 호출되는 메서드를 정의합니다.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure를 인증하고 텍스트를 단일 문자열 또는 일괄 처리로 허용하는 함수를 제공하는 `TextAnalyticsClient` 개체입니다. 텍스트는 API에 동기식 또는 비동기식으로 보낼 수 있습니다. 응답 개체에는 보내는 각 문서에 대한 분석 정보가 포함됩니다. 

## <a name="code-examples"></a>코드 예제

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis) 
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner) 
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

Text Analytics 리소스의 키 및 엔드포인트를 사용하여 `TextAnalyticsClient` 개체를 인스턴스화하는 메서드를 만듭니다.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

프로그램의 `main()` 메서드에서 인증 메서드를 호출하여 클라이언트를 인스턴스화합니다.

## <a name="sentiment-analysis"></a>정서 분석

앞에서 만든 클라이언트를 사용하고 해당 `analyzeSentiment()` 함수를 호출하는 `sentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 `AnalyzeSentimentResult` 개체에는 성공하면 `documentSentiment` 및 `sentenceSentiments`가 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
```

### <a name="output"></a>출력

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>언어 검색

앞에서 만든 클라이언트를 사용하고 해당 `detectLanguage()` 함수를 호출하는 `detectLanguageExample()`이라는 새 함수를 만듭니다. 반환된 `DetectLanguageResult` 개체에는 검색된 기본 언어, 성공하는 경우 검색된 다른 언어 목록 또는 그렇지 않은 경우 `errorMessage`가 포함됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 다른 기본값을 설정하려면 `TextAnalyticsClientOptions.DefaultCountryHint` 속성을 설정하고 클라이언트를 초기화 중에 이를 전달합니다.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>출력

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

> [!NOTE]
> 버전 `3.0`에서 다음을 수행합니다.
> * NER에는 개인 정보를 검색하기 위한 별도의 방법이 포함되어 있습니다. 
> * 엔터티 연결은 NER과 별개의 요청입니다.

앞에서 만든 클라이언트를 사용하고 해당 `recognizeEntities()` 함수를 호출하는 `recognizeEntitiesExample()`이라는 새 함수를 만듭니다. 반환된 `RecognizeEntitiesResult` 개체에는 성공하는 경우 `NamedEntity` 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>출력

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="entity-linking"></a>엔터티 연결

앞에서 만든 클라이언트를 사용하고 해당 `recognizeLinkedEntities()` 함수를 호출하는 `recognizeLinkedEntitiesExample()`이라는 새 함수를 만듭니다. 반환된 `RecognizeLinkedEntitiesResult` 개체에는 성공하는 경우 `LinkedEntity` 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `LinkedEntity` 개체 아래에서 `LinkedEntityMatch` 개체 목록으로 그룹화됩니다.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>출력

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```
## <a name="key-phrase-extraction"></a>핵심 문구 추출

앞에서 만든 클라이언트를 사용하고 해당 `extractKeyPhrases()` 함수를 호출하는 `extractKeyPhrasesExample()`이라는 새 함수를 만듭니다. 반환된 `ExtractKeyPhraseResult` 개체에는 성공하는 경우 핵심 구 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>출력

```console
Recognized phrases: 
cat
veterinarian
```

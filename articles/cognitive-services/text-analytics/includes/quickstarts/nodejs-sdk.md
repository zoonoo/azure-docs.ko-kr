---
title: '빠른 시작: Node.js용 Text Analytics v3 클라이언트 라이브러리 | Microsoft Docs'
description: Node.js용 v3 Text Analytics 클라이언트 라이브러리를 시작합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 0cfe651a91cc16e7d4b58af67dac29fe5106a48c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986750"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

[v3 참조 설명서](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 패키지(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

[v2 참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어<span class="docon docon-navigate-external x-hidden-focus"></span></a> 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp 

cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```
### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

`@azure/ai-text-analytics` NPM 패키지 설치:

```console
npm install --save @azure/ai-text-analytics@1.0.0-preview.3
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

`@azure/cognitiveservices-textanalytics` NPM 패키지 설치:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub에서](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다. 

---

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.
`index.js`라는 파일을 만들고 다음을 추가합니다.

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 `TextAnalyticsClient` 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다.

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

* [클라이언트 인증](#client-authentication)
* [감정 분석](#sentiment-analysis) 
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner)
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="client-authentication"></a>클라이언트 인증

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

키와 엔드포인트를 매개 변수로 사용하여 새 `TextAnalyticsClient` 개체를 만듭니다.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

`credentials` 및 `endpoint`을 매개 변수로 사용하여 새 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) 개체를 만듭니다.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>정서 분석

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `analyzeSentiment()` 메서드를 호출하고 반환된 `SentimentBatchResult` 개체를 가져옵니다. 결과 목록을 반복하고 각 문서의 ID, 문서 수준 감정을 신뢰도 점수로 인쇄합니다. 각 문서에 대해 결과에는 오프셋, 길이 및 신뢰도 점수와 함께 문장 수준 감정이 포함됩니다.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

분석하려는 문서가 포함된 사전 개체의 목록을 만듭니다. 클라이언트의 [감정()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) 메서드를 호출하고 반환된 [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)를 가져옵니다. 결과 목록을 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>언어 검색

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `detectLanguage()` 메서드를 호출하고 반환된 `DetectLanguageResultCollection`를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID를 해당 기본 언어로 인쇄합니다.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

문서가 포함된 사전 개체의 목록을 만듭니다. 클라이언트의 [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) 메서드를 호출하고 반환된 [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID와 언어를 인쇄합니다.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

> [!NOTE]
> 버전 `3.0-preview`에서 다음을 수행합니다.
> * NER에는 개인 정보를 검색하기 위한 별도의 방법이 포함되어 있습니다. 
> * 엔터티 연결은 NER과 별개의 요청입니다.

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `recognizeEntities()` 메서드를 호출하고 `RecognizeEntitiesResult` 개체를 가져옵니다. 결과 목록을 반복하고 엔터티 이름, 형식, 하위 유형, 오프셋, 길이 및 점수를 인쇄합니다.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="using-ner-to-detect-personal-information"></a>NER을 사용하여 개인 정보 검색

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `recognizePiiEntities()` 메서드를 호출하고 `EntitiesBatchResult` 개체를 가져옵니다. 결과 목록을 반복하고 엔터티 이름, 형식, 하위 유형, 오프셋, 길이 및 점수를 인쇄합니다.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>엔터티 연결

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `recognizeLinkedEntities()` 메서드를 호출하고 `RecognizeLinkedEntitiesResult` 개체를 가져옵니다. 결과 목록을 반복하고 엔터티 이름, ID, 데이터 원본, url 및 일치 항목을 인쇄합니다. `matches` 배열의 모든 개체에는 해당 일치 항목에 대한 오프셋, 길이 및 점수가 포함됩니다.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

> [!NOTE]
> 버전 2.1에서 엔터티 연결은 NER 응답에 포함됩니다.

문서가 포함된 개체 목록을 생성합니다. 클라이언트의 [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) 메서드를 호출하고 [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) 개체를 가져옵니다. 결과 목록을 반복하고 각 문서의 ID를 인쇄합니다. 검색된 각 엔터티의 경우 원본 텍스트의 위치뿐만 아니라 wikipedia 이름, 형식 및 하위 형식(존재하는 경우)을 인쇄합니다.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

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

## <a name="key-phrase-extraction"></a>핵심 문구 추출

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `extractKeyPhrases()` 메서드를 호출하고 반환된 `ExtractKeyPhrasesResult` 개체를 가져옵니다. 결과를 반복하고 각 문서의 ID 및 검색된 주요 문구를 인쇄합니다.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

문서가 포함된 개체 목록을 생성합니다. 클라이언트의 [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) 메서드를 호출하고 반환된 [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) 개체를 가져옵니다. 결과를 반복하고 각 문서의 ID 및 검색된 주요 문구를 인쇄합니다.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

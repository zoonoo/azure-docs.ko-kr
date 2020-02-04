---
title: '빠른 시작: Node.js용 Text Analytics v3 클라이언트 라이브러리 | Microsoft Docs'
description: Node.js용 v3 Text Analytics 클라이언트 라이브러리를 시작합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 8ae78bb8c0b70e20bfe12b8dc52c02abb6030bdf
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774105"
---
<a name="HOLTop"></a>

[참조 설명서](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [패키지(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * 이 빠른 시작에서는 Text Analytics 클라이언트 라이브러리의 `3.0-preview` 버전을 사용합니다. 여기에는 향상된 [감정 분석](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) 및 [NER(명명된 엔터티 인식)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)에 대한 공개 미리 보기가 포함되어 있습니다.
>
> * 이 문서의 코드에서는 단순성 이유로 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 문자열을 일괄 처리로 보내는 것이 좋습니다. 예를 들어 `Sentiment()` 대신 `SentimentBatchAsync()`를 호출합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

> [!NOTE]
> [브라우저에서](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) 이 버전의 Text Analytics 클라이언트 라이브러리를 실행할 수도 있습니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```
### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`@azure/cognitiveservices-textanalytics` NPM 패키지 설치:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

`index.js`라는 파일을 만들고 다음 라이브러리를 추가합니다.

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

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
* [감정 분석](#sentiment-analysis)(공개 미리 보기)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-public-preview)(공개 미리 보기)
* [명명된 엔터티 인식 - 개인 정보](#named-entity-recognition---personal-information-public-preview)(공개 미리 보기)
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="client-authentication"></a>클라이언트 인증

키와 엔드포인트를 매개 변수로 사용하여 새 `TextAnalyticsClient` 개체를 만듭니다.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>감정 분석(공개 미리 보기)

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 감정 분석 v3용입니다.

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `analyzeSentiment()` 메서드를 호출하고 반환된 `SentimentBatchResult` 개체를 가져옵니다. 결과 목록을 반복하고 각 문서의 ID, 문서 수준 감정을 신뢰도 점수로 인쇄합니다. 각 문서에 대해 결과에는 오프셋, 길이 및 신뢰도 점수와 함께 문장 수준 감정이 포함됩니다.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
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
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

## <a name="language-detection"></a>언어 검색

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `detectLanguages()` 메서드를 호출하고 반환된 `DetectLanguageResult`를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID를 기본 언어와 검색된 언어로 인쇄합니다.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>명명된 엔터티 인식

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 명명된 엔터티 인식 v3용입니다.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>명명된 엔터티 인식 - 개인 정보(공개 미리 보기)

> [!NOTE]
> 아래 코드는 공개 미리 보기로 제공되는 명명된 엔터티 인식 v3를 사용하여 개인 정보를 검색하기 위한 것입니다.

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `recognizePiiEntities()` 메서드를 호출하고 `EntitiesBatchResult` 개체를 가져옵니다. 결과 목록을 반복하고 엔터티 이름, 형식, 하위 유형, 오프셋, 길이 및 점수를 인쇄합니다.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>엔터티 연결

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `recognizeLinkedEntities()` 메서드를 호출하고 `RecognizeLinkedEntitiesResult` 개체를 가져옵니다. 결과 목록을 반복하고 엔터티 이름, ID, 데이터 원본, url 및 일치 항목을 인쇄합니다. `matches` 배열의 모든 개체에는 해당 일치 항목에 대한 오프셋, 길이 및 점수가 포함됩니다.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
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
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

분석할 문서가 포함된 문자열 배열을 만듭니다. 클라이언트의 `extractKeyPhrases()` 메서드를 호출하고 반환된 `ExtractKeyPhrasesResult` 개체를 가져옵니다. 결과를 반복하고 각 문서의 ID 및 검색된 주요 문구를 인쇄합니다.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
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

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

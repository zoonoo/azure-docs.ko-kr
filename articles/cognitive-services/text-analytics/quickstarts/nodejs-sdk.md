---
title: '빠른 시작: Node.js용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 구합니다.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: c3cd64d0a683a60132808bca6a7ceb4aa84db4f1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195197"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>빠른 시작: Node.js용 Text Analytics 클라이언트 라이브러리
<a name="HOLTop"></a>

Node.js용 Text Analytics 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. 

Node.js용 Text Analytics 클라이언트 라이브러리를 사용하여 수행하는 작업은 다음과 같습니다.

* 정서 분석
* 언어 검색
* 엔터티 인식
* 핵심 문구 추출

[참조 설명서](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [패키지(NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](../../cognitive-services-apis-create-account.md) 또는 [Azure CLI](../../cognitive-services-apis-create-account-cli.md)를 사용하여 Text Analytics용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 키를 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.  
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져오면 `TEXTANALYTICS_SUBSCRIPTION_KEY`라는 키에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

파일 `index.js`를 만들고 다음 라이브러리를 가져옵니다.

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다. 환경 변수 TEXT_ANALYTICS_SUBSCRIPTION_KEY 및 TEXT_ANALYTICS_ENDPOINT에서 이러한 값을 가져옵니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`@azure/ms-rest-js` 및 `@azure/cognitiveservices-textanalytics` NPM 패키지를 설치합니다.

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다.

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>클라이언트 인증

`credentials` 및 `endpoint`를 매개 변수로 사용하여 새 [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) 개체를 만듭니다.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>정서 분석

분석하려는 문서가 포함된 개체 목록을 만듭니다.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
]}
```

`client.sentiment`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>언어 검색

문서가 포함된 개체 목록을 생성합니다.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
    ]
    };
```

`client.detectLanguage()`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 처음 반환된 언어를 인쇄합니다.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
```

## <a name="entity-recognition"></a>엔터티 인식

문서가 포함된 개체 목록을 생성합니다.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
}
```

`client.entities()`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID를 인쇄합니다. 검색된 각 엔터티의 경우 원본 텍스트의 위치뿐만 아니라 wikipedia 이름, 형식 및 하위 형식(존재하는 경우)을 인쇄합니다.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

문서가 포함된 개체 목록을 생성합니다.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
    ]
    };
```

`client.keyPhrases()`를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 검색된 주요 문구를 인쇄합니다.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

콘솔 창에서 `node index.js`를 사용하여 코드를 실행합니다.

### <a name="output"></a>출력

```console
[
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)에서 확인할 수 있습니다.

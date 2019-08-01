---
title: '빠른 시작: Node.js를 사용하여 텍스트 분석 API 호출'
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 구합니다.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697489"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>빠른 시작: Node.js를 사용하여 텍스트 분석 Cognitive Service 호출
<a name="HOLTop"></a>

이 빠른 시작을 사용하여 Node.js용 Text Analytics SDK를 통해 언어 분석을 시작하세요. [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API는 대부분의 프로그래밍 언어와 호환되는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)에서 확인할 수 있습니다.

API 기술 문서는 [API 정의](//go.microsoft.com/fwlink/?LinkID=759346)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Node.JS](https://nodejs.org/)
* Text Analytics [Node.js용 SDK](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics)이며, 다음을 사용하여 SDK를 설치할 수 있습니다.

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

등록하는 동안 생성된 [엔드포인트 및 액세스 키](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)도 있어야 합니다.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Node.js 애플리케이션 만들기 및 SDK 설치

Node.js를 설치한 후 노드 프로젝트를 만듭니다. 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```mkdir myapp && cd myapp```

package.json 파일을 사용하여 노드 애플리케이션을 만들려면 ```npm init```를 실행합니다. `ms-rest-azure` 및 `azure-cognitiveservices-textanalytics` NPM 패키지를 설치합니다.

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

종속성이 있는 앱의 package.json 파일이 업데이트됩니다.

## <a name="authenticate-your-credentials"></a>자격 증명 인증

프로젝트 루트에 새 파일 `index.js`를 만들고 설치된 라이브러리를 가져옵니다.

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Text Analytics 구독 키에 대한 변수를 만듭니다.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> 프로덕션 시스템에서의 안전한 비밀 배포를 위해 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)를 사용하는 것이 좋습니다.
>

## <a name="create-a-text-analytics-client"></a>Text Analytics 클라이언트 만들기

`credentials`를 매개 변수로 사용하여 새 `TextAnalyticsClient` 개체를 만듭니다. Text Analytics 구독에 맞는 Azure 지역을 사용합니다.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>정서 분석

분석하려는 문서가 포함된 개체 목록을 만듭니다. API에 대한 페이로드는 `id`, `language` 및 `text` 특성이 포함된 `documents` 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
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
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>언어 검색

문서가 포함된 개체 목록을 생성합니다. API에 대한 페이로드는 `id` 및 `text` 특성이 포함된 `documents` 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
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
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>엔터티 인식

문서가 포함된 개체 목록을 생성합니다. API에 대한 페이로드는 `id`, `language` 및 `text` 특성이 포함된 `documents` 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

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
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

문서가 포함된 개체 목록을 생성합니다. API에 대한 페이로드는 `id`, `language` 및 `text` 특성이 포함된 `documents` 목록으로 구성됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
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
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md) [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

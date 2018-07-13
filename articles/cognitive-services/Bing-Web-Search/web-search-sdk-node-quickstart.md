---
title: Web Search SDK Node 빠른 시작 | Microsoft Docs
description: Web Search SDK 콘솔 응용 프로그램을 설치합니다.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377959"
---
# <a name="web-search-sdk-node-quickstart"></a>Web Search SDK Node 빠른 시작

Bing Web Search SDK는 웹 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다.

[Node Bing Web Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성

Bing Web Search SDK를 사용하여 콘솔 응용 프로그램을 설치하려면 개발 환경에서 `npm install azure-cognitiveservices-websearch` 명령을 실행합니다.

## <a name="web-search-client"></a>Web Search 클라이언트
*검색* 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. `CognitiveServicesCredentials` 인스턴스를 만듭니다.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
그런 다음, 클라이언트를 인스턴스화합니다.
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
결과를 검색합니다.
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
코드가 텍스트 구문 분석 없이 `result.value` 항목을 콘솔에 인쇄합니다.  범주별 결과가 있는 경우 결과에 다음이 포함됩니다.
- _type: 'ImageObject'
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>다음 단계

[Cognitive Services Node.js SDK 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

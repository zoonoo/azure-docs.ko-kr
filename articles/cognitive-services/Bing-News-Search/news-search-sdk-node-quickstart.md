---
title: '빠른 시작: 뉴스 검색 수행 - Node.js용 Bing News Search SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Node.js용 Bing News Search SDK를 통해 뉴스를 검색하고 응답을 처리합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0fa78474d6400f890626859c32915c8657f92d4e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249348"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>빠른 시작: Node.js용 Bing News Search SDK로 뉴스 검색 수행

Bing News Search SDK는 뉴스 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다. 

[ Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>애플리케이션 종속성
**검색** 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요. 

Bing News Search SDK를 사용하여 콘솔 애플리케이션을 설치하려면:
* 개발 환경에서 `npm install ms-rest-azure` 명령을 실행합니다.
* 개발 환경에서 `npm install azure-cognitiveservices-newssearch` 명령을 실행합니다.

## <a name="news-search-client"></a>News Search 클라이언트
*검색* 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. `CognitiveServicesCredentials` 인스턴스를 만듭니다.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
그런 다음, 클라이언트를 인스턴스화합니다.
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
이 클라이언트를 사용하여 쿼리 텍스트(이 경우 'Winter Olympics')로 검색합니다.
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
코드가 텍스트 구문 분석 없이 `result.value` 항목을 콘솔에 인쇄합니다. 범주별 결과가 있는 경우 결과에 다음이 포함됩니다.
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>다음 단계

[Cognitive Services Node.js SDK 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

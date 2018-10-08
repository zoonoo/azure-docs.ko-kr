---
title: '빠른 시작: Bing Video Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: Bing Video Search SDK 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225619"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>빠른 시작: Node를 사용하여 Bing Video Search SDK

Bing Video Search SDK는 비디오 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다. 

[Node Bing Video Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성

Bing Video Search SDK를 사용하여 콘솔 응용 프로그램을 설치하려면 개발 환경에서 `npm install azure-cognitiveservices-videosearch` 명령을 실행합니다.

## <a name="video-search-client"></a>Video Search 클라이언트
*검색* 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. `CognitiveServicesCredentials` 인스턴스를 만듭니다.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
그런 다음,클라이언트를 인스턴스화합니다.
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
결과를 검색합니다.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>다음 단계

[Cognitive Services Node.js SDK 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

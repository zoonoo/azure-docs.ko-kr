---
title: Video Search SDK Node 빠른 시작 | Microsoft Docs
description: Video Search SDK 콘솔 응용 프로그램을 설치합니다.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35382973"
---
# <a name="video-search-sdk-node-quickstart"></a>Video Search SDK Node 빠른 시작

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

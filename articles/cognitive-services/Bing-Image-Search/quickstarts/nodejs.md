---
title: '빠른 시작: 이미지 검색 - Bing Image Search REST API 및 Node.js'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 JavaScript를 통해 Bing Image Search REST API로 이미지 검색 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 20db928d0664a94887a6dbeec38815fc02d433c2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383849"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>빠른 시작: Bing Image Search REST API 및 Node.js를 사용하여 이미지 검색

이 빠른 시작을 사용하여 검색 요청을 Bing Image Search API로 보낼 수 있습니다. 이 JavaScript 애플리케이션에서는 검색 쿼리를 API로 보내고 결과에 첫 번째 이미지의 URL을 표시합니다. 이 애플리케이션은 Javascript로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 최신 버전의 [Node.js](https://nodejs.org/en/download/).

* [JavaScript 요청 라이브러리](https://github.com/request/request)  
  [!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 엄격성 및 HTTPS 요구 사항을 설정합니다.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. API 엔드포인트, 이미지 API 검색 경로, 구독 키 및 검색 용어에 대한 변수를 만듭니다.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>검색 요청 및 쿼리를 생성합니다.

1. 마지막 단계에서 변수를 사용하여 API 요청에 대한 검색 URL의 형식을 지정합니다. 검색 용어는 API로 보내기 전에 URL로 인코딩해야 합니다.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. 요청 라이브러리를 사용하여 쿼리를 API에 보냅니다. `response_handler`는 다음 섹션에서 정의됩니다.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>응답 처리 및 구문 분석

1. HTTP 호출, `response`를 매개 변수로 사용하는 `response_handler` 함수를 정의합니다. 이 함수 내에서 다음 단계를 수행합니다.

    1. JSON 응답 본문을 포함할 변수를 정의합니다.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. **data** 플래그가 호출될 때 응답 본문을 저장합니다.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. **end** 플래그 신호가 처리되면 JSON 응답에서 첫 번째 결과를 가져옵니다. 반환된 총 이미지 수와 함께 첫 번째 이미지의 URL을 출력합니다.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>예제 JSON 응답

Bing Image Search API의 응답은 JSON으로 반환됩니다. 이 샘플 응답은 단일 결과를 표시하도록 잘렸습니다.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 앱 만들기](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* Bing Search API에 대한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [무료 Cognitive Services 액세스 키 가져오기](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)

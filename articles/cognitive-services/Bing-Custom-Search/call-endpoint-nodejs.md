---
title: '빠른 시작: Node.js를 사용하여 Bing Custom Search 엔드포인트 호출 | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Node.js로 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 43b46b4dd035efa3117ce23728db42bb11a9ab6c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790285"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Bing Custom Search 엔드포인트 호출

이 빠른 시작을 사용하여 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다. 이 애플리케이션은 JavaScript로 작성되지만 Bing Custom Search API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)에서 자세한 내용을 참조하세요.

- [Node.JS](https://www.nodejs.org/)

- [JavaScript 요청 라이브러리](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 요청 라이브러리에 대해 `require()` 문을 추가합니다. 구독 키, 사용자 지정 구성 ID 및 검색 용어에 대한 변수를 만듭니다. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>검색 요청 보내고 받기 

1. 요청에서 전송되는 정보를 저장할 변수를 만듭니다. 검색 용어를 `q=` 쿼리 매개 변수에 추가하고 검색 인스턴스의 사용자 지정 구성 ID를 `customconfig=`에 추가하여 요청 URL을 생성합니다. 매개 변수를 `&` 문자로 분리합니다. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. JavaScript 요청 라이브러리를 사용하여 Bing Custom Search 인스턴스에 검색 요청을 보내고 이름, URL 및 웹 페이지가 마지막으로 크롤링된 날짜를 비롯한 결과에 대한 정보를 출력합니다.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](./tutorials/custom-search-web-page.md)

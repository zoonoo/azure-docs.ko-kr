---
title: '빠른 시작: 뉴스 검색 수행 - Node.js용 Bing News Search SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Node.js용 Bing News Search SDK를 통해 뉴스를 검색하고 응답을 처리합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf13fe5279be9606197abda624c33dbc4f233b34
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798808"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>빠른 시작: Node.js용 Bing News Search SDK로 뉴스 검색 수행

Node.js용 Bing News Search SDK를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing News Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Node.JS](https://nodejs.org/en/)

Bing News Search SDK를 사용하여 콘솔 애플리케이션을 설치하려면:
1. 개발 환경에서 `npm install ms-rest-azure` 명령을 실행합니다.
2. 개발 환경에서 `npm install azure-cognitiveservices-newssearch` 명령을 실행합니다.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. `CognitiveServicesCredentials` 인스턴스를 만듭니다. 구독 키 및 검색 용어에 대한 변수를 만듭니다.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. 클라이언트를 인스턴스화합니다.
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>검색 쿼리 보내기

1. 클라이언트를 사용하여 쿼리 용어를 검색합니다(이 경우 “Winter Olympics”).
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

코드가 텍스트 구문 분석 없이 `result.value` 항목을 콘솔에 인쇄합니다. 범주별 결과가 있는 경우 결과에 다음이 포함됩니다.

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)

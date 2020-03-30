---
title: Bing Video Search JavaScript 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80289911"
---
JavaScript용 Bing Video Search 클라이언트 라이브러리를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing Video Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, 클라이언트 라이브러리를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)에서 확인할 수 있습니다. 자세한 주석 및 기능이 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Node.JS](https://www.nodejs.org/)

Bing Video Search 클라이언트 라이브러리를 사용하여 콘솔 애플리케이션을 설치하려면 다음을 수행합니다.
* 개발 환경에서 `npm install ms-rest-azure` 명령을 실행합니다.
* 개발 환경에서 `npm install azure-cognitiveservices-videosearch` 명령을 실행합니다.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 Bing Video Search 클라이언트 라이브러리에 대한 `require()` 문 및 `CognitiveServicesCredentials` 모듈을 추가합니다. 구독 키에 대한 변수를 만듭니다. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. 키가 포함된 `CognitiveServicesCredentials` 인스턴스를 만듭니다. 그런 다음, 이를 사용하여 비디오 검색 클라이언트의 인스턴스를 만듭니다.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>검색 요청 보내기

1. `client.videosOperations.search()`를 사용하여 Bing Video Search API로 검색 요청을 보냅니다. 검색 결과가 반환된 경우 `.then()`을 사용하여 결과를 기록합니다.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

* [Bing Video Search API란?](../../overview.md)
* [Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
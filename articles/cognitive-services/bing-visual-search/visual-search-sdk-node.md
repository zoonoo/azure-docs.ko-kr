---
title: '빠른 시작: Bing Visual Search SDK for Node.js를 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: Bing Visual Search SDK를 사용하여 이미지를 업로드하고 이미지에 대한 인사이트를 가져올 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: aahi
ms.openlocfilehash: 6f7740ebd09a1fde564613c38ab12513ca51fece
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204905"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>빠른 시작: Bing Visual Search SDK for Node.js를 사용하여 이미지 인사이트 가져오기

이 빠른 시작의 안내에 따라 Node.js SDK를 사용하여 Bing Visual Search 서비스에서 이미지 인사이트 가져오기를 시작하세요. Bing Visual Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js)에서 확인할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
* [Node.JS](https://www.nodejs.org/)
* Bing Visual Search SDK for Node.js
    * Bing Visual Search SDK를 사용하여 콘솔 애플리케이션을 설치하려면 다음 명령을 실행합니다.
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고, 다음 요구 사항을 추가합니다. 구독 키, 사용자 지정 구성 ID 및 업로드할 이미지의 파일 경로에 대한 변수를 만듭니다. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. 클라이언트를 인스턴스화합니다.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>이미지 검색

1. `fs.createReadStream()` 명령을 사용하여 이미지 파일을 읽어 들이고, 검색 요청 및 결과에 대한 변수를 만듭니다. 클라이언트를 사용하여 이미지를 검색합니다.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. 이전 쿼리의 결과를 구문 분석합니다.

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](tutorial-bing-visual-search-single-page-app.md)

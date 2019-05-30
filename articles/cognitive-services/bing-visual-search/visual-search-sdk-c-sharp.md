---
title: '빠른 시작: Bing Visual Search SDK for C#을 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: Bing Visual Search SDK를 사용하여 이미지를 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: v-gedod
ms.openlocfilehash: 9fd693207b429ddc0a2bfdd772f4822f188ac3f7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884935"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>빠른 시작: Bing Visual Search SDK for C#을 사용하여 이미지 인사이트 가져오기

이 빠른 시작의 안내에 따라 C# SDK를 사용하여 Bing Visual Search 서비스에서 이미지 인사이트 가져오기를 시작하세요. Bing Visual Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.
* NuGet Visual Search 패키지입니다. 
    - Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 메뉴에서 `Manage NuGet Packages`를 선택합니다. `Microsoft.Azure.CognitiveServices.Search.VisualSearch` 패키지를 설치합니다. NuGet 패키지를 설치하면 다음도 설치됩니다.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. Visual Studio에서 새 프로젝트를 만듭니다. 그런 다음, 다음 지시문을 추가합니다.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. 구독 키를 사용하여 클라이언트를 인스턴스화합니다.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>검색 요청 보내기 

1. 이미지에 대한 `FileStream`을 만듭니다(이 경우 `TestImages/image.jpg`). 그런 다음, 클라이언트를 사용하여 `client.Images.VisualSearchMethodAsync()`를 통해 검색 요청을 보냅니다. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. 이전 쿼리의 결과를 구문 분석합니다.

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](tutorial-bing-visual-search-single-page-app.md)

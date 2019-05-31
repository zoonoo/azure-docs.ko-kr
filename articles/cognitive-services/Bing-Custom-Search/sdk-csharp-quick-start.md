---
title: '빠른 시작: C# SDK를 사용하여 Bing Custom Search 엔드포인트 호출 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Custom Search SDK C# 콘솔 애플리케이션을 설치합니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 9e13edce77819d5ef8cfc3b6becff9fb82224a83
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595953"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>빠른 시작: C# SDK를 사용하여 Bing Custom Search 엔드포인트 호출 

이 빠른 시작을 사용하여 C# SDK로 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다. Bing Custom Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 Bing Custom Search SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)에서 자세한 내용을 참조하세요.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)의 모든 버전
- Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.
- [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) 패키지 설치. 
    - Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **NuGet 패키지 관리**를 선택합니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지를 설치합니다. NuGet Custom Search 패키지를 설치하면 다음 어셈블리도 함께 설치됩니다.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. Visual Studio를 사용하여 새 C# 콘솔 애플리케이션을 만듭니다. 그런 다음, 프로젝트에 다음 패키지를 추가합니다.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. 애플리케이션의 main 메서드에서 API 키를 사용하여 검색 클라이언트를 인스턴스화합니다.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>검색 요청을 보내고 응답을 수신
    
1. 클라이언트의 `SearchAsync()` 메서드를 사용하여 검색 쿼리를 보내고 응답을 저장합니다. 잊지 말고 `YOUR-CUSTOM-CONFIG-ID`를 인스턴스의 구성 ID로 바꾸세요(ID는 [Bing Custom Search 포털](https://www.customsearch.ai/)에서 찾을 수 있음). 이 예제는 "Xbox"를 검색합니다.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` 메서드는 `WebData` 개체를 반환합니다. 개체를 사용하여 발견한 `WebPages`를 반복합니다. 이 코드는 첫 번째 웹 페이지 결과를 찾아서 웹 페이지의 `Name` 및 `URL`을 인쇄합니다.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)

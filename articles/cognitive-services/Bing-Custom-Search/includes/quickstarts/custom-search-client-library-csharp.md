---
title: Bing Custom Search C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: b722fd34a78f1e9c2f4a660c205cf4a1e163a5d7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253089"
---
C#용 Bing Custom Search 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Bing Custom Search API를 사용하면 관심 있는 토픽에 대한 맞춤형 광고 없는 검색 경험을 만들 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)에서 확인할 수 있습니다.

C#용 Bing Custom Search 클라이언트 라이브러리를 사용하여 다음을 수행합니다.
* Bing Custom Search 인스턴스에서 웹의 검색 결과를 찾습니다.

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>사전 요구 사항

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](../../quick-start.md)에서 자세한 내용을 참조하세요.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)의 모든 버전
- Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.
- [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet 패키지. 
    - Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **NuGet 패키지 관리**를 선택합니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지를 설치합니다. NuGet Custom Search 패키지를 설치하면 다음 어셈블리도 함께 설치됩니다.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
> [Build a Custom Search web app](../../tutorials/custom-search-web-page.md)

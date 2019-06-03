---
title: '빠른 시작: C#을 사용하여 Bing Custom Search 엔드포인트 호출 | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 C#에서 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 267b50e15d39fc5a0df763cea2e2b79f9b23d151
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595814"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>빠른 시작: C#을 사용하여 Bing Custom Search 엔드포인트 호출 

이 빠른 시작을 사용하여 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다. 이 애플리케이션은 C#으로 작성되지만 Bing Custom Search API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)에서 확인할 수 있습니다.

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

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. Visual Studio를 사용하여 새 C# 콘솔 애플리케이션을 만듭니다. 그런 다음, 프로젝트에 다음 패키지를 추가합니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Bing Custom Search API에서 반환된 검색 결과를 저장하는 다음 클래스를 만듭니다.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. 프로젝트의 기본 메서드에서 Bing Custom Search API 구독 키, 검색 인스턴스의 사용자 지정 구성 ID 및 검색어에 대한 변수를 만듭니다.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. 검색어를 `q=` 쿼리 매개 변수에 추가하고 검색 인스턴스의 사용자 지정 구성 ID를 `customconfig=`에 추가하여 요청 URL을 구성합니다. 매개 변수를 `&` 문자로 분리합니다. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>검색 요청 보내고 받기 

1. 요청 클라이언트를 만들고 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. 검색 요청을 수행하고 응답을 JSON 개체로 가져옵니다.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>결과 처리 및 보기

1. 응답 개체를 반복하여 해당 이름, URL 및 웹 페이지가 마지막으로 크롤링된 날짜를 포함하는 각 검색 결과에 대한 정보를 표시합니다.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](./tutorials/custom-search-web-page.md)

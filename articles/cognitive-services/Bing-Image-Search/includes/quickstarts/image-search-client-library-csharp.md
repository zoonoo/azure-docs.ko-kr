---
title: Bing Image Search C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 122e44da7bbf4229f932eefdae4c70dc49f43bfe
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371286"
---
이 빠른 시작을 통해 Bing Image Search 클라이언트 라이브러리를 사용하여 첫 번째 이미지 검색을 수행합니다. 

클라이언트 검색 라이브러리는 REST API에 대한 래퍼로, 동일한 기능을 포함합니다. 

C# 애플리케이션을 만들어 이미지 검색 쿼리를 보내고, JSON 응답을 구문 분석하고, 반환된 첫 번째 이미지의 URL을 표시합니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Windows를 사용하는 경우 [Visual Studio 2017 이상](https://visualstudio.microsoft.com/vs/whatsnew/)의 모든 버전
* macOS 또는 Linux를 사용하는 경우 [.NET Core가 설치된](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install) [VS Code](https://code.visualstudio.com)
* [평가판 Azure 구독](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="create-a-console-project"></a>콘솔 프로젝트 만들기

먼저 새 C# 콘솔 애플리케이션을 만듭니다.

## <a name="create-a-console-project"></a>콘솔 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Visual Studio에서 *BingImageSearch* 라는 새 콘솔 솔루션을 만듭니다.
    
1. [Cognitive Image Search NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) 추가
    1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
    1. **NuGet 패키지 관리** 를 선택합니다.
    1. *Microsoft.Azure.CognitiveServices.Search.ImageSearch* 를 검색하여 선택한 다음, 패키지를 설치합니다.
    
# <a name="vs-code"></a>[VS 코드](#tab/vscode)

1. VS Code에서 터미널 창을 엽니다.
1. 터미널 창에서 다음 코드를 입력하여 *BingImageSearch* 라는 새 콘솔 프로젝트를 만듭니다.
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. VS Code에서 *BingImageSearch* 폴더를 엽니다.
1. 터미널 창에 다음 코드를 입력하여 [Cognitive Image Search NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) NuGetPackage를 추가합니다.

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>애플리케이션 초기화


1. *Program.cs* 의 모든 `using` 문을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. 프로젝트의 `Main` 메서드에서 유효한 구독 키에 대한 변수, Bing에서 반환할 이미지 및 검색어를 만듭니다. 그런 다음, 키를 사용하여 이미지 검색 클라이언트를 인스턴스화합니다.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>클라이언트를 사용하여 검색 쿼리 보내기
    
여전히 `Main` 메서드에서 클라이언트를 사용하여 쿼리 텍스트를 검색합니다.
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>첫 번째 이미지 결과 구문 분석 및 보기

응답에서 반환된 이미지 결과를 구문 분석합니다. 

응답에 검색 결과가 포함된 경우 첫 번째 결과를 저장하고 세부 정보 중 일부를 출력합니다.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK에 대한 .Net 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)

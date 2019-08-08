---
title: ImageInsightsToken을 사용하여 이전 검색에서 유사한 이미지 찾기 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: ImageInsightsToken에서 지정한 이미지의 URL을 가져오기 위해 Bing Visual Search SDK를 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 0db87c221c36a3473d457e71bcf098d016949797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827781"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>ImageInsightsToken을 사용하여 이전 검색에서 유사한 이미지 찾기

Visual Search SDK를 통해 `ImageInsightsToken`을 반환하는 이전 검색에서 온라인으로 이미지를 찾을 수 있습니다. 이 애플리케이션은 `ImageInsightsToken`을 가져와 후속 검색에서 토큰을 사용합니다. 그런 다음를 Bing `ImageInsightsToken` 에 전송 하 고 온라인에서 찾은 유사한 이미지의 url 및 url Bing Search 포함 하는 결과를 반환 합니다.

이 자습서의 전체 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs)의 추가 오류 처리 및 주석과 함께 찾을 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Visual Studio 2019](https://www.visualstudio.com/downloads/)의 모든 버전.
* Linux/MacOS를 사용 하는 경우 [Mono](https://www.mono-project.com/)를 사용 하 여이 응용 프로그램을 실행할 수 있습니다.
* NuGet Visual Search 및 Image Search 패키지입니다.
    - Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 메뉴에서 **NuGet 패키지 관리** 를 선택 합니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지 및 `Microsoft.Azure.CognitiveServices.Search.ImageSearch` 패키지를 차례로 설치합니다. NuGet 패키지를 설치하면 다음도 설치됩니다.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Bing Image Search SDK에서 ImageInsightsToken 가져오기

이 애플리케이션은 [Bing Image Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)를 통해 획득한 `ImageInsightsToken`을 사용합니다. 새 C# 콘솔 애플리케이션에서 클라이언트를 만들어 `ImageSearchClient()`를 사용하는 API를 호출합니다. 그런 다음 `SearchAsync()` 쿼리와 함께를 사용 합니다.

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

`imageResults.Value.First()`를 사용하여 첫 번째 검색 결과를 저장한 다음, 이미지 인사이트의 `ImageInsightsToken`을 저장합니다.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

요청에 Bing Visual Search 전송 됩니다.`ImageInsightsToken`

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Visual Search 요청에 ImageInsightsToken 추가

Bing Visual Search의 응답에 포함된 `ImageInsightsToken`에서 `ImageInfo` 개체를 만들어 Visual Search 요청에 대한 `ImageInsightsToken`을 지정합니다.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Bing Visual Search를 사용하여 ImageInsightsToken에서 이미지 찾기

`VisualSearchRequest` 개체는 검색할 `ImageInfo`의 이미지에 대한 정보를 포함합니다. `VisualSearchMethodAsync()` 메서드가 결과를 가져옵니다. 이미지는 토큰으로 표시되므로 이미지 이진을 제공할 필요가 없습니다.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Visual Search 결과 반복

Visual Search 결과는 `ImageTag` 개체입니다. 각 태그에는 `ImageAction` 개체의 목록이 포함됩니다. 각 `ImageAction` 에는 `Data` 동작의 유형에 따라 달라 지는 값 목록으로 필드를 포함 합니다. 예를 들어 `visualSearchResults.Tags`에서 `ImageTag` 개체를 반복하고, `ImageAction` 태그를 가져올 수 있습니다. 아래 샘플에서는 작업의 `PagesIncluding` 세부 정보를 인쇄 합니다.

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

작업 유형에서 실제 이미지 URL을 가져오려면 값 목록이 있는 `Data` 요소를 포함하는 `ImageModuleAction`으로 `ActionType`을 읽는 캐스트가 필요합니다. 각 값은 이미지의 URL입니다.  다음은 `PagesIncluding` 동작 유형을로 `ImageModuleAction` 캐스팅 하 고 값을 읽습니다.

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

이러한 데이터 형식에 대한 자세한 내용은 [이미지 - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)를 참조합니다.

## <a name="returned-urls"></a>반환된 URL

전체 애플리케이션은 다음 URL을 반환합니다.

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

위에 표시된 대로 `TopicResults` 및 `ImageResults` 유형에는 관련 이미지에 대한 쿼리가 포함됩니다. Bing Search 결과에 대한 URL 링크입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Visual Search 단일 페이지 웹앱 만들기](tutorial-bing-visual-search-single-page-app.md)

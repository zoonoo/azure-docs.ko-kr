---
title: '자습서: ImageInsightsToken - Bing Visual Search'
titlesuffix: Azure Cognitive Services
description: ImageInsightsToken에서 지정한 이미지의 URL을 가져오기 위해 Bing Visual Search SDK를 사용하는 방법.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 62780500d29c891182d3869bf0ba3ccdc5e2f715
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441064"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>자습서: Bing Visual Search SDK ImageInsightsToken 및 결과
Visual Search SDK에는 `ImageInsightsToken`을 반환하는 이전 검색에서 온라인으로 이미지를 찾는 옵션이 포함됩니다.  이 예제는 `ImageInsightsToken`을 가져와 후속 검색에서 토큰을 사용합니다.  코드는 `ImageInsightsToken`을 Bing에 보내고 Bing Search URL 및 온라인에서 찾은 비슷한 이미지의 URL을 포함하는 결과를 반환합니다.

## <a name="prerequisites"></a>필수 조건
Visual Studio 2017. 필요하면 여기(https://www.visualstudio.com/vs/community/)에서 무료 커뮤니티 버전을 다운로드할 수 있습니다.
Cognitive Services API 키는 SDK 호출을 인증하는 데 필요합니다. 평가판 키에 등록합니다. 평가판 키는 초당 한 번의 호출로 7일 동안 유효합니다. 프로덕션 시나리오의 경우 액세스 키를 구입하세요. 가격 책정 정보도 참조하세요.
.NET core SDK 및 .net core 1.1 앱을 실행하는 기능입니다. 여기(https://www.microsoft.com/net/download/)에서 코어, 프레임워크 및 런타임을 가져올 수 있습니다.

이 자습서의 경우 [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/)에 표시된 대로 S9 가격 계층에서 구독을 시작해야 합니다. 

Azure Portal에서 구독을 시작하려면
1. `Search resources, services, and docs`라고 표시되는 Azure Portal의 맨 위에 있는 텍스트 상자에 'BingSearchV7'을 입력합니다.  
2. 드롭다운 목록의 Marketplace 아래에서 `Bing Search v7`을 선택합니다.
3. 새 리소스에 대해 `Name`을 입력합니다.
4. `Pay-As-You-Go` 구독을 선택합니다.
5. `S9` 가격 책정 계층을 선택합니다.
6. `Enable`을 클릭하여 구독을 시작합니다.

## <a name="application-dependencies"></a>애플리케이션 종속성
Bing Web Search SDK를 사용하여 콘솔 애플리케이션을 설정하려면 Visual Studio의 솔루션 탐색기에서 NuGet 패키지 관리 옵션을 찾습니다. 추가:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage 패키지입니다.

NuGet Web Search SDK 패키지 설치는 다음을 포함한 종속성도 설치합니다.

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Image Search에서 ImageInsightsToken 가져오기

이 예제는 다음 방법으로 얻은 `ImageInsightsToken`을 사용합니다.  이 호출에 대한 자세한 내용은 [Image Search SDK C# 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)을 참조합니다.

코드는 'Canadian Rockies'에 대한 쿼리의 결과를 검색하고 ImageInsightsToken을 가져옵니다. 첫 번째 이미지의 인사이트 토큰, 썸네일 URL 및 이미지 콘텐츠 URL을 인쇄합니다.  메서드는 후속 Visual Search 요청에서 사용하기 위해 `ImageInsightsToken`을 반환합니다.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Visual Search 요청에 대해 ImageInsightsToken 지정

이 예제에서는 이전 메서드에서 반환한 인사이트 토큰을 사용합니다. 다음 코드는 `ImageInsightsToken`에서 `ImageInfo` 개체를 만들고 ImageInfo 개체를 `VisualSearchRequest`로 로드합니다. `VisualSearchRequest`에 대한 `ImageInfo`에서 `ImageInsightsToken` 지정

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>ImageInsightsToken에서 이미지를 찾으려면 Visual Search 사용

`VisualSearchRequest`는 `ImageInfo` 개체에서 검색할 이미지에 대한 정보를 포함합니다.  `VisualSearchMethodAsync` 메서드가 결과를 가져옵니다.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>ImageModuleAction에서 URL 데이터 가져오기
Visual Search 결과는 `ImageTag` 개체입니다.  각 태그에는 `ImageAction` 개체의 목록이 포함됩니다.  각 `ImageAction`에는 작업 유형에 종속되는 값의 목록인 `Data` 필드가 포함됩니다.

다음 코드를 사용하여 다양한 유형을 가져올 수 있습니다.
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
전체 응용 프로그램이 반환됩니다.

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

앞의 목록에 표시된 대로 `TopicResults` 및 `ImageResults` 유형은 관련 이미지에 대한 쿼리를 포함합니다. Bing 검색 결과에 대한 목록 링크의 URL입니다.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Visual Search로 찾은 이미지의 PagesIncluding ActionType URL

실제 이미지 URL 가져오기는 `ActionType`을 `ImageModuleAction`으로 읽으며 값의 목록이 있는 `Data` 요소를 포함하는 캐스트가 필요합니다.  각 값은 이미지의 URL입니다.  다음은 `PagesIncluding` 동작 유형을 `ImageModuleAction`으로 캐스트하고 값을 읽습니다.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
이러한 데이터 형식에 대한 자세한 내용은 [이미지 - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)를 참조합니다.

## <a name="complete-code"></a>전체 코드

다음 코드는 앞의 예제를 실행합니다. 후속 요청에서 `ImageInsightsToken`을 보냅니다. 그런 다음, 각 ActionType에 대한 Bing 검색 URL을 인쇄합니다. ActionType이 `PagesIncluding`이면 코드는 `Data`에서 `ImageObject` 항목을 가져옵니다.  `Data`는 웹 페이지의 이미지 URL인 값의 목록을 포함합니다.  결과 Visual Search URL을 복사하여 브라우저에 붙여넣어 결과를 표시합니다. ContentUrl 항목을 복사하여 브라우저에 붙여넣어 이미지를 표시합니다.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

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

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```

## <a name="next-steps"></a>다음 단계
[Visual Search 응답](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)

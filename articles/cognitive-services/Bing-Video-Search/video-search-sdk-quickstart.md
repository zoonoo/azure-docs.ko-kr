---
title: '빠른 시작: Bing Video Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Bing Video Search SDK 콘솔 애플리케이션을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 01/29/2018
ms.author: rosh
ms.openlocfilehash: bf8eece4b5afe34635d80a57cc12c26a8ed157d6
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314135"
---
# <a name="quickstart-bing-video-search-sdk-with-c"></a>빠른 시작: C#을 사용한 Bing Video Search SDK 

Bing Video Search SDK는 웹 요청 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다.

[C# Bing Video Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>애플리케이션 종속성
**검색** 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

Bing Video Search SDK를 사용하여 콘솔 애플리케이션을 설정하려면 Visual Studio의 솔루션 탐색기에서 `Manage NuGet Packages` 옵션을 찾습니다.  `Microsoft.Azure.CognitiveServices.Search.VideoSearch` 패키지를 추가합니다.

[[NuGet Video Search SDK 패키지]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)를 설치하면 다음을 포함한 종속성도 설치됩니다.
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json


## <a name="video-search-client"></a>Video Search 클라이언트
`VideoSearchAPI` 클라이언트의 인스턴스를 만들려면 Using 지시문을 추가합니다.
```
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

```
그런 다음, 클라이언트를 인스턴스화합니다.
```
var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
이 클라이언트를 사용하여 쿼리 텍스트 "SwiftKey"를 사용하여 비디오를 검색합니다.
```
var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
Console.WriteLine("Search videos for query \"SwiftKey\"");

```

결과를 구문 분석하고, 결과의 수를 확인한 후, 첫 번째 비디오 결과의 ID, 이름 및 URL을 출력합니다.
```
if (videoResults == null)
{
    Console.WriteLine("Didn't see any video result data..");
}
else
{
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value.First();

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
}

```
## <a name="complete-console-application"></a>전체 콘솔 애플리케이션

다음 콘솔 애플리케이션은 이전에 정의된 쿼리를 실행하고 결과를 구문 분석합니다.

```
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

namespace VideoSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("19aa718a79d6444daaa415981d9f54ad"));

            try
            {
                var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
                Console.WriteLine("Search videos for query \"SwiftKey\"");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these calls to use queries defined under the following headings.
            //VideoSearchWithFilters(client);
            //VideoDetail(client);
            //VideoTrending(client);


            Console.WriteLine("Any key to exit...");
            Console.ReadKey();

        }

```
## <a name="url-parameters"></a>URL 매개 변수

변경되지 않았고 짧으며 1080p 해상도를 갖는 비디오를 쿼리 텍스트 "Bellevue Trailer"에 대해 검색합니다.  결과의 수를 확인한 후, 첫 번째 비디오 결과의 ID, 이름 및 URL을 출력합니다.

```
        public static void VideoSearchWithFilters(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer", pricing: VideoPricing.Free, length: VideoLength.Short, resolution: VideoResolution.HD1080p).Result;
                Console.WriteLine("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="trending-videos"></a>최신 비디오
최신 비디오를 검색한 후 배너 타일 및 범주를 확인합니다.
```
        public static void VideoTrending(VideoSearchAPI client)
        {
            try
            {
                var trendingResults = client.Videos.TrendingAsync().Result;
                Console.WriteLine("Search trending videos");

                if (trendingResults == null)
                {
                    Console.WriteLine("Didn't see any trending video data..");
                }
                else
                {
                    // Banner Tiles
                    if (trendingResults.BannerTiles?.Count > 0)
                    {
                        var firstBannerTile = trendingResults.BannerTiles[0];
                        Console.WriteLine($"\r\nBanner tile count: {trendingResults.BannerTiles.Count}");
                        Console.WriteLine($"First banner tile text: {firstBannerTile.Query.Text}");
                        Console.WriteLine($"First banner tile url: {firstBannerTile.Query.WebSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find banner tiles!");
                    }

                    // Categories
                    if (trendingResults.Categories?.Count > 0)
                    {
                        var firstCategory = trendingResults.Categories[0];
                        Console.WriteLine($"Category count: {trendingResults.Categories.Count}");
                        Console.WriteLine($"First category title: {firstCategory.Title}");

                        if (firstCategory.Subcategories?.Count > 0)
                        {
                            var firstSubCategory = firstCategory.Subcategories[0];
                            Console.WriteLine($"SubCategory count: {firstCategory.Subcategories.Count}");
                            Console.WriteLine($"First sub category title: {firstSubCategory.Title}");

                            if (firstSubCategory.Tiles?.Count > 0)
                            {
                                var firstTile = firstSubCategory.Tiles[0];
                                Console.WriteLine($"Tile count: {firstSubCategory.Tiles.Count}");
                                Console.WriteLine($"First tile text: {firstTile.Query.Text}");
                                Console.WriteLine($"First tile url: {firstTile.Query.WebSearchUrl}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tiles!");
                            }
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find subcategories!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find categories!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="details"></a>세부 정보
“Bellevue Trailer”에 대한 비디오를 검색하고 첫 번째 비디오에 대한 세부 정보를 검색합니다.
```
        public static void VideoDetail(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer").Result;

                var firstVideo = videoResults?.Value?.FirstOrDefault();

                if (firstVideo != null)
                {
                    var modules = new List<VideoInsightModule?>() { VideoInsightModule.All };
                    var videoDetail = client.Videos.DetailsAsync(query: "Bellevue Trailer", id: firstVideo.VideoId, modules: modules).Result;
                    Console.WriteLine($"Search detail for video id={firstVideo.VideoId}, name={firstVideo.Name}");

                    if (videoDetail != null)
                    {
                        if (videoDetail.VideoResult != null)
                        {
                            Console.WriteLine($"\r\nExpected video id: {videoDetail.VideoResult.VideoId}");
                            Console.WriteLine($"Expected video name: {videoDetail.VideoResult.Name}");
                            Console.WriteLine($"Expected video url: {videoDetail.VideoResult.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find expected video!");
                        }

                        if (videoDetail?.RelatedVideos?.Value?.Count > 0)
                        {
                            var firstRelatedVideo = videoDetail.RelatedVideos.Value[0];
                            Console.WriteLine($"Related video count: {videoDetail.RelatedVideos.Value.Count}");
                            Console.WriteLine($"First related video id: {firstRelatedVideo.VideoId}");
                            Console.WriteLine($"First related video name: {firstRelatedVideo.Name}");
                            Console.WriteLine($"First related video url: {firstRelatedVideo.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any related video!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find detail about the video!");
                    }
                }
                else
                {
                    Console.WriteLine("Couldn't find video results!");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }

```

## <a name="next-steps"></a>다음 단계

[Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

---
title: '빠른 시작: 뉴스 검색 수행 - C#용 Bing News Search SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python용 Bing News Search SDK를 통해 뉴스를 검색하고 응답을 처리합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 5b3e68765fbcff12dcb5337aec38623b8994882c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156802"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>빠른 시작: C#용 Bing News Search SDK로 뉴스 검색 수행

Bing News Search SDK는 뉴스 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다. 

[C# Bing News Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>애플리케이션 종속성
**검색** 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.  

Bing News Search SDK를 사용하여 콘솔 애플리케이션을 설정하려면 Visual Studio의 솔루션 탐색기에서 `Manage NuGet Packages` 옵션을 찾습니다.  `Microsoft.Azure.CognitiveServices.Search.NewsSearch` 패키지를 추가합니다.

[NuGet News Search SDK 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)를 설치하면 다음을 포함한 종속성도 설치됩니다.
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>News Search 클라이언트
`NewsSearchClient` 인스턴스를 만들려면 using 지시문을 추가합니다.
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
그런 다음, 클라이언트를 인스턴스화합니다.
```
var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
쿼리 텍스트로 검색하려면 클라이언트를 사용합니다.
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
이전 쿼리의 결과에 반환된 뉴스를 구문 분석합니다.
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value[0];

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>전체 콘솔 애플리케이션

다음 콘솔 애플리케이션은 이전에 정의된 쿼리를 실행하고 "Quantum Computing"에 대한 뉴스를 검색합니다. 요청에는 `market` 및 `count` 매개 변수가 포함됩니다. 이 코드는 결과의 수를 확인하고 첫 번째 뉴스 결과의 `totalEstimatedMatches`, `name`, `url`, `description`, `published time` 및 `provider` `name`을 출력합니다.

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>Recent news, freshness 및 sortBy 매개 변수
다음 코드에서는 `freshness` 및 `sortBy` 매개 변수를 사용하여 "Artificial Intelligence"에 대한 최신 뉴스를 검색합니다. 이 코드는 결과의 수를 확인하고 첫 번째 뉴스 결과의 `totalEstimatedMatches`, `name`, `url`, `description`, `published time` 및 공급자 `name`을 출력합니다.
```
        public static void NewsSearchWithFilters(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>범주 뉴스, 안전 검색
다음 코드에서는 안전 검색을 사용하여 동영상 및 TV 엔터테인먼트에 대한 범주 뉴스를 검색합니다.  이 코드는 결과의 수를 확인하고 첫 번째 뉴스 결과의 `category`, `name`, `url`, `description`, `published time` 및 공급자 `name`을 출력합니다.
```
        public static void NewsCategory(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>최신 항목
다음 코드에서는 Bing에서 최신 뉴스 항목을 검색합니다. 이 코드는 결과의 수를 확인하고 첫 번째 뉴스 결과의 `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` 및 `image.Url`을 출력합니다.
```
        public static void TrendingTopics(NewsSearchClient client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>다음 단계

[Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

---
title: Custom Search SDK C# 빠른 시작 | Microsoft Docs
titleSuffix: Cognitive Services
description: Custom Search SDK C# 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372791"
---
# <a name="custom-search-sdk-c-quickstart"></a>Custom Search SDK C# 빠른 시작

Bing Custom Search SDK는 엔터티 검색 및 결과 구문 분석을 위한 REST API 기능을 포함하고 있습니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성

Bing Custom Search SDK를 사용하여 콘솔 응용 프로그램을 설정하려면 Visual Studio의 솔루션 탐색기에서 `Manage NuGet Packages` 옵션을 찾습니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지를 추가합니다.

[NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) 패키지를 설치하면 다음 어셈블리를 포함한 종속성도 설치됩니다.
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Entity Search 클라이언트

CustomSearchAPI 클라이언트의 인스턴스를 만들려면 using 지시문을 추가합니다.
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

사용자 지정 검색 클라이언트 인스턴스화: `YOUR-CUSTOM-SEARCH-KEY` 및 `YOUR-CUSTOM-CONFIG-ID`를 [내 인스턴스](https://www.customsearch.ai/)에 할당된 액세스 키 및 API 엔드포인트 구성 ID로 바꿉니다.
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
쿼리 텍스트로 검색하려면 클라이언트를 사용합니다.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>결과 구문 분석

쿼리에 대한 항목이 발견되면 `SearchAsync` 메서드는 `WebPages`를 포함하는 `WebData` 개체를 반환합니다. 이 코드는 첫 번째 결과를 찾아서 `Name` 및 `URL`을 가져옵니다.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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

```
## <a name="complete-console-application"></a>전체 콘솔 응용 프로그램

다음 코드는 "Xbox" 쿼리를 검색하여 첫 번째 웹 결과의 `Name` 및 `URL`을 출력합니다.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>다음 단계

[Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

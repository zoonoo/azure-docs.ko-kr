---
title: '빠른 시작: Custom Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Custom Search SDK C# 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 7ac298ad5c5b93b5dce0ce2dd59ffe541888db88
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307766"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>빠른 시작: C#을 통해 Bing Custom Search SDK 설정

Bing Custom Search SDK는 Bing Custom Search REST API보다 쉬운 프로그래밍 모델을 제공합니다. 이 섹션에서는 C# SDK를 사용하여 첫 번째 사용자 지정 검색 호출을 만드는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 바로 사용할 수 있는 사용자 지정 검색 인스턴스. [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.  
  
- 구독 키 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)을 정품 인증하면 구독 키를 받을 수 있습니다. Azure 대시보드에서 유료 구독 키를 사용할 수도 있습니다([Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 참조).  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.
  
- Visual Studio 2017 설치. 아직 Visual Studio 2017이 없는 경우 **무료** [Visual Studio 2017 Community 버전](https://www.visualstudio.com/downloads/)을 다운로드할 수 있습니다.  
  
- [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) 패키지 설치. Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 메뉴에서 `Manage NuGet Packages`를 선택합니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지를 설치합니다.

NuGet Custom Search 패키지를 설치하면 다음 어셈블리도 함께 설치됩니다.

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>코드 실행

> [!TIP]
> [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)에서 최신 코드를 Visual Studio 솔루션으로 가져옵니다.

이 예제를 실행하려면 다음 단계를 수행합니다.

1. Visual Studio 2017을 엽니다.
  
2. **파일** 메뉴를 클릭하고 **새로 만들기**, **프로젝트**, **Visual C# 콘솔 응용 프로그램** 템플릿을 차례로 클릭합니다.
  
3. CustomSearchSolution 솔루션의 이름을 지정하고 솔루션을 배치할 폴더로 이동합니다.
  
4. [확인]을 클릭하여 솔루션을 만듭니다.  
  
4. 솔루션 탐색기에서 프로젝트(솔루션과 같은 이름)를 마우스 오른쪽 단추로 클릭하고 `Manage NuGet Packages`를 선택합니다. NuGet 패키지 관리자에서 **찾아보기**를 클릭합니다. 검색 상자에 Microsoft.Azure.CognitiveServices.Search.CustomSearch를 입력하고 Eenter 키를 누릅니다. 패키지를 선택하고 [설치]를 클릭합니다.  
  
4. Program.cs 파일에 다음 코드를 복사합니다. **YOUR-SUBSCRIPTION-KEY** 및 **YOUR-CUSTOM-CONFIG-ID**를 구독 키와 구성 ID로 바꿉니다.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

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
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. 솔루션을 빌드하고 실행(디버그)합니다. 




## <a name="breaking-it-down"></a>세분화

NuGet Custom Search 패키지를 설치한 후에는 using 지시문을 추가해야 합니다.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

그 다음에는 검색 요청을 만들 때 사용하는 사용자 지정 검색 클라이언트를 인스턴스화합니다. `YOUR-SUBSCRIPTION-KEY`를 고유의 키로 바꿉니다.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

이제 클라이언트를 사용하여 검색 요청을 보낼 수 있습니다. 잊지 말고 `YOUR-CUSTOM-CONFIG-ID`를 인스턴스의 구성 ID로 바꾸세요(ID는 [Custom Search 포털](https://www.customsearch.ai/)에서 찾을 수 있음). 이 예제는 Xbox를 검색합니다. 필요에 따라 업데이트합니다.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync` 메서드는 `WebData` 개체를 반환합니다. `WebData`를 사용하여 발견한 `WebPages`를 반복합니다. 이 코드는 첫 번째 웹 페이지 결과를 찾아서 웹 페이지의 `Name` 및 `URL`을 인쇄합니다.

```csharp
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


## <a name="next-steps"></a>다음 단계

SDK 샘플을 확인하세요. 각 샘플에는 필수 구성 요소 및 샘플 설치/실행에 대한 세부 정보가 있는 ReadMe 파일이 포함되어 있습니다.

* [.NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 시작 
    * [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * 정의 및 종속성은 [.NET 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch)도 참조하세요.
* [NodeJS 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 시작 
    * 정의 및 종속성은 [NodeJS 라이브러리](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch)도 참조하세요.
* [Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 시작 
    * 정의 및 종속성은 [Java 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch)도 참조하세요.
* [Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 시작 
    * 정의 및 종속성은 [Python 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch)도 참조하세요.


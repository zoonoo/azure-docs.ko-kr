---
title: '빠른 시작: Bing Visual Search SDK 사용, C#'
titleSuffix: Azure Cognitive Services
description: Visual search SDK C# 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: 938768ad366611b8651adc3d554c44c16a3830ef
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224531"
---
# <a name="quickstart-bing-visual-search-sdk-c"></a>빠른 시작: Bing Visual Search SDK C#

Bing Visual Search SDK는 웹 요청 및 구문 분석 결과에 대한 REST API의 기능을 사용합니다.
[C# Bing Visual Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch)은 Git Hub에서 얻을 수 있습니다.

코드 시나리오에 대해서는 다음 머리글 아래에서 설명합니다.
* [Visual Search 클라이언트](#client)
* [전체 콘솔 응용 프로그램](#complete)
* [cropArea를 사용하여 이진 이미지 게시](#binary-crop)
* [KnowledgeRequest 매개 변수](#knowledge-req)
* [태그, 동작 및 actionType](#tags-actions)
* [태그 수, 작업 수 및 첫 번째 actionType](#num-tags-actions)

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2017. 필요하면 여기(https://www.visualstudio.com/vs/community/)에서 무료 커뮤니티 버전을 다운로드할 수 있습니다.
* Cognitive Services API 키는 SDK 호출을 인증하는 데 필요합니다. [평가판 키](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7)에 등록합니다. 평가판 키는 초당 한 번의 호출로 7일 동안 유효합니다. 프로덕션 시나리오의 경우 [액세스 키를 구입하세요](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). [가격 책정 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/)도 참조하세요.
* .NET core SDK 및 .net core 1.1 앱을 실행하는 기능입니다. 여기(https://www.microsoft.com/net/download/)에서 코어, 프레임워크 및 런타임을 가져올 수 있습니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성

Bing Web Search SDK를 사용하여 콘솔 응용 프로그램을 설정하려면 Visual Studio의 솔루션 탐색기에서 `Manage NuGet Packages` 옵션을 찾습니다.  `Microsoft.Azure.CognitiveServices.Search.VisualSearch` 패키지를 추가합니다.

[NuGet Web Search SDK 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/1.0) 설치는 다음을 포함한 종속성도 설치합니다.
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

<a name="client"></a>

## <a name="visual-search-client"></a>Visual Search 클라이언트
`VisualSearchAPI` 클라이언트의 인스턴스를 만들려면 Using 지시문을 추가합니다.

```csharp
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
```

그런 다음,클라이언트를 인스턴스화합니다.

```csharp
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```

클라이언트를 사용하여 이미지를 검색합니다.

```csharp
 System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
 // The knowledgeRequest parameter is not required if an image binary is passed in the request body
 var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
```

이전 쿼리의 결과를 구문 분석합니다.

```csharp
// Visual Search results
if (visualSearchResults.Image?.ImageInsightsToken != null)
{
    Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
}
else
{
    Console.WriteLine("Couldn't find image insights token!");
}

// List of tags
if (visualSearchResults.Tags.Count > 0)
{
    var firstTagResult = visualSearchResults.Tags.First();
    Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

    // List of actions in first tag
    if (firstTagResult.Actions.Count > 0)
    {
        var firstActionResult = firstTagResult.Actions.First();
        Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
        Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
    }
    else
    {
        Console.WriteLine("Couldn't find tag actions!");
    }
```

<a name="complete"></a> 

## <a name="complete-console-application"></a>전체 콘솔 응용 프로그램

다음 콘솔 응용 프로그램은 이전에 정의된 쿼리를 실행하고 결과를 구문 분석합니다.

```csharp
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using System;
using System.IO;
using System.Linq;

namespace VisualSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            String subKey = "YOUR-SUBSCRIPTION-KEY";

            VisualSearchImageBinary(subKey);
            //VisualSearchImageBinaryWithCropArea(subKey);
            //VisualSearchUrlWithFilters(subKey);
            //VisualSearchInsightsTokenWithCropArea(subKey);
            //VisualSearchUrlWithJson(subKey);

            Console.WriteLine("\r\nAny key to quit...");
            Console.ReadKey();
        }


        // This will send an image binary in the body of the post request and print out the imageInsightsToken, 
        //  the number of tags, the number of actions, and the first actionType.

        public static void VisualSearchImageBinary(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                using (System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
                {
                    // The knowledgeRequest parameter is not required if an image binary is passed in the request body
                    var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
                    Console.WriteLine("\r\nVisual search request with binary of image");

                    if (visualSearchResults == null)
                    {
                        Console.WriteLine("No visual search result data.");
                    }
                    else
                    {
                        // Visual Search results
                        if (visualSearchResults.Image?.ImageInsightsToken != null)
                        {
                            Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image insights token!");
                        }

                        // List of tags
                        if (visualSearchResults.Tags.Count > 0)
                        {
                            var firstTagResult = visualSearchResults.Tags.First();
                            Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                            // List of actions in first tag
                            if (firstTagResult.Actions.Count > 0)
                            {
                                var firstActionResult = firstTagResult.Actions.First();
                                Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                                Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tag actions!");
                            }

                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image tags!");
                        }
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

Bing Search 샘플에서는 SDK의 다양한 기능을 설명합니다.  다음 함수를 이전에 정의된 `VisualSrchSDK` 클래스에 추가합니다.

<a name="binary-crop"></a>

## <a name="image-binary-post-with-croparea"></a>cropArea를 사용하여 이진 이미지 게시

다음 코드는 cropArea 개체와 함께 게시 요청의 본문에 이진 이미지를 보냅니다.  그런 다음, imageInsightsToken, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```csharp
public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        using (FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
        {
            // The ImageInfo struct contains a crop area specifying a region to crop in the uploaded image
            CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
            ImageInfo ImageInfo = new ImageInfo(cropArea: CropArea);
            VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);

            // The knowledgeRequest here holds additional information about the image, which is passed in in binary form
            var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: VisualSearchRequest).Result;
            Console.WriteLine("\r\nVisual search request with binary of image with knowledgeRequest of CropArea");

            if (visualSearchResults == null)
            {
                Console.WriteLine("No visual search result data.");
            }
            else
            {
                // Visual Search results
                if (visualSearchResults.Image?.ImageInsightsToken != null)
                {
                    Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                }
                else
                {
                    Console.WriteLine("Couldn't find image insights token!");
                }

                // List of tags
                if (visualSearchResults.Tags.Count > 0)
                {
                    var firstTagResult = visualSearchResults.Tags.First();
                    Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                    // List of actions in first tag
                    if (firstTagResult.Actions.Count > 0)
                    {
                        var firstActionResult = firstTagResult.Actions.First();
                        Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                        Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find tag actions!");
                    }

                }
                else
                {
                    Console.WriteLine("Couldn't find image tags!");
                }
            }
        }
    }

    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

<a name="knowledge-req"></a>

## <a name="knowledgerequest-parameter"></a>KnowledgeRequest 매개 변수

다음 코드는 \"site:www.bing.com\" 필터와 함께 `knowledgeRequest` 매개 변수에서 이미지 URL을 보냅니다.  그런 다음, `imageInsightsToken`, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```csharp
public static void VisualSearchUrlWithFilters(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        // The image can be specified via URL, in the ImageInfo object
        var ImageUrl = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80";
        ImageInfo ImageInfo = new ImageInfo(url: ImageUrl);

        // Optional filters inside the knowledgeRequest will restrict similar products and images to certain domains
        Filters Filters = new Filters(site: "www.bing.com");
        KnowledgeRequest KnowledgeRequest = new KnowledgeRequest(filters: Filters);

        // An image binary is not necessary here, as the image is specified via URL
        VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo, knowledgeRequest: KnowledgeRequest);
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
        Console.WriteLine("\r\nVisual search request with url of image and knowledgeRequest based on filters");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

<a name="tags-actions"></a>

## <a name="tags-actions-and-actiontype"></a>태그, 작업 및 actionType

다음 코드는 cropArea 개체와 함께 knowledgeRequest 매개 변수에서 이미지 정보 토큰을 보냅니다.  그런 다음, imageInsightsToken, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```csharp
public static void VisualSearchInsightsTokenWithCropArea(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        // The image can be specified via an insights token, in the ImageInfo object
        var ImageInsightsToken = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145";

        // An optional crop area can be passed in to define a region of interest in the image
        CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
        ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: ImageInsightsToken, cropArea: CropArea);

        // An image binary is not necessary here, as the image is specified via insights token
        VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
        Console.WriteLine("\r\nVisual search request with ImageInsightsToken and knowledgeRequest based on imageInfo of cropArea");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

<a name="num-tags-actions"></a>

## <a name="number-of-tags-number-of-actions-and-first-actiontype"></a>태그 수, 작업 수 및 첫 번째 actionType

다음 코드는 자르기 영역과 함께 knowledgeRequest 매개 변수에서 이미지 url을 보냅니다.  그런 다음, imageInsightsToken, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```csharp
public static void VisualSearchUrlWithJson(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {

        //The visual search request can be passed in as a JSON string
        //The image is specified via URL in the ImageInfo object, along with a crop area as shown below:
        
        //     "imageInfo": {
        //         "url": "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80",
        //     "cropArea": {
        //           "top": 0.1,
        //           "bottom": 0.5,
        //           "left": 0.1,
        //           "right": 0.9
        //         }
        //     },
        //     "knowledgeRequest": {
        //        "filters": {
        //            "site": "www.bing.com"
        //        }              
        //     }

        var VisualSearchRequestJSON = "{\"imageInfo\":{\"url\":\"https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80\",\"cropArea\":{\"top\":0.1,\"bottom\":0.5,\"left\":0.1,\"right\":0.9}},\"knowledgeRequest\":{\"filters\":{\"site\":\"www.bing.com\"}}}";

        // An image binary is not necessary here, as the image is specified by URL in JSON text
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequestJSON).Result;
        Console.WriteLine("\r\nVisual search request with image url specified by JSON text");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

## <a name="next-steps"></a>다음 단계

[Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

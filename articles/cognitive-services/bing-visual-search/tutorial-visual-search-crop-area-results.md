---
title: Bing Visual Search SDK 자르기 영역 결과 자습서 | Microsoft Docs
description: Bing Visual Search SDK를 사용하여 업로드된 이미지의 자르기 영역과 유사한 이미지의 URL을 가져오는 방법입니다.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 9bc3c180f108025f442343d8c5356982a83826a6
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958406"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>자습서: Bing Visual Search SDK 이미지 자르기 영역 및 결과
Visual Search SDK에는 이미지의 영역을 선택하고 더 큰 이미지의 자르기 영역과 유사한 이미지를 온라인에서 찾는 옵션이 포함되어 있습니다.  이 예제에서는 여러 사람이 포함된 이미지에서 한 사람을 표시하는 자르기 영역을 지정합니다.  코드는 더 큰 이미지의 자르기 영역 및 URL을 보내고 Bing Search URL 및 온라인에서 찾은 비슷한 이미지의 URL을 포함하는 결과를 반환합니다.

## <a name="prerequisites"></a>필수 조건

Windows에서 실행되는 이 코드를 가져오려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. (무료 Community Edition이 작동됩니다.)

Bing Search API를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성
Bing Web Search SDK를 사용하여 콘솔 응용 프로그램을 설정하려면 Visual Studio의 솔루션 탐색기에서 NuGet 패키지 관리 옵션을 찾습니다. Microsoft.Azure.CognitiveServices.Search.VisualSearch 패키지를 추가합니다.

NuGet Web Search SDK 패키지 설치는 다음을 포함한 종속성도 설치합니다.

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>이미지 및 자르기 영역
다음 이미지에는 Microsoft 경영진 팀이 나와 있습니다.  Visual Search SDK를 사용하여 이미지의 자르기 영역을 업로드하고 더 큰 이미지에서 선택된 영역의 엔터티를 포함하는 웹 페이지와 다른 이미지를 찾습니다.  이 경우 엔터티는 사람입니다.

![Microsoft 경영진 팀](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>VisualSearchRequest에서 자르기 영역을 ImageInfo로 지정합니다.
이 예제에서는 전체 이미지의 비율로 왼쪽 상단 및 오른쪽 하단 좌표를 지정하는 이전 이미지의 자르기 영역을 사용합니다.  다음 코드는 자르기 이미지에서 `ImageInfo` 개체를 만들고 `ImageInfo` 개체를 `VisualSearchRequest`로 로드합니다.  `ImageInfo` 개체도 온라인에 있는 이미지의 URL을 포함합니다.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>자르기 영역과 유사한 이미지 검색
`VisualSearchRequest`에는 이미지에 대한 자르기 영역 정보와 해당 URL이 포함됩니다.  `VisualSearchMethodAsync` 메서드가 결과를 가져옵니다.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>ImageModuleAction에서 URL 데이터 가져오기
Visual Search 결과는 `ImageTag` 개체입니다.  각 태그에는 `ImageAction` 개체의 목록이 포함됩니다.  각 `ImageAction`에는 작업 유형에 종속되는 값의 목록인 `Data` 필드가 포함됩니다.

다음 코드를 사용하여 다양한 유형을 가져올 수 있습니다.
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
전체 응용 프로그램이 반환됩니다.

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

앞의 목록에 표시된 대로 `Entity` `ActionType`에는 인식할 수 있는 사람, 장소 또는 사물에 대한 정보를 반환하는 Bing Search 쿼리가 포함됩니다.  `TopicResults` 및 `ImageResults` 유형에는 관련 이미지에 대한 쿼리가 포함됩니다. Bing Search 결과에 대한 목록 링크의 URL입니다.


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

## <a name="complete-code"></a>코드 완료

다음 코드는 앞의 예제를 실행합니다. POST 요청의 본문에 cropArea 개체와 함께 이진 이미지를 보내고 각 ActionType에 대한 Bing Search URL을 인쇄합니다. ActionType이 PagesIncluding이면 코드는 ImageObject 데이터의 ImageObject 항목을 가져옵니다.  데이터에는 웹 페이지의 이미지 URL인 값의 목록이 포함됩니다.  결과 Visual Search URL을 복사하여 브라우저에 붙여넣어 결과를 표시합니다. ContentUrl 항목을 복사하여 브라우저에 붙여넣어 이미지를 표시합니다.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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
[Visual Search 응답](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)
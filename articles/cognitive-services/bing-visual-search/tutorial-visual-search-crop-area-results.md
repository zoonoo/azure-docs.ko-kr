---
title: '자습서: Bing Visual Search SDK를 사용하여 이미지 자르기'
description: Bing Visual Search SDK를 사용하여 이미지의 특정 영역에서 인사이트를 얻습니다.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/03/2019
ms.author: rosh
ms.openlocfilehash: a6a98d9ddf76e86feb35c6d3d71f7777b0edd51d
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471769"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>자습서: Bing Visual Search SDK for C#을 사용하여 이미지 자르기

Bing Visual Search SDK를 사용 하면 유사한 온라인 이미지를 찾기 전에 이미지를 자를 수 있습니다. 이 애플리케이션은 여러 사용자를 포함하는 이미지에서 단일 사람을 자른 다음, 온라인에서 찾은 유사한 이미지를 포함하는 검색 결과를 반환합니다.

이 애플리케이션에 대한 전체 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs)에서 제공됩니다.

이 자습서는 다음 방법을 보여줍니다.

> [!div class="checklist"]
> * Bing Visual Search SDK를 사용하여 요청 보내기
> * Bing Visual Search를 사용하여 검색할 이미지의 영역 자르기
> * 응답 수신 및 처리
> * 응답에서 작업 항목의 Url 찾기

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.
* [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) 패키지 설치.
    - Visual Studio에서 솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 하면 프로젝트를 마우스 **NuGet 패키지 관리** 합니다. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 패키지를 설치합니다. NuGet Custom Search 패키지를 설치하면 다음 어셈블리도 함께 설치됩니다.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>이미지 자르기 영역 지정

이 애플리케이션은 Microsoft 선임 리더십 팀의 이 이미지의 영역을 잘라냅니다. 이 자르기 영역 전체 이미지의 백분율로 나타내는 왼쪽 및 오른쪽 좌표를 사용 하 여 정의 됩니다.  

![Microsoft 경영진 팀](./media/MS_SrLeaders.jpg)

자르기 영역에서 `ImageInfo` 개체를 만들고, `ImageInfo` 개체를 `VisualSearchRequest`로 로드하여 이 이미지를 자릅니다. `ImageInfo` 개체는 이미지의 URL에도 포함 되어 있습니다.

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>자르기 영역과 유사한 이미지 검색

변수의 `VisualSearchRequest` 이미지의 자르기 영역 및 해당 URL에 대 한 정보를 포함 합니다. `VisualSearchMethodAsync()` 메서드 결과 가져옵니다.

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>URL 데이터를 가져올 `ImageModuleAction`

Bing Visual Search 결과는 `ImageTag` 개체입니다. 각 태그에는 `ImageAction` 개체의 목록이 포함됩니다. 각 `ImageAction` 포함을 `Data` 필드는 목록 작업의 형식에 종속 되는 값입니다.

다음 코드를 사용하여 다양한 유형을 출력할 수 있습니다.

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

전체 애플리케이션이 반환됩니다.

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|엔터티 -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

위에 표시된 대로 `Entity` ActionType에는 인식할 수 있는 사람, 장소 또는 사물에 대한 정보를 반환하는 Bing Search 쿼리가 포함됩니다. `TopicResults` 및 `ImageResults` 유형에는 관련 이미지에 대한 쿼리가 포함됩니다. Bing Search 결과에 대한 목록 링크의 URL입니다.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>에 대 한 Url을 가져오는 `PagesIncluding` `ActionType` 이미지

실제 이미지 URL 가져오기는 `ActionType`을 `ImageModuleAction`으로 읽으며 값의 목록이 있는 `Data` 요소를 포함하는 캐스트가 필요합니다. 각 값은 이미지의 URL입니다. 다음과 같은 캐스트를 `PagesIncluding` 동작 유형을 `ImageModuleAction` 값을 읽는:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Visual Search 단일 페이지 웹앱 만들기](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>참고 항목
> [Bing Visual Search API란?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)

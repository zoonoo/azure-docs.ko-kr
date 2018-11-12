---
title: Bing Video Search란?
titlesuffix: Azure Cognitive Services
description: Bing Video Search API를 사용하여 웹에서 비디오를 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: e48a0a056628e0c863330de792f8edfaa48aae34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261862"
---
# <a name="what-is-bing-video-search"></a>Bing Video Search란?

Bing Video Search API는 유사한(단, 정확하지는 않음) 경험을 [Bing 비디오](https://www.bing.com/videos)에 제공합니다. Bing Video Search API를 사용하면 검색 쿼리를 Bing에 보내고 관련 비디오 목록을 다시 가져올 수 있습니다.

사용자의 검색 쿼리와 관련이 있는 비디오를 찾기 위해 비디오 전용 검색 결과 페이지를 빌드하는 경우 더 일반적인 [Bing Web Search API](../bing-web-search/search-the-web.md)를 호출하는 대신 이 API를 호출합니다. 비디오와 함께 웹 페이지, 뉴스 및 이미지와 같은 다른 유형의 콘텐츠를 원하는 경우 Bing Web Search API를 호출합니다.

## <a name="suggesting--using-search-terms"></a>검색어 제안 및 사용

사용자가 자신의 검색 용어를 입력할 수 있는 검색 상자를 제공하는 경우 [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 사용하여 환경을 개선합니다. API는 부분 검색 용어 기반의 제안된 쿼리 문자열을 사용자 형식으로 반환합니다.

사용자가 검색어를 입력하면 URL에서 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) 쿼리 매개 변수를 설정하기 전에 해당 용어를 인코딩합니다. 예를 들어 사용자가 입력 *소형 범선*을 입력한 경우 `q`를 `sailing+dinghies` 또는 `sailing%20dinghies`로 설정합니다.

## <a name="getting-videos"></a>비디오 가져오기

웹에서 사용자의 검색어와 관련된 비디오를 가져오려면 다음의 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

모든 요청은 서버에서 이뤄져야 합니다.

Bing API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 전에 Bing API를 호출하고 Bing이 사용자 및 장치 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다.

특정 도메인에서 비디오를 가져오려면 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

응답은 Bing이 인식하기에 쿼리와 관련이 있는 비디오 목록을 포함하는 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 답변을 포함합니다. 목록에서 각 [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) 개체는 비디오의 URL, 해당 기간, 해당 차원 및 다른 특성 간 해당 인코딩 형식을 포함합니다. 또한 비디오 개체는 비디오 썸네일의 URL 및 썸네일의 차원도 포함합니다.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

모든 비디오 썸네일의 콜라주를 표시하거나 썸네일의 하위 집합을 표시할 수 있습니다. 하위 집합을 표시하는 경우 사용자에게 나머지 비디오를 볼 수 있는 옵션도 제공합니다. 응답에 제공된 순서대로 비디오를 표시해야 합니다. 썸네일 크기 조정에 대한 내용은 [썸네일 크기 조정 및 자르기](./resize-and-crop-thumbnails.md)를 참조합니다. 

사용자가 썸네일을 마우스로 가리키면 비디오의 썸네일 버전을 재생하도록 [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl)을 사용할 수 있습니다. 표시하는 경우 동작 썸네일의 특성을 확인합니다.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

사용자가 썸네일을 클릭하는 경우 비디오 보기 옵션은 다음과 같습니다.

- 호스트 웹 사이트(예: YouTube)에서 비디오를 보려면 [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl)을 사용합니다.
- Bing 비디오 브라우저에서 비디오를 보려면 [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl)을 사용합니다.
- 사용자의 고유한 환경에 비디오를 포함시키려면 [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml)을 사용합니다. 

게시자 및 작성자가 비디오를 재생할 때 비디오의 특성을 확인해야 합니다.

비디오에 대한 인사이트를 얻기 위해 [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid)를 사용하는 데 관한 자세한 내용은 [동영상 인사이트](./video-insights.md)를 참조하세요.

## <a name="filtering-videos"></a>비디오 필터링

기본적으로 Video Search API는 쿼리와 관련된 모든 비디오를 반환합니다. 무료 비디오 또는 길이가 5분 이내 비디오만 원하는 경우 다음 필터 쿼리 매개 변수를 사용하면 됩니다.

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;가격별 비디오 필터링(예: 무료인 비디오 또는 가격을 지불해야 하는 비디오)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;해상도별 비디오 필터링(예: 720p 또는 더 높은 해상도의 비디오)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;비디오 길이별 비디오 필터링(예: 길이가 5분 이내인 비디오)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;기간별 비디오 필터링(예: 지난 주에 Bing에서 검색된 비디오)

특정 도메인에서 비디오를 가져오려면 쿼리 문자열에 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 포함합니다.

> [!NOTE]
> `site:` 연산자를 사용하는 경우 쿼리에 따라 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch) 설정에 관계 없이 응답에 성인 콘텐츠가 포함될 가능성이 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 콘텐츠를 지원하는 경우에만 `site:`를 사용해야 합니다.

다음 예제에서는 해상도가 720p 이상이며 지난 달에 Bing에서 검색된 무료 비디오를 ContosoSailing.com에서 가져오는 방법을 보여줍니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>쿼리 확장

Bing이 원래 검색을 좁히기 위해 쿼리를 확장할 수 있는 경우 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 개체에는 `queryExpansions` 필드가 포함됩니다. 예를 들어 쿼리가 *배수로 청소*인 경우 확장된 쿼리는 배수로 청소 **도구**, 배수로 청소의 **기초**, 배수로 청소 **기계** 및 **손쉬운** 배수로 청소일 수 있습니다.

다음 예제에서는 *배수로 청소*에 대한 확장된 쿼리를 보여줍니다.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` 필드에는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 확장된 쿼리가 포함되며 `displayText` 필드에는 확장 용어가 포함됩니다. 확장된 쿼리 문자열이 실제로 원하는 것인 경우 확장된 쿼리 문자열을 사용자에게 표시하려면 텍스트 및 썸네일 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

## <a name="pivoting-the-query"></a>쿼리 피벗

Bing이 원래 검색 쿼리를 분할할 수 있는 경우 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 개체에는 `pivotSuggestions` 필드가 포함됩니다. 예를 들어 원래 쿼리가 *배수로 청소*인 경우 Bing은 쿼리를 *청소*와 *배수로*로 분할할 수 있습니다.

다음 예제에서는 *배수로 청소*에 대한 피벗 제안 사항을 보여줍니다.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

각 피벗에 대한 응답에는 제안된 쿼리를 포함하는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 제안된 쿼리가 포함되며 `displayText` 필드에는 원래 쿼리의 피벗을 대체하는 용어가 포함됩니다. 예를 들어 창문 청소입니다.

확장된 쿼리 문자열이 실제로 원하는 것인 경우 확장된 쿼리 문자열을 사용자에게 표시하려면 `text` 및 `thumbnail` 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 빠르게 시작하려면 [첫 번째 요청 수행](./quick-start.md)을 참조하세요.

구독 키를 가져오려면 [구독 키](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api)를 참조하세요.

[Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 참조를 숙지하세요. 참조에는 검색 결과를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수 목록이 포함되어 있습니다. 응답 개체의 정의도 포함됩니다. 

검색 상자 사용자 경험을 개선하려면 [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 참조합니다. 사용자가 쿼리 용어를 입력할 때 다른 사용자가 사용한 관련 쿼리 용어를 가져오려면 이 API를 호출할 수 있습니다.

검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)을 반드시 읽도록 합니다.

Video Search API를 호출하면 Bing은 결과 목록을 반환합니다. 목록은 쿼리와 관련된 결과의 총 수의 하위 집합입니다. 응답의 `totalEstimatedMatches` 필드에는 볼 수 있는 비디오의 예상 수가 포함됩니다. 나머지 비디오를 통해 페이징하는 방법에 대한 자세한 내용은 [비디오 페이징](./paging-videos.md)을 참조하세요.

비디오에 대한 인사이트 가져오기에 대한 자세한 내용은 [비디오 인사이트](./video-insights.md)를 참조하세요.

인기 비디오 가져오기에 관한 자세한 내용은 [인기 비디오](./trending-videos.md)를 참조하세요.
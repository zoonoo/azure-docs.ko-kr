---
title: Bing Video Search API에 검색 요청 전송
titlesuffix: Azure Cognitive Services
description: Bing Video Search API에 검색 쿼리를 전송하는 데 관해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 2eaefcf9af6188867bfd692fad891a70fcadb076
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386515"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Bing Video Search API를 사용한 비디오 검색

Bing Video Search API를 통해 Bing의 인지적 뉴스 검색 기능을 애플리케이션으로 쉽게 통합할 수 있습니다. API에서는 주로 관련 비디오를 웹에서 찾고 반환하는 반면, 웹에서 지능적이고 포커스가 있는 비디오 검색을 위한 몇 가지 기능을 제공합니다.

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

Bing API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 전에 Bing API를 호출하고 Bing이 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다.

특정 도메인에서 비디오를 가져오려면 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

응답은 Bing이 인식하기에 쿼리와 관련이 있는 비디오 목록을 포함하는 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 답변을 포함합니다. 목록에서 각 [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) 개체는 비디오의 URL, 해당 기간, 해당 차원 및 다른 특성 간 해당 인코딩 형식을 포함합니다. 또한 비디오 개체는 비디오 썸네일의 URL 및 썸네일의 차원도 포함합니다.

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

## <a name="video-thumbnails"></a>비디오 썸네일

Bing Video Search API에서 반환되는 비디오 썸네일의 전체 또는 하위 집합을 표시할 수 있습니다. 하위 집합을 표시하는 경우 사용자에게 나머지 비디오를 볼 수 있는 옵션도 제공합니다. Bing API에 대한 [요구 사항 사용 및 표시](../UseAndDisplayRequirements.md)의 일부로 응답에 제공된 순서대로 비디오를 표시해야 합니다. 썸네일 크기 조정에 대한 내용은 [썸네일 크기 조정 및 자르기](../resize-and-crop-thumbnails.md)를 참조합니다. 

사용자가 썸네일을 마우스로 가리키면 비디오의 썸네일 버전을 재생하도록 [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl)을 사용할 수 있습니다. 표시하는 경우 동작 썸네일의 특성을 확인합니다.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

썸네일을 클릭하면 비디오를 보기 위한 세 가지 옵션이 있습니다.

- 호스트 웹 사이트(예: YouTube)에서 비디오를 보려면 [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl)을 사용합니다.
- Bing 비디오 브라우저에서 비디오를 보려면 [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl)을 사용합니다.
- 사용자의 고유한 환경에 비디오를 포함시키려면 [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml)을 사용합니다. 

게시자 및 작성자가 비디오를 재생할 때 비디오의 특성을 확인해야 합니다.

비디오에 대한 인사이트를 얻기 위해 [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid)를 사용하는 데 관한 자세한 내용은 [동영상 인사이트](../video-insights.md)를 참조하세요.

## <a name="filtering-videos"></a>비디오 필터링

기본적으로 Video Search API는 쿼리와 관련된 모든 비디오를 반환합니다. 무료 비디오 또는 길이가 5분 이내 비디오만 원하는 경우 다음 필터 쿼리 매개 변수를 사용하면 됩니다.

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash;가격별 비디오 필터링(예: 무료인 비디오 또는 가격을 지불해야 하는 비디오)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash;해상도별 비디오 필터링(예: 720p 또는 더 높은 해상도의 비디오)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash;비디오 길이별 비디오 필터링(예: 길이가 5분 이내인 비디오)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash;기간별 비디오 필터링(예: 지난 주에 Bing에서 검색된 비디오)

특정 도메인에서 비디오를 가져오려면 쿼리 문자열에 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 포함합니다.

> [!NOTE]
> `site:` 연산자를 사용하는 경우 쿼리에 따라 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) 설정에 관계 없이 응답에 성인 콘텐츠가 포함될 가능성이 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 콘텐츠를 지원하는 경우에만 `site:`를 사용해야 합니다.

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

Bing이 원래 검색을 좁히기 위해 쿼리를 확장할 수 있는 경우 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 개체에는 `queryExpansions` 필드가 포함됩니다. 예를 들어 쿼리가 *Cleaning Gutters*인 경우 Gutter Cleaning **Tools**, Cleaning Gutters **From the Ground**, Gutter Cleaning **Machine** 및 **Easy** Gutter Cleaning 같은 확장 쿼리가 가능합니다.

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

`queryExpansions` 필드에는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 확장된 쿼리가 포함되며 `displayText` 필드에는 확장 용어가 포함됩니다. 확장된 쿼리 문자열이 실제로 원하는 것인 경우 확장된 쿼리 문자열을 사용자에게 표시하려면 텍스트 및 썸네일 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

## <a name="pivoting-the-query"></a>쿼리 피벗

Bing이 원래 검색 쿼리를 분할할 수 있는 경우 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 개체에는 `pivotSuggestions` 필드가 포함됩니다. 예를 들어 원래 쿼리가 *배수로 청소*인 경우 Bing은 쿼리를 *청소*와 *배수로*로 분할할 수 있습니다.

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

각 피벗에 대한 응답에는 제안된 쿼리를 포함하는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 제안된 쿼리가 포함되며 `displayText` 필드에는 원래 쿼리의 피벗을 대체하는 용어가 포함됩니다. 예를 들어 창문 청소입니다.

확장된 쿼리 문자열이 실제로 원하는 것인 경우 확장된 쿼리 문자열을 사용자에게 표시하려면 `text` 및 `thumbnail` 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

---
title: Bing Image Search란? | Microsoft Docs
description: Bing Images Search API를 사용하여 웹에서 이미지를 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336522"
---
# <a name="what-is-bing-image-search"></a>Bing Image Search란?

사용자 검색 쿼리를 Bing에 전송하고 관련 이미지 목록을 돌려 받게 함으로써 Bing Images Search API는 [Bing Image](https://www.bing.com/images)와 비슷한 환경을 제공합니다.

사용자의 검색 쿼리와 관련이 있는 이미지를 찾기 위해 이미지 전용 검색 결과 페이지를 빌드하는 경우 보다 일반적인 [Web Search API](../bing-web-search/search-the-web.md)을 호출하는 대신 이 API를 호출합니다. 이미지 그리고 웹 페이지, 뉴스 및 비디오와 같은 다른 유형의 콘텐츠를 원하는 경우 Web Search API를 호출합니다.

## <a name="suggesting--using-search-terms"></a>검색 용어 제안 및 사용

사용자가 자신의 검색 용어를 입력할 수 있는 검색 상자를 제공하는 경우 [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 사용하여 환경을 개선합니다. API는 부분 검색 용어 기반의 제안된 쿼리 문자열을 사용자 형식으로 반환합니다.

사용자가 자신의 검색 용어를 입력한 후 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) 쿼리 매개 변수를 설정하기 전에 URL은 해당 용어를 인코딩합니다. 예를 들어 사용자가 입력 *소형 범선*을 입력한 경우 `q`를 `sailing+dinghies` 또는 `sailing%20dinghies`로 설정합니다.

## <a name="getting-images"></a>이미지 가져오기

웹에서 사용자의 검색 용어와 관련된 이미지를 가져오려면 다음의 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

모든 요청은 서버에서 이뤄져야 합니다. 클라이언트에서는 호출할 수 없습니다.

Bing API 중 하나를 처음으로 호출한 경우 클라이언트 ID 헤더를 포함하지 마십시오. 전에 Bing API를 호출하고 Bing이 사용자 및 장치 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다.

특정 도메인에서 이미지를 가져오려면 [사이트:](http://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

응답은 Bing이 인식하기에 쿼리와 관련이 있는 이미지 목록을 포함하는 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 답변을 포함합니다. 목록에서 각 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 개체는 이미지, 해당 크기, 차원 및 인코딩 형식의 URL을 포함합니다. 또한 이미지 개체는 이미지 썸네일의 URL 및 썸네일의 차원도 포함합니다.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

모든 이미지 썸네일의 콜라주를 표시하거나 썸네일의 하위 집합을 표시할 수 있습니다. 하위 집합을 표시하는 경우 사용자에게 나머지 이미지를 볼 수 있는 옵션도 제공합니다. 응답에 제공된 순서대로 이미지를 표시해야 합니다.

또한 사용자가 썸네일 위로 커서를 움직이면 썸네일을 확대할 수도 있습니다. 확대하는 경우 이미지의 특성을 확인합니다. 예를 들어 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl)에서 호스트를 추출하여 이미지 아래에 표시하여 확인합니다. 썸네일 크기 조정에 대한 내용은 [썸네일 크기 조정 및 자르기](./resize-and-crop-thumbnails.md)를 참조합니다.

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

사용자가 썸네일을 클릭하는 경우 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl)을 사용하여 사용자에게 전체 크기 이미지를 표시할 수 있습니다. 이미지 특성을 확인합니다.

`shoppingSourcesCount` 또는 `recipeSourcesCount`가 0보다 큰 경우 이미지의 항목에 대해 쇼핑이나 레시피가 존재하는 것을 나타내려면 썸네일에 쇼핑 카트 같은 배지를 추가합니다.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

이미지에서 인식된 사람 또는 이미지를 포함하는 웹 페이지 같이 이미지에 대한 인사이트를 얻으려면 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken)을 사용합니다. 자세한 내용은 [이미지 인사이트](./image-insights.md)를 참조합니다.

## <a name="filtering-images"></a>이미지 필터링

 기본적으로 Images Search API는 쿼리와 관련된 모든 이미지를 반환합니다. 하지만 투명한 배경의 이미지 또는 특정 크기의 이미지를 원하는 경우 Bing이 반환하는 이미지를 필터링하려면 다음 쿼리 매개 변수를 사용합니다.  

- [가로 세로](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)—가로 세로 비율로 이미지 필터링(예를 들어 표준 또는 넓은 화면 이미지)
- [색상](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)—흑백 같은 주요 색상으로 이미지 필터링
- [새로 고침](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)—시기로 이미지 필터링(예를 들어 지난 주에 Bing에서 검색된 이미지)
- [높이](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [너비](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)—너비와 높이로 이미지 필터링
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)—콘텐츠로 이미지 필터링(예를 들어 사람의 얼굴만 보여주는 이미지)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)—형식으로 이미지 필터링(예를 들어 클립 아트, 애니메이션 GIF 또는 투명한 배경)
- [라이선스](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)—사이트와 연결된 라이선스의 유형으로 이미지 필터링
- [크기](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)—최대 200x200 픽셀의 작은 이미지 같이 크기로 이미지 필터링

특정 도메인에서 이미지를 가져오려면 [사이트:](http://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

> [!NOTE]
> `site:` 연산자를 사용하는 경우 쿼리에 따라 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 설정에 관계 없이 응답에 성인 콘텐츠가 포함될 가능성이 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 컨텐츠를 지원하는 경우에만 `site:`를 사용해야 합니다.

다음 예제에서는 Bing이 지난 주에 발견한 ContosoSailing.com에서 작은 이미지를 가져오는 방법을 보여줍니다.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>쿼리 피벗

Bing이 원래 검색 쿼리를 분할할 수 있는 경우 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 개체에는 `pivotSuggestions` 필드가 포함됩니다. 예를 들어 원래 쿼리가 *Microsoft Surface*인 경우 Bing은 쿼리를 *Microsoft* 및 *Surface*로 분할할 수 있습니다.  

다음 예제에서는 *Microsoft Surface*에 대한 피벗 제안 사항을 보여줍니다.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

사용자가 관심이 있는 경우 선택적 쿼리 용어를 사용자에게 표시할 수 있습니다.

`pivotSuggestions` 필드에는 원래 쿼리가 분할된 세그먼트(피벗) 목록이 포함됩니다. 각 피벗에 대한 응답에는 제안된 쿼리를 포함하는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 제안된 쿼리가 포함되며 `displayText` 필드에는 원래 쿼리의 피벗을 대체하는 용어가 포함됩니다. 예를 들어 Surface의 릴리스 날짜입니다.

피벗 쿼리 문자열이 실제로 원하는 것인 경우 피벗 쿼리 문자열을 사용자에게 표시하려면 `text` 및 `thumbnail` 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>쿼리 확장

Bing이 원래 검색을 좁히기 위해 쿼리를 확장하는 경우 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 개체에는 `queryExpansions` 필드가 포함됩니다. 예를 들어 쿼리가 *Microsoft Surface*인 경우 확장된 쿼리는 Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** 및 Microsoft Surface **Hub**가 될 수 있습니다.

다음 예제에서는 *Microsoft Surface*에 대한 확장된 쿼리를 보여줍니다.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` 필드에는 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) 개체 목록이 포함됩니다. `text` 필드에는 확장된 쿼리가 포함되며 `displayText` 필드에는 확장 용어가 포함됩니다. 확장된 쿼리 문자열이 실제로 원하는 것인 경우 확장된 쿼리 문자열을 사용자에게 표시하려면 `text` 및 `thumbnail` 필드를 사용할 수 있습니다. `webSearchUrl` URL 또는 `searchLink` URL을 사용하여 썸네일 및 텍스트를 클릭할 수 있게 만듭니다. Bing 검색 결과를 사용자에게 보내려면 `webSearchUrl`을, 직접 결과 페이지를 제공하는 경우 `searchLink`를 사용합니다.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

첫 번째 요청으로 신속하게 시작하려면 [C# 빠른 시작](quickstarts/csharp.md)을 참조합니다.

[Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 참조에 익숙해지십시오. 참조에는 검색 결과를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수 목록이 포함됩니다. 응답 개체의 정의도 포함됩니다.

검색 상자 사용자 경험을 개선하려면 [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 참조합니다. 사용자가 쿼리 용어를 입력할 때 다른 사용자가 사용한 관련 쿼리 용어를 가져오려면 이 API를 호출할 수 있습니다.

검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)을 반드시 읽도록 합니다.

Bing Image Search API를 호출하는 경우 Bing은 결과 목록을 반환합니다. 목록은 쿼리와 관련된 결과의 총 수의 하위 집합입니다. 응답의 `totalEstimatedMatches` 필드에는 볼 수 있는 이미지의 예상 수가 포함됩니다. 나머지 이미지를 통해 페이징하는 방법에 대한 자세한 내용은 [이미지 페이징](./paging-images.md)을 참조합니다.
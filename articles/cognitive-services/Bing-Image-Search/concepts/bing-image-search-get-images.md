---
title: Bing Image Search API를 사용하여 웹에서 이미지 가져오기 | Microsoft Docs
description: Bing Image Search API를 사용하여 웹에서 관련 이미지를 검색하고 가져옵니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082523"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Bing Image Search API를 사용하여 웹에서 이미지 가져오기

Bing Image Search REST API를 사용하면 다음 GET 요청을 보내서 사용자의 검색 용어와 관련된 웹에서 이미지를 가져올 수 있습니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * 모든 요청은 클라이언트가 아닌 서버에서 이뤄져야 합니다.
> * Bing Search API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 이전에 사용자 및 장치 조합에 대한 클라이언트 ID를 반환한 Bing API를 호출한 경우에만 클라이언트 ID를 포함합니다.
> * 응답에 제공된 순서대로 이미지를 표시해야 합니다.

## <a name="getting-images-from-a-specific-web-domain"></a>특정 웹 도메인에서 이미지 가져오기

특정 도메인에서 이미지를 가져오려면 [사이트:](http://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> `site:` 연산자를 사용하는 쿼리에 대한 응답은 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 설정과 관계없이 성인 콘텐츠를 포함할 수 있습니다. 도메인의 콘텐츠를 인식하는 경우에만 `site:`를 사용합니다.

다음 예제에서는 Bing이 지난 주에 발견한 ContosoSailing.com에서 작은 이미지를 가져오는 방법을 보여줍니다.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>이미지 필터링

 기본적으로 Images Search API는 쿼리와 관련된 모든 이미지를 반환합니다. Bing에서 반환하는 이미지를 필터링하는 경우(예: 투명한 배경의 이미지 또는 특정 크기의 이미지만을 반환하기 위해) 다음 쿼리 매개 변수를 사용하면 됩니다.

* [가로 세로](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)—가로 세로 비율로 이미지 필터링(예를 들어 표준 또는 넓은 화면 이미지)
* [색상](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)—흑백 같은 주요 색상으로 이미지 필터링
* [새로 고침](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)—시기로 이미지 필터링(예를 들어 지난 주에 Bing에서 검색된 이미지)
* [높이](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [너비](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)—너비와 높이로 이미지 필터링
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)—콘텐츠로 이미지 필터링(예를 들어 사람의 얼굴만 보여주는 이미지)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)—형식으로 이미지 필터링(예를 들어 클립 아트, 애니메이션 GIF 또는 투명한 배경)
* [라이선스](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)—사이트와 연결된 라이선스의 유형으로 이미지 필터링
* [크기](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)—최대 200x200 픽셀의 작은 이미지 같이 크기로 이미지 필터링

특정 도메인에서 이미지를 가져오려면 [사이트:](http://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

다음 예제에서는 Bing이 지난 주에 발견한 ContosoSailing.com에서 작은 이미지를 가져오는 방법을 보여줍니다.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing Image Search 응답 형식

Bing의 응답 메시지에는 Azure Cognitive Services에서 쿼리와 관련되도록 결정한 이미지 목록을 포함하는 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 대답이 포함됩니다. 목록에서 각 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 개체에는 이미지에 대한 URL, 크기, 차원, 인코딩 형식, 이미지 썸네일에 대한 URL 및 썸네일의 차원과 같은 정보가 포함됩니다.

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

Bing Image Search API를 호출하는 경우 Bing은 결과 목록을 반환합니다. 목록은 쿼리와 관련된 결과의 총 수의 하위 집합입니다. 응답의 `totalEstimatedMatches` 필드에는 볼 수 있는 이미지의 예상 수가 포함됩니다. 나머지 이미지를 통해 페이징하는 방법에 대한 자세한 내용은 [이미지 페이징](../paging-images.md)을 참조합니다.

## <a name="next-steps"></a>다음 단계

Bing Image Search API를 사용해본 적이 없다면 [빠른 시작](../quickstarts/csharp.md)을 사용하세요. 더 복잡한 항목을 찾는 경우 [단일 페이지 웹앱](../tutorial-bing-image-search-single-page-app.md)을 만들기 위한 자습서를 사용하세요.

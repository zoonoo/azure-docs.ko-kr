---
title: Bing Video Search API를 사용하여 웹에서 인기 비디오 검색
titlesuffix: Azure Cognitive Services
description: Bing Video Search API를 사용하여 웹에서 인기 비디오를 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2aa3c0cd9112f2de9ccda818e74b0b2c377133bf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869367"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Bing Video Search API를 사용하여 인기 비디오 가져오기 

Bing Video Search API를 사용하면 웹에서 다양한 범주의 최신 인기 비디오를 찾을 수 있습니다. 

## <a name="get-request"></a>GET 요청

Bing Video Search API에서 최신 인기 비디오를 가져오려면 다음 GET 요청을 보냅니다.  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>시장 지원

다음 지역/국가에서 인기 비디오를 지원합니다.  
 
-   en-AU(영어, 오스트레일리아)  
-   en-CA(영어, 캐나다)  
-   en-GB(영어, 영국)  
-   en-ID(영어, 인도네시아)  
-   en-IE(영어, 아일랜드)  
-   en-IN(영어, 인도)  
-   en-NZ(영어, 뉴질랜드)  
-   en-PH(영어, 필리핀)  
-   en-SG(영어, 싱가포르)  
-   en-US(영어, 미국)  
-   en-WW(영어, 전 세계 집계 코드)  
-   en-ZA(영어, 남아프리카 공화국)  
-   zh-CN(중국어, 중국)

## <a name="example-json-response"></a>예제 JSON 응답  

다음 예제에서는 범주 및 하위 범주별로 나열되는 인기 비디오가 포함된 API 응답을 보여 줍니다. 또한 응답에는 가장 인기 있는 비디오인 배너 비디오가 포함되어 있으며 하나 이상의 범주에서 가져올 수 있습니다.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
[비디오 인사이트 가져오기](video-insights.md)
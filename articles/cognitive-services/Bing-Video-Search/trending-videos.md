---
title: 인기 비디오에 대한 웹 검색 - Bing Video Search
titlesuffix: Azure Cognitive Services
description: Bing Video Search API를 사용하여 웹에서 인기 비디오를 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203558"
---
# <a name="get-trending-videos"></a>인기 비디오 가져오기  

현재 유행하는 인기 비디오를 가져오려면 다음 GET 요청을 보냅니다.  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

  
다음 예제에서는 인기 비디오를 포함하는 응답을 보여 줍니다.  

```  
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
응답에는 범주 및 하위 범주별 비디오 목록이 들어 있습니다. 예를 들어 범주 목록에 뮤직 비디오 범주가 포함되었고 해당 하위 범주 중 하나가 상위인 경우 사용자 환경에서 상위 뮤직 비디오 범주를 만들 수 있습니다. 그런 다음, `thumbnailUrl`, `displayText` 및 `webSearchUrl` 필드를 사용하여 각 범주 아래 클릭할 수 있는 타일을 만들 수 있습니다(예를 들어 상위 뮤직 비디오). 사용자가 타일을 클릭하면 비디오가 재생되는 Bing의 비디오 브라우저가 표시됩니다.

응답에는 가장 인기 있는 비디오인 배너 비디오도 포함됩니다. 배너 비디오는 하나 이상의 범주에서 가져온 것일 수 있습니다.  
  

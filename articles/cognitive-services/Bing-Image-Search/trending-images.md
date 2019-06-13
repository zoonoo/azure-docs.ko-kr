---
title: Bing Image Search API를 사용하여 유행하는 이미지 가져오기
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하여 웹에서 유행하는 최신 이미지를 검색합니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383425"
---
# <a name="get-trending-images-from-the-web"></a>웹에서 유행하는 이미지 가져오기

현재 유행하는 최신 이미지를 가져오려면 다음 GET 요청을 보냅니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

현재 Trending Images API는 다음 시장만 지원합니다.  

- en-US(영어, 미국)  
- en-CA(영어, 캐나다)  
- en-AU(영어, 오스트레일리아)  
- zh-CN(중국어, 중국)

응답에는 이미지를 범주별로 나열하는 [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) 개체가 포함됩니다. 범주의 `title`을 사용하여 사용자 경험의 이미지를 그룹화합니다. 범주는 매일 변경될 수 있습니다.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

각 타일에는 이미지 및 관련 이미지를 가져오기 위한 옵션이 포함되어 있습니다. 관련 이미지를 가져오기 위해 `text` 쿼리를 사용하여 [Image Search API](./search-the-web.md)를 호출하고 관련 이미지를 직접 표시할 수 있습니다. 또는 `webSearchUrl`의 URL을 사용하여 사용자를 관련 이미지가 포함된 Bing의 이미지 검색 결과 페이지로 이동할 수 있습니다.

Image Search API를 호출하여 관련 이미지를 가져오는 경우 [id](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) 쿼리 매개 변수를 `id` 필드의 ID로 설정하세요. ID를 지정하면 응답에 이미지(응답의 첫 번째 이미지) 및 관련 이미지가 포함됩니다. 또한 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 쿼리 매개 변수를 `text` 필드의 `query` 개체에 있는 텍스트로 설정하세요.

다음 예제는 이미지 ID를 사용하여 이전 Trending Images API 응답의 Mr. Smith 관련 이미지를 가져오는 방법을 보여줍니다.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

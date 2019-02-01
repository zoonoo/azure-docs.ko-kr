---
title: Bing Local Business Search API 쿼리 및 응답 보내기 및 사용 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 Bing Local Business Search API를 통해 검색 쿼리를 보내고 사용하는 방법을 알아봅니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: 3e5fcf45ac1d8649203bdbb5b4aa3899332b3a1c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153337"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Bing Local Business Search API 쿼리 및 응답 보내기 및 사용

검색 쿼리를 해당 엔드포인트에 보내고 필수인 `Ocp-Apim-Subscription-Key` 헤더를 포함하면 Bing Local Business Search API에서 로컬 결과를 가져올 수 있습니다. 사용 가능한 [헤더](local-search-reference.md#headers) 및 [매개 변수](local-search-reference.md#query-parameters)와 함께 검색할 영역에 대한 [지리적 경계](specify-geographic-search.md)와 반환되는 장소의 [범주](local-search-query-response.md)를 지정하여 검색을 사용자 지정할 수 있습니다.

## <a name="creating-a-request"></a>요청 만들기

Bing Local Business Search API에 요청을 보내려면 검색어를 API 엔드포인트에 추가하고 `Ocp-Apim-Subscription-Key` 헤더를 포함하기 전에 `q=` 매개 변수에 추가합니다. 예: 

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

전체 요청 URL 구문은 다음과 같습니다. 요청 보내기에 대한 자세한 내용은 Bing Local Business Search API [빠른 시작](quickstarts/local-quickstart.md), [헤더](local-search-reference.md#headers) 및 [매개 변수](local-search-reference.md#query-parameters)에 대한 참조 콘텐츠를 참조하세요. 

로컬 검색 범주에 대한 내용은 [Bing Local Business Search API에 대한 검색 범주](local-categories.md)를 참조하세요.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>응답 사용

Bing Local Business Search API의 JSON 응답에는 `SearchResponse` 개체가 포함됩니다. API는 관련 검색 결과를 `places` 필드에 반환합니다. 결과가 없는 경우 `places` 필드는 응답에 포함되지 않습니다.

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>검색 결과 특성

API에서 반환된 JSON 결과는 다음 특성을 포함합니다.

* _type
* address
* entityPresentationInfo
* 지역
* id
* 이름
* routeablePoint
* telephone
* URL

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 일반적인 내용은 [Bing Local Search API v7](local-search-reference.md) 참조를 확인하세요.

> [!NOTE]
> 사용자 또는 사용자를 대신한 제3자는 모든 타사 서비스나 기능을 테스트, 개발, 학습, 배포 또는 제공하기 위해 Local Search API의 모든 데이터를 사용, 유지, 저장, 캐시, 공유 또는 배포할 수 없습니다. 


## <a name="example-json-response"></a>예제 JSON 응답

다음 JSON 응답은 쿼리 `?q=restaurant+in+Bellevue`에서 지정된 검색 결과를 포함합니다.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>다음 단계
- [Local Business Search 빠른 시작](quickstarts/local-quickstart.md)
- [Local Business Search Java 빠른 시작](quickstarts/local-search-java-quickstart.md)
- [Local Business Search Node 빠른 시작](quickstarts/local-search-node-quickstart.md)
- [Local Business Search Python 빠른 시작](quickstarts/local-search-python-quickstart.md)

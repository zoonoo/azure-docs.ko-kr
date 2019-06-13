---
title: Bing Web Search API 응답 구조 및 대답 형식
titleSuffix: Azure Cognitive Services
description: Bing Web Search API에서 사용하는 응답 형식 및 응답에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 47a573fd4ed4d058b9f9a265c8774f0856371b94
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390091"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Search API 응답 구조 및 대답 형식  

Bing Web Search에 검색 요청을 보내는 경우 응답 본문에 [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) 개체가 반환됩니다. 개체는 Bing이 쿼리와 관련이 있다고 결정한 각 응답에 대한 필드를 포함합니다. 이 예제에서는 Bing이 모든 응답을 반환한 경우 응답 개체를 설명합니다.

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

일반적으로 Bing Web Search는 응답의 하위 집합을 반환합니다. 예를 들어, 쿼리 용어가 *돛단배*였다면 응답은 `webPages`, `images` 및 `rankingResponse`를 포함할 수 있습니다. [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter)를 사용하여 웹 페이지를 필터링한 경우 외에는 응답은 항상 `webpages` 및 `rankingResponse` 응답을 포함합니다.

## <a name="webpages-answer"></a>웹 페이지 응답

[webPages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) 응답은 Bing Web Search가 쿼리와 관련이 있다고 결정한 웹 페이지에 대한 링크의 목록을 포함합니다. 목록의 각 [웹 페이지](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)는 페이지의 이름, URL, 표시 URL, 콘텐츠에 대한 짧은 설명 및 Bing이 콘텐츠를 찾은 날짜를 포함합니다.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

`name` 및 `url`을 사용하여 웹 페이지로 사용자를 이동시키는 하이퍼링크를 만듭니다.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>이미지 응답

[images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 응답은 Bing이 인식하기에 쿼리와 관련이 있는 이미지 목록을 포함합니다. 목록에서 각 [이미지](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image)는 이미지의 URL, 해당 크기, 차원 및 인코딩 형식을 포함합니다. 또한 이미지 개체는 이미지 썸네일의 URL 및 썸네일의 차원도 포함합니다.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

사용자의 디바이스에 따라 일반적으로 사용자가 나머지 이미지를 [넘겨볼 수 있는](paging-webpages.md) 옵션을 사용하여 썸네일의 하위 집합을 표시할 수 있습니다.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

또한 사용자가 썸네일 위로 커서를 움직이면 썸네일을 확대할 수도 있습니다. 확대하는 경우 이미지의 특성을 확인합니다. 예를 들어 `hostPageDisplayUrl`에서 호스트를 추출하여 이미지 아래에 표시합니다. 썸네일 크기 조정에 대한 내용은 [썸네일 크기 조정 및 자르기](./resize-and-crop-thumbnails.md)를 참조합니다.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

사용자가 썸네일을 클릭하면 이미지의 콜라주가 포함된 이미지에 대한 Bing의 검색 결과 페이지로 사용자를 이동시키려면 `webSearchUrl`을 사용합니다.

이미지 응답 및 이미지에 대한 자세한 내용은 [Image Search API](../bing-image-search/search-the-web.md)를 참조하세요.

## <a name="related-searches-answer"></a>관련 검색 응답

[relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) 응답은 다른 사용자가 수행한 가장 인기 있는 관련 쿼리의 목록을 포함합니다. 목록에서 각 [쿼리](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj)는 해당 쿼리에 대한 Bing의 검색 결과에 대한 쿼리 문자열(`text`), 적중 항목 강조 표시 문자가 포함된 쿼리 문자열(`displayText`) 및 URL(`webSearchUrl`)을 포함합니다.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

관련된 쿼리에 대한 Bing 검색 결과 페이지로 사용자를 이동시키는 하이퍼링크를 만들려면 `displayText` 쿼리 문자열 및 `webSearchUrl` URL을 사용합니다. 사용자의 Web Search API 쿼리에 있는 `text` 쿼리 문자열을 사용하여 직접 결과를 표시할 수도 있습니다.

`displayText`에서 강조 표시 마커를 처리하는 방법에 대한 자세한 내용은 [적중 항목 강조 표시](./hit-highlighting.md)를 참조하세요.

다음은 Bing.com에서 관련된 쿼리 사용의 예를 보여줍니다.

![Bing에서 관련된 검색 예제](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>비디오 응답

[videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 응답은 Bing이 인식하기에 쿼리와 관련이 있는 비디오 목록을 포함합니다. 목록에서 각 [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video)는 비디오의 URL, 해당 기간, 해당 차원 및 해당 인코딩 형식을 포함합니다. 또한 비디오 개체는 비디오 썸네일의 URL 및 썸네일의 차원도 포함합니다.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

사용자의 디바이스에 따라 일반적으로 사용자가 나머지 비디오를 볼 수 있는 옵션을 사용하여 비디오의 하위 집합을 표시할 수 있습니다. 비디오의 길이, 설명(이름) 및 특성(게시자)을 사용하여 비디오의 썸네일을 표시할 수 있습니다.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

사용자가 썸네일을 마우스로 가리키면 비디오의 썸네일 버전을 재생하도록 `motionThumbnailUrl`을 사용할 수 있습니다. 표시하는 경우 동작 썸네일의 특성을 확인합니다.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

사용자가 썸네일을 클릭하는 경우 비디오 보기 옵션은 다음과 같습니다.

- 호스트 웹 사이트(예: YouTube)에서 비디오를 보려면 `hostPageUrl`을 사용합니다.
- Bing 비디오 브라우저에서 비디오를 보려면 `webSearchUrl`을 사용합니다.
- 사용자의 고유한 환경에 비디오를 포함시키려면 `embedHtml`을 사용합니다.

비디오 응답 및 비디오에 대한 자세한 내용은 [Video Search API](../bing-video-search/search-the-web.md)를 참조하세요.

## <a name="news-answer"></a>뉴스 응답

[news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) 응답은 Bing이 인식하기에 쿼리와 관련이 있는 뉴스 기사의 목록을 포함합니다. 목록에서 각 [뉴스 기사](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle)는 기사의 제목, 설명 및 호스트의 웹 사이트에서 기사에 대한 URL을 포함합니다. 기사가 이미지를 포함하는 경우 개체는 이미지의 썸네일을 포함합니다.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

사용자의 디바이스에 따라 일반적으로 사용자가 나머지 기사를 볼 수 있는 옵션을 사용하여 뉴스 기사의 하위 집합을 표시할 수 있습니다. 호스트의 사이트에 있는 기사로 사용자를 이동시키는 하이퍼링크를 만들려면 `name` 및 `url`을 사용합니다. 기사가 이미지를 포함하는 경우 `url`을 사용하여 이미지를 클릭할 수 있도록 만듭니다. `provider`를 사용하여 기사의 특성을 확인합니다.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

뉴스 응답 및 뉴스 기사에 대한 자세한 내용은 [News Search API](../bing-news-search/search-the-web.md)를 참조하세요.

## <a name="computation-answer"></a>계산 응답

사용자가 수학 표현식 또는 단위 변환 쿼리를 입력하는 경우 응답은 [Computation](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) 응답을 포함할 수 있습니다. `computation` 응답은 정규화된 식 및 해당 결과를 포함합니다.

단위 변환 쿼리는 한 단위를 다른 단위로 변환하는 쿼리입니다. 예를 들어 *10m는 몇 피트인가?* 또는 *1/4컵은 몇 테이블스푼인가?* 가 있습니다.

다음은 *10m는 몇 피트인가?* 에 대한 `computation` 응답입니다.

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

다음은 수치 연산 쿼리 및 그에 해당하는 `computation` 응답입니다.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

수학 표현식은 다음과 같은 기호를 포함할 수 있습니다.

|기호|설명|
|------------|-----------------|
|+|더하기|
|-|빼기|
|/|나누기|
|*|곱하기|
|^|Power|
|!|계승값|
|.|Decimal|
|()|우선 순위 그룹화|
|[]|함수|

수학 표현식은 다음과 같은 상수를 포함할 수 있습니다.

|기호|설명|
|------------|-----------------|
|Pi|3.14159...|
|Degree|도|
|i|허수|
|e|e, 2.71828...|
|GoldenRatio|황금비, 1.61803...|

수학 표현식은 다음과 같은 함수를 포함할 수 있습니다.

|기호|설명|
|------------|-----------------|
|정렬|제곱근|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|삼각함수(인수 사용, 라디안 단위)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|역삼각함수(라디안 단위로 결과 제공)|
|Exp[x], E^x|지수 함수|
|Log[x]|자연 로그|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|쌍곡선 함수|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|역쌍곡선함수|

변수를 포함하는 수학 표현식(예: 4x+6=18, 여기서 x는 변수)은 지원되지 않습니다.

## <a name="timezone-answer"></a>표준 시간대 응답

사용자가 시간 또는 날짜 쿼리를 입력하는 경우 응답은 [TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) 응답을 포함할 수 있습니다. 이 응답은 암시적 또는 명시적 쿼리를 지원합니다. *몇 시입니까?* 와 같은 암시적 쿼리는 사용자의 위치를 기준으로 현지 시간을 반환합니다. *시애틀은 몇 시입니까?* 와 같은 명시적 쿼리는 워싱턴 주 시애틀의 현지 시간을 반환합니다.

`timeZone` 응답은 위치 이름, 지정된 위치의 현재 UTC 날짜 및 시간, UTC 오프셋을 제공합니다. 위치의 경계가 여러 표준 시간대 내에 있으면 응답은 경계 내 모든 표준 시간대의 현재 UTC 날짜 및 시간을 포함합니다. 예를 들어, 플로리다 주는 두 표준 시간대에 속하기 때문에 응답은 두 표준 시간대의 현지 날짜 및 시간을 포함합니다.  

쿼리 상태 또는 국가/지역에, Bing 해당 위치의 지리적 경계 내에서 기본 도시를 결정을 요청에서 반환 된 `primaryCityTime` 필드입니다. 경계가 여러 표준 시간대를 포함하는 경우 나머지 표준 시간대가 `otherCityTimes` 필드에 반환됩니다.

다음은 `timeZone` 응답을 반환하는 예제 쿼리입니다.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion 응답

Bing이 판단하기에 사용자가 다른 것을 검색하고자 하는 경우 응답은 [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) 개체를 포함합니다. 예를 들어, 사용자가 *carlos pen*을 검색하는 경우 사용자가 대신에 Carlos Pena를 검색하려는 것 같다고 Bing이 판단할 수 있습니다(*carlos pen*을 검색한 다른 사람들의 과거 검색을 기반으로). 다음은 예제 맞춤법 응답을 보여줍니다.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

다음은 Bing이 추천 단어를 사용하는 방법을 보여줍니다.

![Bing 추천 단어 예제](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="response-headers"></a>응답 헤더

Bing Web Search API의 응답에 다음 헤더가 포함될 수 있습니다.

|||
|-|-|
|`X-MSEdge-ClientID`|Bing에서 사용자에게 할당한 고유한 ID|
|`BingAPIs-Market`|요청을 이행하는 데 사용된 지역/국가|
|`BingAPIs-TraceId`|이 요청에 대한 Bing API 서버의 로그 항목(지원용)|

클라이언트 ID를 유지하고 후속 요청과 함께 반환하는 것이 특히 중요합니다. 이렇게 하면 검색은 검색 결과의 순위 지정에서 과거 컨텍스트를 사용하고 일관된 사용자 환경도 제공합니다.

그러나 JavaScript에서 Bing Web Search API를 호출하면 브라우저의 기본 제공 보안 기능(CORS)이 이러한 헤더 값으로의 액세스를 차단할 수 있습니다.

헤더에 액세스하기 위해 CORS 프록시를 통해 Bing Web Search API 요청을 만들 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 허용 목록에 추가하고 이를 JavaScript에서 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 있습니다.

[자습서 앱](tutorial-bing-web-search-single-page-app.md)이 선택적 클라이언트 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 프롬프트에서 다음 명령을 입력합니다.

    npm install -g cors-proxy-server

다음으로, HTML 파일에서 Bing Web Search API 엔드포인트를 다음으로 변경합니다.

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

    cors-proxy-server

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="response-headers-in-production"></a>프로덕션에서 응답 헤더

이전 응답에서 설명한 CORS 프록시 방식은 개발, 테스트 및 학습에 적합합니다.

프로덕션 환경에서는 Bing Web Search API를 사용하는 웹 페이지와 동일한 도메인에서 서버 쪽 스크립트를 호스트해야 합니다. 이 스크립트는 웹 페이지 JavaScript의 요청에 따라 API 호출을 수행하고 헤더를 포함한 모든 결과를 다시 클라이언트로 전달해야 합니다. 두 리소스(페이지 및 스크립트)가 원본을 공유하므로 CORS는 사용되지 않으며 특수 헤더가 웹 페이지의 JavaScript에 액세스할 수 있습니다.

이 방법은 또한 API 키가 서버 쪽 스크립트에서만 필요하기 때문에 API 키가 공개적으로 노출되지 않도록 합니다. 이 스크립트는 다른 메서드를 사용하여 요청이 승인되었는지 확인할 수 있습니다.

다음은 Bing이 추천 단어를 사용하는 방법을 보여줍니다.

![Bing 추천 단어 예제](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>다음 단계  

* [요청 제한](throttling-requests.md) 설명서를 검토합니다.  

## <a name="see-also"></a>참고 항목  

* [Bing Web Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

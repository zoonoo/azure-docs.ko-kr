---
title: '자습서: 단일 페이지 웹앱 만들기 - Bing Image Search API'
titleSuffix: Azure cognitive services
description: Bing Image Search API를 사용하면 웹에서 고품질 관련 이미지를 검색할 수 있습니다. 이 자습서를 사용하여 검색 쿼리를 API에 보낼 수 있는 단일 페이지 웹 애플리케이션을 빌드하고 웹 페이지 내에 결과를 표시합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 10bcbb4c1957735b0ddad6c97325c32be19ddcdb
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383392"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>자습서: Bing Image Search API를 사용하여 단일 페이지 앱 만들기

Bing Image Search API를 사용하면 웹에서 고품질 관련 이미지를 검색할 수 있습니다. 이 자습서를 사용하여 검색 쿼리를 API에 보낼 수 있는 단일 페이지 웹 애플리케이션을 빌드하고 웹 페이지 내에 결과를 표시합니다. 이 자습서는 Bing Web Search에 대한 [해당 자습서](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)와 유사합니다.

자습서 앱은 다음 방법을 보여 줍니다.

> [!div class="checklist"]
> * JavaScript에서 Bing Image Search API 호출 수행
> * 검색 옵션을 사용하여 검색 결과 개선
> * 검색 결과 표시 및 페이징
> * API 구독 키와 Bing 클라이언트 ID를 요청 및 처리합니다.

이 자습서의 전체 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 최신 버전의 [Node.js](https://nodejs.org/).
* Node.js의 [Express.js](https://expressjs.com/) 프레임워크. 소스 코드에 대한 설치 지침은 GitHub 샘플 추가 정보 파일에서 사용할 수 있습니다.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>사용자 구독 키 관리 및 저장

이 애플리케이션은 웹 브라우저의 영구적 저장소를 사용하여 API 구독 키를 저장합니다. 키가 저장되지 않은 경우 웹 페이지에서 해당 키를 요청하는 메시지를 사용자에게 표시하고 나중에 사용할 수 있도록 키를 저장합니다. 나중에 키가 API에서 거부되면 앱이 저장소에서 키를 제거합니다.


`storeValue` 및 `retrieveValue` 함수를 정의하여 `localStorage` 개체(브라우저가 지원하는 경우) 또는 쿠키를 사용합니다.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

`getSubscriptionKey()` 함수는 `retrieveValue`를 사용하여 이전에 저장된 키를 검색하려고 시도합니다. 키가 저장되지 않은 경우 해당 키를 요청하는 메시지를 사용자에게 표시하고 `storeValue`를 사용하여 키를 저장합니다.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML `<form>` 태그 `onsubmit`는 `bingWebSearch` 함수를 호출하여 검색 결과를 반환합니다. `bingWebSearch`는 `getSubscriptionKey`를 사용하여 각 쿼리를 인증합니다. 이전 정의에 표시된 것처럼 키를 입력하지 않은 경우 `getSubscriptionKey`는 사용자에게 키를 확인하는 메시지를 표시합니다. 그런 다음, 애플리케이션에서 계속 사용할 수 있도록 키가 저장됩니다.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>검색 요청 보내기

이 애플리케이션은 `onsubmit` 특성을 사용하여 `newBingImageSearch()`를 호출하는 사용자 검색 요청을 처음에 보내는 데 HTML `<form>`을 사용합니다.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

기본적으로 `onsubmit` 처리기는 `false`를 반환하여 양식이 제출되지 못하게 합니다.

## <a name="select-search-options"></a>검색 옵션 선택

![[Bing Image Search 양식]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Bing Image Search API는 검색 범위를 좁히고 검색 결과를 필터링하기 위한 여러 [필터 쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters)를 제공합니다. 이 애플리케이션의 HTML 양식은 다음 매개 변수 옵션을 사용하고 표시합니다.

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | 검색에 사용되는 지역/국가(위치 및 언어)를 선택하기 위한 드롭다운 메뉴입니다.                                                                                             |
| `query`      | 검색어를 입력한 텍스트 필드입니다.                                                                                                                                 |
| `aspect`     | 대략 정사각형, 너비 또는 높이 등 찾은 이미지의 비율을 선택하는 라디오 단추입니다.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | 필요에 따라 가장 최근의 일, 주 또는 월로 검색을 제한하기 위한 드롭다운 메뉴입니다.                                                                                          |
| `safe`       | Bing의 유해 정보 차단 기능을 사용하여 "성인" 결과를 필터링할지 여부를 나타내는 확인란입니다.                                                                                      |
| `count`      | 숨겨진 필드 각 요청에서 반환할 검색 결과 수입니다. 페이지당 더 적거나 더 많은 결과를 표시하도록 변경합니다.                                                            |
| `offset`     | 숨겨진 필드입니다. 요청의 첫 번째 검색 결과에 대한 오프셋으로, 페이징에 사용됩니다. 새 요청 시 `0`으로 다시 설정됩니다.                                                           |
| `nextoffset` | 숨겨진 필드 검색 결과를 수신하면 이 필드는 응답에서 `nextOffset`의 값으로 설정됩니다. 이 필드를 사용하면 후속 페이지에서 겹치는 결과를 방지합니다. |
| `stack`      | 숨겨진 필드 이전 페이지로 다시 탐색하기 위해 검색 결과 중 이전 페이지의 오프셋에서 JSON 인코딩된 목록                                                      |

`bingSearchOptions()` 함수는 앱의 API 요청에서 사용할 수 있는 부분 쿼리 문자열로 이러한 옵션의 형식을 지정합니다.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>요청 수행

검색 쿼리, 옵션 문자열 및 API 키를 통해 `BingImageSearch()` 함수는 XMLHttpRequest 개체를 사용하여 Bing Image Search 엔드포인트에 대한 요청을 수행합니다.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 요청이 성공적으로 완료되면 JavaScript는 “로드” 이벤트 처리기인 `handleBingResponse()`를 호출하여 성공한 HTTP GET 요청을 처리합니다.

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> 성공한 HTTP 요청에는 실패한 검색 정보가 포함될 수 있습니다. 검색 작업 중에 오류가 발생하는 경우 Bing Image Search API는 JSON 응답으로 200 이외의 HTTP 상태 코드 및 오류 정보를 반환합니다. 또한 요청의 속도가 제한된 경우 API에서 빈 응답을 반환합니다.

## <a name="display-the-search-results"></a>검색 결과 표시

검색 결과는 `renderSearchResults()` 함수에 의해 표시되며, 이 함수는 Bing Image Search 서비스에서 반환된 JSON을 사용하고 반환된 이미지 및 관련 검색에서 해당하는 렌더러 함수를 호출합니다.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

이미지 검색 결과는 JSON 응답 내의 최상위 `value` 개체에 포함됩니다. 이러한 검색 결과는 결과를 반복하고 각 항목을 HTML로 변환하는 `renderImageResults()`에 전달됩니다.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Bing Image Search API는 각각 최상위 개체에서 사용자 검색 환경을 안내하는 데 도움이 되는 네 가지 유형의 검색 제안을 반환할 수 있습니다.

| 제안         | 설명                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | 원래 검색에 포함된 중심 단어를 다른 단어로 바꾸는 쿼리입니다. 예를 들어 “red flowers”를 검색하는 경우 중심 단어는 “red”일 수 있고, 중심 제안은 “yellow flowers”일 수 있습니다. |
| `queryExpansions`  | 용어를 더 추가하여 원래 검색의 범위를 좁히는 쿼리입니다. 예를 들어 “Microsoft Surface”를 검색하는 경우 쿼리가 “Microsoft Surface Pro”로 확장될 수 있습니다.                                   |
| `relatedSearches`  | 또한 원래 검색을 입력했던 다른 사용자가 입력한 쿼리입니다. 예를 들어 “Mount Rainier”를 검색하는 경우 관련 검색은 “Mt. Saint Helens”일 수 있습니다.                       |
| `similarTerms`     | 원래 검색과 의미가 유사한 쿼리입니다. 예를 들어, "kittens"를 검색하는 경우 비슷한 용어로 "cute"가 있을 수 있습니다.                                                                   |

이 애플리케이션은 `relatedItems` 제안만 렌더링하고 결과 링크를 페이지의 사이드바에 배치합니다.

## <a name="rendering-search-results"></a>검색 결과 렌더링

이 애플리케이션에서 `searchItemRenderers` 개체에는 각 종류의 검색 결과에 대한 HTML을 생성하는 렌더러 함수가 포함됩니다.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

이러한 렌더러 함수는 다음 매개 변수를 허용합니다.

| 매개 변수         | 설명                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | URL 및 해당 설명과 같은 항목의 속성을 포함하는 JavaScript 개체입니다. |
| `index` | 해당 컬렉션 내에서 결과 항목의 인덱스입니다.                                          |
| `count` | 검색 결과 항목 컬렉션에 있는 항목 수입니다.                                  |

`index` 및 `count` 매개 변수는 결과에 번호를 매기고, 컬렉션에 대한 HTML을 생성하고, 콘텐츠를 구성하는 데 사용됩니다. 특히 다음을 수행합니다.

* 이미지 썸네일 크기를 계산합니다(높이가 90픽셀에 고정된 반면 너비는 최소 120픽셀로 다름).
* HTML `<img>` 태그를 작성하여 이미지 미리 보기를 표시합니다.
* 이미지와 해당 이미지를 포함하는 페이지에 연결되는 HTML `<a>` 태그를 빌드합니다.
* 이미지와 해당 이미지가 있는 사이트에 대한 정보를 표시하는 설명을 빌드합니다.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

썸네일 이미지의 `height` 및 `width`는 `<img>` 태그와 썸네일의 URL에 있는 `h` 및 `w` 필드 둘 다에 사용됩니다. 따라서 Bing은 정확히 해당 크기의 [썸네일](resize-and-crop-thumbnails.md)을 반환할 수 있습니다.

## <a name="persisting-client-id"></a>클라이언트 ID 유지

Bing Search API의 응답에는 후속 요청과 함께 API로 다시 전송되어야 하는 `X-MSEdge-ClientID` 헤더가 포함될 수 있습니다. 여러 개의 Bing Search API를 사용하는 경우 모두 동일한 클라이언트 ID를 사용해야 합니다(가능한 경우).

`X-MSEdge-ClientID` 헤더를 제공하면 Bing API가 모든 사용자 검색을 연결할 수 있어, 다음과 같은 경우 유용합니다.

첫째, Bing 검색 엔진이 이전 컨텍스트를 검색에 적용하여 사용자에게 보다 만족스러운 결과를 찾을 수 있습니다. 예를 들어 사용자가 이전에 항해와 관련된 용어를 검색한 경우 나중에 “knots”를 검색하면 항해에 사용되는 노트에 대한 정보가 우선적으로 반환될 수 있습니다.

둘째, Bing을 통해 선택된 임의 사용자는 새 기능이 폭넓게 사용되기 전에 이러한 새 기능을 경험해볼 수 있습니다. 각 요청에 동일한 클라이언트 ID를 제공하면 기능을 볼 수 있도록 선택된 사용자에게 항상 기능이 표시됩니다. 클라이언트 ID가 없으면 사용자의 검색 결과에서 기능이 임의로 나타나거나 사라지는 것처럼 보일 수 있습니다.

브라우저 보안 정책(CORS) 때문에 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용하지 못할 수 있습니다. 이러한 제한은 검색 응답의 원본이 해당 응답을 요청한 페이지와 다른 경우에 발생합니다. 프로덕션 환경에서는 웹 페이지와 동일한 도메인에 대해 API 호출을 수행하는 서버 쪽 스크립트를 호스트하여 이 정책 문제를 해결해야 합니다. 스크립트의 원본은 웹 페이지와 동일하므로 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용할 수 있습니다.

> [!NOTE]
> 그래도 프로덕션 웹 애플리케이션의 경우 서버 쪽에서 요청을 수행해야 합니다. 서버 쪽에서 수행하지 않을 경우 소스를 보는 누구나 사용할 수 있는 웹 페이지에 Bing Search API 키를 포함해야 합니다. 권한 없는 사람이 수행한 요청을 포함하여 API 구독 키를 통한 모든 사용량에 요금이 청구되므로, 키를 노출하지 않는 것이 중요합니다.

개발 목적으로 CORS 프록시를 통해 Bing Web Search API 요청을 수행할 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 허용 목록에 추가하고 JavaScript에서 응답 헤더를 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 포함됩니다.

자습서 앱이 클라이언트 ID 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 창에서 다음 명령을 실행합니다.

    npm install -g cors-proxy-server

다음으로, HTML 파일에서 Bing Web Search 엔드포인트를 변경합니다.

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

    cors-proxy-server

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search API를 사용하여 이미지 세부 정보 추출](tutorial-image-post.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search API 참조](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

---
title: Bing Image Search 단일 페이지 웹앱 | Microsoft Docs
description: 단일 페이지 웹 응용 프로그램에서 Bing Image Search API를 사용하는 방법을 보여줍니다.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377030"
---
# <a name="tutorial-single-page-web-app"></a>자습서: 단일 페이지 웹앱

Bing Image Search API를 사용하여 웹을 검색하고 검색 쿼리와 관련된 이미지 결과를 얻을 수 있습니다. 이 자습서에서는 Bing Image Search API를 사용하여 이 페이지에 검색 결과를 표시하는 단일 페이지 웹 응용 프로그램을 빌드합니다. 응용 프로그램에는 HTML, CSS 및 JavaScript 구성 요소가 포함됩니다.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> 클릭하면 페이지의 맨 아래에서 JSON 및 HTTP 제목이 JSON 응답 및 HTTP 요청 정보가 표시됩니다. 이러한 세부 정보는 서비스를 탐색할 때 유용합니다.

자습서 앱은 다음 방법을 보여줍니다.

> [!div class="checklist"]
> * JavaScript에서 Bing Image Search API 호출 수행
> * Bing Image Search API에 검색 옵션 전달
> * 검색 결과 표시
> * 검색 결과 페이지 표시
> * Bing 클라이언트 ID와 API 구독 키 처리
> * 발생할 수 있는 오류 처리

자습서 페이지는 완전히 독립적입니다. 즉, 모든 외부 프레임워크, 스타일시트 또는 이미지 파일을 사용하지 않습니다. 이 페이지는 광범위하게 지원되는 JavaScript 언어 기능만 사용하며, 모든 주요 웹 브라우저의 현재 버전에서 작동합니다.

이 자습서에서는 소스 코드의 선택된 부분에 대해서만 설명합니다. 전체 소스 코드를 [별도 페이지에서](tutorial-bing-image-search-single-page-app-source.md) 사용할 수 있습니다. 텍스트 편집기에 이 코드를 복사 및 붙여넣고 `bing.html`로 저장합니다.

> [!NOTE]
> 이 자습서는 [단일 페이지 Bing Web Search 앱 자습서](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)와 대체로 비슷하지만 이미지 검색 결과만을 처리합니다.

## <a name="app-components"></a>앱 구성 요소

단일 페이지 웹앱과 마찬가지로, 자습서 응용 프로그램은 다음 세 부분으로 구성되어 있습니다.

> [!div class="checklist"]
> * HTML - 페이지의 구조와 콘텐츠를 정의합니다.
> * CSS - 페이지의 모양을 정의합니다.
> * JavaScript - 페이지의 동작을 정의합니다.

이 자습서에서는 대부분의 HTML 또는 CSS를 자세히 다루지 않습니다. 간단하기 때문입니다.

HTML에는 사용자가 쿼리를 입력하고 검색 옵션을 선택하는 검색 양식이 포함되어 있습니다. 이 양식은 실제로 `<form>` 태그의 `onsubmit` 특성에 의한 검색을 수행하는 JavaScript에 연결됩니다.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

`onsubmit` 처리기는 `false`를 반환하여 양식이 서버에 제출되지 못하게 합니다. JavaScript 코드는 실제로 양식에서 필요한 정보를 수집하고 검색을 수행합니다.

또한 HTML에는 검색 결과가 나타나는 부분(HTML `<div>` 태그)이 포함됩니다.

## <a name="managing-subscription-key"></a>구독 키 관리

코드에 Bing Search API 구독 키를 포함할 필요가 없도록, 브라우저의 영구 저장소를 사용하여 키를 저장합니다. 키가 저장되지 않은 경우 사용자의 키에 대한 메시지를 표시하고 나중에 사용하도록 저장합니다. 키가 나중에 API에서 거부될 경우 사용자가 다시 하도록 저장된 키를 무효화합니다.

`localStorage` 개체(브라우저가 지원하는 경우) 또는 쿠키를 사용하는 `storeValue` 및 `retrieveValue` 함수를 정의합니다. `getSubscriptionKey()` 함수는 이러한 함수를 사용하여 사용자의 키를 저장하고 검색합니다.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
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

HTML `<form>` 태그 `onsubmit`는 `bingWebSearch` 함수를 호출하여 검색 결과를 반환합니다. `bingWebSearch`는 `getSubscriptionKey`를 사용하여 각 쿼리를 인증합니다. 이전 정의에 표시된 것처럼 키를 입력하지 않은 경우 `getSubscriptionKey`는 사용자에게 키를 확인하는 메시지를 표시합니다. 그런 다음, 키는 응용 프로그램에서 계속 사용될 수 있게 저장됩니다.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>검색 옵션 선택

![[Bing Image Search 양식]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

HTML 양식에는 다음과 같은 컨트롤이 포함됩니다.

| | |
|-|-|
|`where`|검색에 사용되는 지역/국가(위치 및 언어)를 선택하기 위한 드롭다운 메뉴입니다.|
|`query`|검색어를 입력한 텍스트 필드입니다.|
|`aspect`|대략 정사각형, 너비 또는 높이 등 찾은 이미지의 비율을 선택하는 라디오 단추입니다.|
|`color`|색 또는 흑백, 널리 사용되는 색을 선택합니다.
|`when`|필요에 따라 가장 최근의 일, 주 또는 월로 검색을 제한하기 위한 드롭다운 메뉴입니다.|
|`safe`|Bing의 유해 정보 차단 기능을 사용하여 "성인" 결과를 필터링할지 여부를 나타내는 확인란입니다.|
|`count`|숨겨진 필드 각 요청에서 반환할 검색 결과의 수입니다. 페이지당 더 적거나 더 많은 결과를 표시하도록 변경합니다.|
|`offset`|숨겨진 필드 요청의 첫 번째 검색 결과에 대한 오프셋으로, 페이징에 사용됩니다. 새 요청 시 `0`으로 다시 설정됩니다.|
|`nextoffset`|숨겨진 필드 검색 결과를 수신하면 이 필드는 응답에서 `nextOffset`의 값으로 설정됩니다. 이 필드를 사용하면 후속 페이지에서 겹치는 결과를 방지합니다.|
|`stack`|숨겨진 필드 이전 페이지로 다시 탐색하기 위해 검색 결과 중 이전 페이지의 오프셋에서 JSON 인코딩된 목록|

> [!NOTE]
> Bing Image Search에서는 여러 추가 쿼리 매개 변수를 제공합니다. 여기서는 그 중 일부만 사용합니다.

JavaScript 함수 `bingSearchOptions()`는 이러한 필드를 Bing Search API에 필요한 형식의 부분 쿼리 문자열로 변환합니다.

```javascript
// build query options from the HTML form
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

예를 들어 유해 정보 차단 기능은 `moderate`를 기본값으로 사용하는 `strict`, `moderate` 또는 `off`일 수 있습니다. 그러나 양식에서는 2개의 상태만 있는 확인란을 사용합니다. JavaScript 코드는 이 설정을 `strict` 또는 `off`로 변환합니다(`moderate`를 사용하지 않음).

## <a name="performing-the-request"></a>요청 수행

쿼리, 옵션 문자열 및 API 키가 제공될 경우 `BingImageSearch` 함수는 `XMLHttpRequest` 개체를 사용하여 Bing Image Search 엔드포인트에 대해 요청을 수행합니다.

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

HTTP 요청이 성공적으로 완료되면 JavaScript는 `load` 이벤트 처리기, `handleBingResponse()` 함수를 호출하여 API에 대한 성공적인 HTTP GET 요청을 처리합니다. 

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
> 성공적인 HTTP 요청이 반드시 검색 자체가 성공했음을 의미하는 것은 *아닙니다*. 검색 작업에 오류가 발생하는 경우 Bing Image Search API는 200 이외의 HTTP 상태 코드를 반환하고 JSON 응답에 오류 정보를 포함합니다. 또한 요청의 속도가 제한되었으면 API는 빈 응답을 반환합니다.

위의 두 함수에 포함된 많은 코드는 대부분 오류 처리에만 사용됩니다. 다음 단계에서 오류가 발생할 수 있습니다.

|단계|잠재적 오류|처리 방법|
|-|-|-|
|JavaScript 요청 개체 빌드|잘못된 URL|`try`/`catch` 차단|
|요청하기|네트워크 오류, 중단된 연결|`error` 및 `abort` 이벤트 처리기|
|검색 수행|잘못된 요청, 잘못된 JSON, 속도 제한|`load` 이벤트 처리기에서 테스트|

오류는 오류에 대한 알려진 정보로 `renderErrorMessage()`를 호출하여 처리됩니다. 응답이 전체 오류 테스트 단계를 통과하면 `renderSearchResults()`를 호출하여 페이지에 검색 결과를 표시합니다.

## <a name="displaying-search-results"></a>검색 결과 표시

검색 결과를 표시하기 위한 주 함수는 `renderSearchResults()`입니다. 이 함수는 Bing Image Search 서비스에서 반환된 JSON을 사용하고, 이미지 및 관련 검색(있는 경우)을 렌더링합니다.

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

주 이미지 검색 결과는 JSON 응답에서 최상위 수준 `value` 개체로 반환됩니다. 이 결과를 `renderImageResults()` 함수에 전달하면 결과를 따라 반복되면서 별도 함수가 호출되고 각 항목이 HTML로 렌더링됩니다. 결과 HTML은 `renderSearchResults()`로 반환되고, 여기서 페이지의 `results` 부분에 삽입됩니다.

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

Bing Image Search API는 최대 4가지 다른 종류의 관련 검색을 고유한 최상위 수준 개체에 반환합니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

|||
|-|-|
|`pivotSuggestions`|원래 검색에 포함된 중심 단어를 다른 단어로 바꾸는 쿼리입니다. 예를 들어, "red flowers"를 검색하는 경우 중심 단어는 "red"일 수 있으며 중심 제안은 "yellow flowers"일 수 있습니다|
|`queryExpansions`|용어를 더 추가하여 원래 검색의 범위를 좁히는 쿼리입니다. 예를 들어, "Microsoft Surface"를 검색하는 경우 쿼리가 "Microsoft Surface Pro"와 같이 확장될 수 있습니다.|
|`relatedSearches`|또한 원래 검색을 입력한 다른 사용자가 입력했던 쿼리입니다. 예를 들어 "Mount Rainier"를 검색하는 경우 관련 검색이 "Mt. Saint Helens"일 수 있습니다.|
|`similarTerms`|원래 검색과 의미가 유사한 쿼리입니다. 예를 들어, "kittens"를 검색하는 경우 비슷한 용어로 "cute"가 있을 수 있습니다.|

이전에 `renderSearchResults()`에 표시된 것처럼 `relatedItems` 제안만 렌더링하고 결과 링크를 페이지의 사이드바에 표시할 수 있습니다.

## <a name="rendering-result-items"></a>결과 항목 렌더링

JavaScript 코드에서 개체 `searchItemRenderers`는 각 종류의 검색 결과에 대해 HTML을 생성하는 *renderers:* 함수를 포함합니다.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

렌더러 함수는 다음 매개 변수를 사용할 수 있습니다.

| | |
|-|-|
|`item`|URL 및 해당 설명과 같은 항목의 속성을 포함하는 JavaScript 개체입니다.|
|`index`|해당 컬렉션 내에서 결과 항목의 인덱스입니다.|
|`count`|검색 결과 항목 컬렉션에 있는 항목의 수입니다.|

`index` 및 `count` 매개 변수는 결과에 번호를 매기고, 컬렉션 시작 또는 끝에 대한 특별한 HTML을 생성하고, 특정 개수의 항목 다음에 줄 바꿈을 삽입하는 등의 작업에 사용될 수 있습니다. 렌더러에서 이 기능이 필요하지 않은 경우 이러한 두 매개 변수를 수락할 필요가 없습니다.

`images` 렌더러를 좀 더 자세히 살펴보겠습니다.

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

이미지 렌더러는 다음과 같이 작동합니다.

> [!div class="checklist"]
> * 이미지 썸네일 크기를 계산합니다(높이가 90픽셀에 고정된 반면 너비는 최소 120픽셀로 다름).
> * HTML `<img>` 태그를 작성하여 이미지 미리 보기를 표시합니다. 
> * 이미지와 해당 이미지를 포함하는 페이지에 연결되는 HTML `<a>` 태그를 작성합니다.
> * 이미지와 해당 이미지가 있는 사이트에 대한 정보를 표시하는 설명을 작성합니다.

첫 번째 이미지 결과 전에 `<p>` 태그를 삽입하기 위해 `index` 변수를 테스트합니다. 그렇지 않으면 썸네일은 서로 맞추고 브라우저 창에서 필요에 따라 래핑합니다.

미리 보기 크기는 `<img>` 태그와 미리 보기의 URL에 있는 `h` 및 `w` 필드 둘 다에 사용됩니다. 그러면 [Bing 미리 보기 서비스](resize-and-crop-thumbnails.md)에서 정확히 해당 크기를 갖는 미리 보기를 전달합니다.

## <a name="persisting-client-id"></a>클라이언트 ID 유지

Bing Search API의 응답에는 후속 요청과 함께 API로 다시 전송되어야 하는 `X-MSEdge-ClientID` 헤더가 포함될 수 있습니다. 여러 Bing Search API가 사용될 경우 모두 동일한 클라이언트 ID가 사용되어야 합니다(가능한 경우).

`X-MSEdge-ClientID` 헤더를 제공하면 Bing API가 모든 사용자 검색을 연결할 수 있습니다. 이로 인해 2가지 중요한 이점을 얻게 됩니다.

첫째, Bing 검색 엔진이 이전 컨텍스트를 검색에 적용하여 사용자를 더 많이 만족시키는 결과를 찾을 수 있도록 합니다. 예를 들어, 사용자가 이전에 항해와 관련된 용어를 검색한 경우 나중에 “knots”를 검색하면 항해에 사용되는 노트에 대한 정보가 우선적으로 반환될 수 있습니다.

둘째, Bing을 통해 선택된 임의 사용자는 새 기능이 폭넓게 사용되기 전에 이러한 새 기능을 경험해볼 수 있습니다. 각 요청에 동일한 클라이언트 ID를 제공하면 선택된 사용자에게 항상 이를 보는 기능이 표시됩니다. 클라이언트 ID가 없으면 사용자의 결과 페이지에서 기능이 무작위로 나타나거나 사라지는 것처럼 보일 수 있습니다.

브라우저 보안 정책(CORS) 때문에 `X-MSEdge-ClientID` 헤더를 JavaScript에서 사용하지 못할 수 있습니다. 이러한 제한은 검색 응답이 해당 응답을 요청한 페이지와는 다른 원본을 가질 때 발생합니다. 프로덕션 환경에서 웹 페이지와 동일한 도메인에 대해 API 호출을 수행하는 서버 쪽 스크립트를 호스트하여 이 정책 문제를 해결해야 합니다. 스크립트는 웹 페이지와 동일한 원본을 가지므로 `X-MSEdge-ClientID` 헤더를 JavaScript에서 사용할 수 있습니다.

> [!NOTE]
> 그래도 프로덕션 웹 응용 프로그램에서 서버 쪽 요청을 수행해야 합니다. 그렇지 않은 경우 Bing Search API 키를 웹 페이지에 포함해야만 소스를 보는 누구나 사용할 수 있게 됩니다. API 구독 키에 따른 모든 사용에 비용이 부과되며 허가되지 않은 파티에서 수행한 요청도 마찬가지이므로 키를 노출하지 않는 것이 중요합니다.

개발 목적으로 CORS 프록시를 통해 Bing Web Search API 요청을 만들 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 허용 목록에 추가하고 이를 JavaScript에서 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 있습니다.

자습서 앱이 클라이언트 ID 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 창에서 다음 명령을 실행합니다.

    npm install -g cors-proxy-server

다음으로, HTML 파일에서 Bing Web Search 엔드포인트를 변경합니다.

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

    cors-proxy-server

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search API 참조](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)


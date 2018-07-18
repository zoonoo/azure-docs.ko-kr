---
title: Bing Web Search 단일 페이지 웹앱 | Microsoft Docs
description: 단일 페이지 웹 응용 프로그램에서 Bing Web Search API를 사용하는 방법을 보여줍니다.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377031"
---
# <a name="tutorial-single-page-web-app"></a>자습서: 단일 페이지 웹앱

Bing Web Search API를 사용하여 웹을 검색하고 검색 쿼리와 관련된 다양한 형식의 결과를 얻을 수 있습니다. 이 자습서에서는 Bing Web Search API를 사용하여 이 페이지에 검색 결과를 표시하는 단일 페이지 웹 응용 프로그램을 빌드합니다. 응용 프로그램에는 HTML, CSS 및 JavaScript 구성 요소가 포함됩니다.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> 클릭하면 페이지의 맨 아래에서 JSON 및 HTTP 제목이 JSON 응답 및 HTTP 요청 정보가 표시됩니다. 이러한 세부 정보는 서비스를 탐색할 때 유용합니다.

자습서 앱은 다음 방법을 보여줍니다.

> [!div class="checklist"]
> * JavaScript에서 Bing Web Search API 호출 수행
> * Bing Web Search API에 검색 옵션 전달
> * 웹, 뉴스, 이미지 및 비디오 검색 결과 표시
> * 검색 결과 페이지 표시
> * Bing 클라이언트 ID와 API 구독 키 처리
> * 발생할 수 있는 오류 처리

자습서 페이지는 완전히 독립적입니다. 즉, 모든 외부 프레임워크, 스타일시트 또는 이미지 파일을 사용하지 않습니다. 이 페이지는 광범위하게 지원되는 JavaScript 언어 기능만 사용하며, 모든 주요 웹 브라우저의 현재 버전에서 작동합니다.

이 자습서에서는 소스 코드의 선택된 부분에 대해서만 설명합니다. 전체 소스 코드를 [별도 페이지에서](tutorial-bing-web-search-single-page-app-source.md) 사용할 수 있습니다. 텍스트 편집기에 이 코드를 복사 및 붙여넣고 `bing.html`로 저장합니다.

## <a name="app-components"></a>앱 구성 요소

단일 페이지 웹앱과 마찬가지로, 자습서 응용 프로그램은 다음 세 부분으로 구성되어 있습니다.

> [!div class="checklist"]
> * HTML - 페이지의 구조와 콘텐츠를 정의합니다.
> * CSS - 페이지의 모양을 정의합니다.
> * JavaScript - 페이지의 동작을 정의합니다.

이 자습서에서는 대부분의 HTML 또는 CSS를 자세히 다루지 않습니다. 간단하기 때문입니다.

HTML에는 사용자가 쿼리를 입력하고 검색 옵션을 선택하는 검색 양식이 포함되어 있습니다. 이 양식은 실제로 `<form>` 태그의 `onsubmit` 특성에 의한 검색을 수행하는 JavaScript에 연결됩니다.

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` 처리기는 `false`를 반환하여 양식이 서버에 제출되지 못하게 합니다. JavaScript 코드는 실제로 양식에서 필요한 정보를 수집하고 검색을 수행합니다.

또한 HTML에는 검색 결과가 나타나는 부분(HTML `<div>` 태그)이 포함됩니다.

## <a name="managing-subscription-key"></a>구독 키 관리

코드에 Bing Search API 구독 키를 포함할 필요가 없도록, 브라우저의 영구 저장소를 사용하여 키를 저장합니다. 키가 저장되지 않은 경우 사용자의 키에 대한 메시지를 표시하고 나중에 사용하도록 저장합니다. 키가 나중에 API에서 거부될 경우 사용자에게 다시 메시지가 표시되도록 저장된 키를 무효화합니다.

`localStorage` 개체(브라우저가 지원하는 경우) 또는 쿠키를 사용하는 `storeValue` 및 `retrieveValue` 함수를 정의합니다. `getSubscriptionKey()` 함수는 이러한 함수를 사용하여 사용자의 키를 저장하고 검색합니다.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

HTML `form` 태그 `onsubmit`는 `bingWebSearch` 함수를 호출하여 검색 결과를 반환합니다. `bingWebSearch`는 `getSubscriptionKey`를 사용하여 각 쿼리를 인증합니다. 이전 정의에 표시된 것처럼 키를 입력하지 않은 경우 `getSubscriptionKey`는 사용자에게 키를 확인하는 메시지를 표시합니다. 그런 다음, 키는 응용 프로그램에서 계속 사용될 수 있게 저장됩니다.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>검색 옵션 선택

![[Bing Web Search 양식]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

HTML 양식에는 다음 이름의 요소가 포함됩니다.

| | |
|-|-|
| `where` | 검색에 사용되는 지역/국가(위치 및 언어)를 선택하기 위한 드롭다운 메뉴입니다. |
| `query` | 검색어를 입력한 텍스트 필드입니다. |
| `what` | 특정 종류의 결과를 승격하기 위한 확인란입니다. 예를 들어 이미지를 승격하면 이미지 순위가 높아집니다. |
| `when` | 필요에 따라 가장 최근의 일, 주 또는 월로 검색을 제한하기 위한 드롭다운 메뉴입니다. |
| `safe` | Bing의 유해 정보 차단 기능을 사용하여 "성인" 결과를 필터링할지 여부를 나타내는 확인란입니다. |
| `count` | 숨겨진 필드 각 요청에서 반환할 검색 결과의 수입니다. 페이지당 더 적거나 더 많은 결과를 표시하도록 변경합니다. |
| `offset` | 숨겨진 필드 요청의 첫 번째 검색 결과에 대한 오프셋으로, 페이징에 사용됩니다. 새 요청 시 `0`으로 다시 설정됩니다. |

> [!NOTE]
> Bing Web Search에서는 여러 추가 쿼리 매개 변수를 제공합니다. 여기서는 그 중 일부만 사용합니다.

JavaScript 함수 `bingSearchOptions()`는 Bing Search API에 필요한 형식으로 이러한 필드를 변환합니다.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

예를 들어, 실제 API 호출의 `SafeSearch` 매개 변수는 `strict`, `moderate` 또는 `off`일 수 있고 기본값은 `moderate`입니다. 그러나 예제 양식에서는 2개의 상태만 있는 확인란을 사용합니다. JavaScript 코드는 이 설정을 `strict` 또는 `off`로 변환합니다(`moderate`는 사용되지 않음).

**승격** 확인란이 선택되면 `answerCount` 매개 변수도 쿼리에 추가합니다. `promote` 매개 변수를 사용할 때 `answerCount`가 필요합니다. 단순히 `9`(Bing Web Search API에서 지원하는 결과 형식 수)로 설정하여 최대 가능한 결과 형식의 수를 가져와야 합니다.

> [!NOTE]
> 결과 형식을 승격하면 검색 결과에 이러한 종류의 결과가 포함되도록 *보장*하지 않습니다. 대신 프로모션은 일반적인 해당 순위를 기준으로 이러한 종류의 결과 순위를 높입니다. 특정 종류의 결과에 대한 검색을 제한하려면 `responseFilter` 쿼리 매개 변수를 사용하거나 Bing Image Search 또는 Bing News Search와 같은 구체적인 엔드포인트를 호출합니다.

또한 `textDecoration` 및 `textFormat` 쿼리 매개 변수를 전송하여 검색 용어가 검색 결과에 굵은 글꼴로 지정되도록 합니다. 이러한 값은 스크립트에서 하드코딩됩니다.

## <a name="performing-the-request"></a>요청 수행

쿼리, 옵션 문자열 및 API 키가 제공될 경우 `BingWebSearch` 함수는 `XMLHttpRequest` 개체를 사용하여 Bing Web Search 엔드포인트에 대해 요청을 수행합니다.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> 성공적인 HTTP 요청이 반드시 검색 자체가 성공했음을 의미하는 것은 *아닙니다*. 검색 작업에 오류가 발생하는 경우 Bing Web Search API는 200 이외의 HTTP 상태 코드를 반환하고 JSON 응답에 오류 정보를 포함합니다. 또한 요청의 속도가 제한되었으면 API는 빈 응답을 반환합니다.

위의 두 함수에 포함된 많은 코드는 대부분 오류 처리에만 사용됩니다. 다음 단계에서 오류가 발생할 수 있습니다.

|단계|잠재적 오류|처리 방법|
|-|-|-|
|JavaScript 요청 개체 빌드|잘못된 URL|`try`/`catch` 차단|
|요청하기|네트워크 오류, 중단된 연결|`error` 및 `abort` 이벤트 처리기|
|검색 수행|잘못된 요청, 잘못된 JSON, 속도 제한|`load` 이벤트 처리기에서 테스트|

오류는 오류에 대한 알려진 정보로 `renderErrorMessage()`를 호출하여 처리됩니다. 응답이 전체 오류 테스트 단계를 통과하면 `renderSearchResults()`를 호출하여 페이지에 검색 결과를 표시합니다.

## <a name="displaying-search-results"></a>검색 결과 표시

ing Web Search API [에서는 지정된 순서로 결과를 표시해야 합니다](useanddisplayrequirements.md). API가 다양한 종류의 응답을 반환할 수 있으므로 JSON 응답에서 최상위 `WebPages` 컬렉션을 반복하고 해당 결과를 표시하는 것으로는 충분하지 않습니다. (한 종류의 결과만 필요한 경우 `responseFilter` 쿼리 매개 변수 또는 다른 Bing Search 엔드포인트를 사용합니다.)

대신 검색 결과에서 `rankingResponse`를 사용하여 표시할 결과를 정렬합니다. 이 개체는 `WebPages` `News`, `Images` 및/또는 `Videos` 컬렉션 또는 JSON 응답에서 다른 최상위 대답 컬렉션 파일에 있는 항목을 참조합니다.

`rankingResponse`에는 `pole`, `mainline` 및 `sidebar`로 지정된 검색 결과 중 최대 3개의 컬렉션이 포함될 수 있습니다. 

있는 경우 `pole`은 가장 관련된 검색 결과이며 눈에 띄게 표시됩니다. `mainline`은 대량의 검색 결과를 나타냅니다. `pole` 바로 뒤에 중요 결과가 표시됩니다(또는 `pole`가 표시되지 않는 경우 먼저 표시됨). 

마지막으로 `sidebar`는 보조 검색 결과를 나타냅니다. 종종 이러한 결과는 관련된 검색 또는 이미지입니다. 가능하면 실제 사이드바에서 이러한 결과를 표시합니다. 화면 제한으로 인해 사이드바가 실용적이지 않은 경우(예: 모바일 장치에서) `mainline` 결과 뒤에 표시됩니다.

`rankingResponse` 컬렉션의 각 항목은 다르지만 동등한 두 가지 방법으로 실제 검색 결과 항목을 나타냅니다.

| | |
|-|-|
|`id`|`id`는 URL처럼 보이지만 링크에 사용되지 않아야 합니다. 순위 결과의 `id` 형식은 대답 컬렉션 *또는* 전체 도움말 컬렉션(`Images` 등)에서 결과 항목 중 하나의 `id`와 일치합니다.
|`answerType`, `resultIndex`|`answerType`은 결과가 포함된 최상위 응답 컬렉션을 나타냅니다(예: `WebPages`). `resultIndex`는 해당 컬렉션 내에서 결과 인덱스를 참조합니다. `resultIndex`가 생략되면 순위 결과는 전체 컬렉션을 나타냅니다.

> [!NOTE]
> 검색 응답의 이 부분에 대한 자세한 내용은 [순위 결과](rank-results.md)를 참조하세요.

응용 프로그램에서 가장 편리한 참조된 검색 결과 항목을 찾는 메서드를 사용할 수 있습니다. 자습서 코드에서 `answerType` 및 `resultIndex`를 사용하여 각 검색 결과를 찾습니다.

마지막으로 `renderSearchResults()` 함수를 확인합니다. 이 함수는 검색 결과의 세 가지 섹션을 나타내는 세 개의 `rankingResponse` 컬렉션을 반복합니다. 각 섹션에서 `renderResultsItems()`를 호출하여 해당 섹션의 결과를 렌더링합니다.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()`는 차례로 각 `rankingResponse` 섹션에서 반복하고, `answerType` 및 `resultIndex` 필드를 사용하여 검색 결과에 각 순위 결과를 매핑하고, 결과 HTML을 생성하는 적절한 렌더링 함수를 호출합니다. 

`resultIndex`가 지정된 순위 항목에 대해 지정되지 않으면 `renderResultsItems()`는 해당 형식의 모든 결과에서 반복하고 각 항목에 대한 렌더링 함수를 호출합니다. 

어떤 방법을 사용하든지 결과 HTML이 페이지의 적절한 `<div>` 요소에 삽입됩니다.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>결과 항목 렌더링

JavaScript 코드에서 개체 `searchItemRenderers`는 각 종류의 검색 결과에 대해 HTML을 생성하는 *renderers:* 함수를 포함합니다.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> 자습서 앱에는 웹 페이지, 뉴스 항목, 이미지, 비디오 및 관련 검색의 렌더러가 있습니다. 고유한 응용 프로그램에는 수신할 수 있는 종류의 결과에 대한 렌더러가 필요합니다. 여기에는 계산, 맞춤법 제안, 엔터티, 표준 시간대 및 정의가 포함될 수 있습니다.

일부 렌더링 함수는 `item` 매개 변수인 단일 검색 결과 나타내는 JavaScript 개체만을 허용합니다. 다른 사용자는 다양한 컨텍스트에서 다르게 항목을 렌더링하는 데 사용할 수 있는 추가 매개 변수를 허용합니다. (이 정보를 사용하지 않는 렌더러는 이러한 매개 변수를 허용할 필요가 없습니다.)

컨텍스트 인수는 다음과 같습니다.

| | |
|-|-|
|`section`|항목이 표시되는 결과 섹션(`pole`, `mainline` 또는 `sidebar`)
|`index`<br>`count`|`rankingResponse` 항목이 지정된 컬렉션에 모든 결과가 표시되도록 지정한 경우에 사용할 수 있습니다. 그렇지 않으면 `undefined`입니다. 이러한 매개 변수는 해당 컬렉션 내에 있는 항목의 인덱스 및 해당 컬렉션에 있는 총 항목 수를 수신합니다. 이 정보를 사용하여 결과를 세고 첫 번째 또는 마지막 결과에 대해 다른 HTML을 생성할 수 있습니다.|

자습서 앱에서 `images` 및 `relatedSearches` 렌더러는 모두 컨텍스트 인수를 사용하여 생성한 HTML을 사용자 지정합니다. `images` 렌더러를 좀 더 자세히 살펴보겠습니다.

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

이미지 렌더러는 다음과 같이 작동합니다.

> [!div class="checklist"]
> * 이미지 썸네일 크기를 계산합니다(높이가 60픽셀에 고정된 반면 너비는 다름).
> * 컨텍스트에 따라 이미지 결과의 앞에 오는 HTML을 삽입합니다.
> * 이미지가 포함된 페이지로 연결되는 HTML `<a>`태그를 빌드합니다.
> * HTML `<img>` 태그를 작성하여 이미지 미리 보기를 표시합니다. 

이미지 렌더러는 `section` 및 `index` 변수를 사용하여 나타나는 위치에 따라 다르게 결과를 표시합니다. 줄 바꿈(`<br>` 태그)은 사이드바에서 이미지 결과 사이에 삽입되므로 사이드바가 이미지 열을 표시합니다. 다른 섹션에서 첫 번째 이미지 결과 `(index === 0)`는 `<p>` 태그가 앞에 옵니다. 그렇지 않으면 썸네일은 서로 맞추고 브라우저 창에서 필요에 따라 래핑합니다.

미리 보기 크기는 `<img>` 태그와 미리 보기의 URL에 있는 `h` 및 `w` 필드 둘 다에 사용됩니다. 그러면 [Bing 미리 보기 서비스](resize-and-crop-thumbnails.md)에서 정확히 해당 크기를 갖는 미리 보기를 전달합니다. `title` 및 `alt` 특성(이미지의 텍스트 설명)은 이미지의 이름 및 URL의 호스트 이름에서 생성됩니다.

이미지는 중요 검색 결과에서 아래 그림과 같이 표시됩니다.

![[Bing 이미지 결과]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

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
> [Visual Search 모바일 앱 자습서](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Bing Web Search API 참조](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

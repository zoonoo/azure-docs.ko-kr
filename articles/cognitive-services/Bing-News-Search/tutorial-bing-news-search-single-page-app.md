---
title: '자습서: 단일 페이지 웹앱 만들기 - Bing News Search API'
titlesuffix: Azure Cognitive Services
description: 이 자습서를 사용하여 검색 쿼리를 Bing News API에 보낼 수 있는 단일 페이지 웹 애플리케이션을 빌드하고 웹 페이지 내에 결과를 표시합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 02cfd00b8ab1f51c46e745dae480e39dee2ed710
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262339"
---
# <a name="tutorial-create-a-single-page-web-app"></a>자습서: 단일 페이지 웹앱 만들기

Bing News Search API를 사용하여 웹을 검색하고 검색 쿼리와 관련된 뉴스 유형의 결과를 얻을 수 있습니다. 이 자습서에서는 Bing News Search API를 사용하여 페이지에 검색 결과를 표시하는 단일 페이지 웹 애플리케이션을 빌드합니다. 애플리케이션에는 HTML, CSS 및 JavaScript 구성 요소가 포함됩니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html)에 제공됩니다.

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> 클릭하면 페이지 맨 아래의 JSON 및 HTTP 제목에 JSON 응답 및 HTTP 요청 정보가 표시됩니다. 이러한 세부 정보는 서비스를 탐색할 때 유용할 수 있습니다.

자습서 앱은 다음 방법을 보여 줍니다.
> [!div class="checklist"]
> * JavaScript에서 Bing News Search API 호출 수행
> * Bing News Search API에 검색 옵션 전달
> * 24시간의 기간, 지난 주, 지난 달 또는 사용 가능한 모든 시간의 임의 유형, 비즈니스, 상태 또는 정책의 네 가지 범주에 해당하는 뉴스 검색 결과 표시
> * 검색 결과 페이지 표시
> * Bing 클라이언트 ID와 API 구독 키 처리
> * 발생할 수 있는 오류 처리

자습서 페이지는 완전히 독립적입니다. 즉, 모든 외부 프레임워크, 스타일시트 또는 이미지 파일을 사용하지 않습니다. 이 페이지는 광범위하게 지원되는 JavaScript 언어 기능만 사용하며, 모든 주요 웹 브라우저의 현재 버전에서 작동합니다.

## <a name="app-components"></a>앱 구성 요소
단일 페이지 웹앱과 마찬가지로, 이 자습서 애플리케이션은 다음 세 부분으로 구성되어 있습니다.

> [!div class="checklist"]
> * HTML - 페이지의 구조와 콘텐츠를 정의합니다.
> * CSS - 페이지의 모양을 정의합니다.
> * JavaScript - 페이지의 동작을 정의합니다.

대부분의 HTML 및 CSS는 기존 방식이므로 이 자습서에서 다루지 않습니다. HTML에는 사용자가 쿼리를 입력하고 검색 옵션을 선택하는 검색 양식이 포함되어 있습니다. 이 양식은 실제로 `<form>` 태그의 `onsubmit` 특성을 사용하여 검색을 수행하는 JavaScript에 연결됩니다.

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
`onsubmit` 처리기는 `false`를 반환하여 양식이 서버에 제출되지 못하게 합니다. JavaScript 코드는 양식에서 필요한 정보를 수집하고 검색을 수행합니다.

또한 HTML에는 검색 결과가 나타나는 부분(HTML `<div>` 태그)이 포함됩니다.

## <a name="managing-subscription-key"></a>구독 키 관리

Bing Search API 구독 키를 코드에 포함할 필요가 없도록, 여기서는 브라우저의 영구적 저장소를 사용하여 키를 저장합니다. 키가 저장되기 전에 사용자에게 키를 확인하는 메시지가 표시됩니다. 키가 나중에 API에서 거부될 경우 저장된 키를 무효화하므로 사용자에게 다시 메시지가 표시됩니다.

`localStorage` 개체(일부 브라우저에서만 지원) 또는 쿠키를 사용하는 `storeValue` 및 `retrieveValue` 함수를 정의합니다. `getSubscriptionKey()` 함수는 이러한 함수를 사용하여 사용자 키를 저장하고 검색합니다.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
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
HTML `<form>` 태그 `onsubmit`는 `bingWebSearch` 함수를 호출하여 검색 결과를 반환합니다. `bingWebSearch`는 `getSubscriptionKey()`를 사용하여 각 쿼리를 인증합니다. 이전 정의에 표시된 것처럼 키를 입력하지 않은 경우 `getSubscriptionKey`는 사용자에게 키를 확인하는 메시지를 표시합니다. 그런 다음, 애플리케이션에서 계속 사용할 수 있도록 키가 저장됩니다.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>검색 옵션 선택
다음 그림에서는 학교 자금에 대한 뉴스 검색을 정의하는 쿼리 텍스트 상자 및 옵션을 보여 줍니다.

![Bing News Search 옵션](media/news-search-categories.png)

HTML 양식에는 다음 이름의 요소가 포함됩니다.

|요소|설명|
|-|-|
| `where` | 검색에 사용되는 지역/국가(위치 및 언어)를 선택하기 위한 드롭다운 메뉴입니다. |
| `query` | 검색어를 입력하기 위한 텍스트 필드입니다. |
| `category` | 특정 종류의 결과를 승격하기 위한 확인란입니다. 예를 들어 의료를 승격하면 의료 뉴스 순위가 높아집니다. |
| `when` | 필요에 따라 가장 최근의 일, 주 또는 월로 검색을 제한하기 위한 드롭다운 메뉴입니다. |
| `safe` | Bing 유해 정보 차단 기능을 사용하여 “성인” 결과를 필터링할지 여부를 나타내는 확인란입니다. |
| `count` | 숨겨진 필드입니다. 각 요청에서 반환할 검색 결과 수입니다. 페이지당 더 적거나 더 많은 결과를 표시하도록 변경합니다. |
| `offset`|  숨겨진 필드입니다. 요청의 첫 번째 검색 결과에 대한 오프셋으로, 페이징에 사용됩니다. 새 요청 시 `0`으로 다시 설정됩니다. |

> [!NOTE]
> Bing Web Search에서는 다른 쿼리 매개 변수를 제공합니다. 여기서는 그 중 일부만 사용합니다.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

예를 들어, 실제 API 호출의 `SafeSearch` 매개 변수는 `strict`, `moderate` 또는 `off`일 수 있고, 기본값은 `moderate`입니다. 그러나 이 양식에서는 두 개의 상태만 있는 확인란을 사용합니다. JavaScript 코드는 이 설정을 `strict` 또는 `off`로 변환합니다(`moderate`는 사용되지 않음).

## <a name="performing-the-request"></a>요청 수행
쿼리, 옵션 문자열 및 API 키가 제공될 경우 `BingNewsSearch` 함수는 `XMLHttpRequest` 개체를 사용하여 Bing News Search 엔드포인트에 대해 요청을 수행합니다.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

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
            if (jsobj._type === "News") {
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
> 성공적인 HTTP 요청이 반드시 검색 자체가 성공했음을 의미하는 것은 *아닙니다*. 검색 작업에 오류가 발생하는 경우 Bing News Search API는 200 이외의 HTTP 상태 코드를 반환하고 JSON 응답에 오류 정보를 포함합니다. 또한 요청의 속도가 제한되었으면 API는 빈 응답을 반환합니다.

위의 두 함수에 포함된 코드는 대부분 오류 처리에만 사용됩니다. 다음 단계에서 오류가 발생할 수 있습니다.

|단계|잠재적 오류|처리 방법|
|-|-|-|
|JavaScript 요청 개체 빌드|잘못된 URL|`try`/`catch` 블록|
|요청하기|네트워크 오류, 중단된 연결|`error` 및 `abort` 이벤트 처리기|
|검색 수행|잘못된 요청, 잘못된 JSON, 속도 제한|`load` 이벤트 처리기에서 테스트|

오류는 오류에 대한 알려진 정보로 `renderErrorMessage()`를 호출하여 처리됩니다. 응답이 전체 오류 테스트 단계를 통과하면 `renderSearchResults()`를 호출하여 페이지에 검색 결과를 표시합니다.

## <a name="displaying-search-results"></a>검색 결과 표시
검색 결과를 표시하기 위한 주 함수는 `renderSearchResults()`입니다. 이 함수는 Bing News Search 서비스에서 반환된 JSON을 사용하고, 뉴스 결과 및 관련 검색(있는 경우)을 렌더링합니다.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
주 검색 결과는 JSON 응답에서 최상위 수준 `value` 개체로 반환됩니다. 이 결과를 `renderResults()` 함수에 전달하면 결과를 따라 반복되면서 별도 함수가 호출되고 각 항목이 HTML로 렌더링됩니다. 결과 HTML은 `renderSearchResults()`로 반환되고, 여기서 페이지의 `results` 부분에 삽입됩니다.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Bing News Search API는 최대 4가지 종류의 관련 검색을 각각 고유한 최상위 개체에 반환합니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

|관계|설명|
|-|-|
|`pivotSuggestions`|원래 검색에 포함된 중심 단어를 다른 단어로 바꾸는 쿼리입니다. 예를 들어 “red flowers”를 검색하는 경우 중심 단어는 “red”일 수 있고, 중심 제안은 “yellow flowers”일 수 있습니다.|
|`queryExpansions`|용어를 더 추가하여 원래 검색의 범위를 좁히는 쿼리입니다. 예를 들어 “Microsoft Surface”를 검색하는 경우 쿼리가 “Microsoft Surface Pro”로 확장될 수 있습니다.|
|`relatedSearches`|또한 원래 검색을 입력했던 다른 사용자가 입력한 쿼리입니다. 예를 들어 “Mount Rainier”를 검색하는 경우 관련 검색은 “Mt. Saint Helens”일 수 있습니다.|
|`similarTerms`|원래 검색과 의미가 유사한 쿼리입니다. 예를 들어, “schools”를 검색하는 경우 유사한 용어로 “education”이 있을 수 있습니다.|

이전에 `renderSearchResults()`에서 확인한 것처럼, `relatedItems` 제안만 렌더링하고 결과 링크를 페이지의 사이드바에 배치합니다.

## <a name="rendering-result-items"></a>결과 항목 렌더링

JavaScript 코드에서 개체 `searchItemRenderers`는 각 종류의 검색 결과에 대해 HTML을 생성하는 *renderers:* 함수를 포함합니다.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
렌더러 함수는 다음 매개 변수를 사용할 수 있습니다.

|매개 변수|설명|
|-|-|
|`item`| URL 및 해당 설명과 같은 항목의 속성을 포함하는 JavaScript 개체입니다.|
|`index`| 해당 컬렉션 내에서 결과 항목의 인덱스입니다.|
|`count`| 검색 결과 항목 컬렉션에 있는 항목 수입니다.|

`index` 및 `count` 매개 변수는 결과에 번호를 지정하고, 컬렉션 시작 또는 끝에 대한 특수 HTML을 생성하고, 특정 개수의 항목 다음에 줄 바꿈을 삽입하는 등의 작업에 사용될 수 있습니다. 렌더러에서 이 기능이 필요하지 않은 경우 이러한 두 매개 변수를 수락할 필요가 없습니다.

`news` 렌더러는 다음 javascript 발췌 내용이 나와 있습니다.
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
뉴스 렌더러는 다음과 같이 작동합니다.
> [!div class="checklist"]
> * 단락 태그를 만들고, `news` 클래스에 할당한 후 html 배열에 푸시합니다.
> * 이미지 썸네일 크기를 계산합니다(너비는 60픽셀로 고정되고 높이는 비례해서 계산됨).
> * HTML `<img>` 태그를 빌드하여 이미지 썸네일을 표시합니다. 
> * 이미지와 해당 이미지를 포함하는 페이지에 연결되는 HTML `<a>` 태그를 빌드합니다.
> * 이미지와 해당 이미지가 있는 사이트에 대한 정보를 표시하는 설명을 빌드합니다.

썸네일 크기는 `<img>` 태그와 썸네일의 URL에 있는 `h` 및 `w` 필드 둘 다에 사용됩니다. 그러면 [Bing 썸네일 서비스](resize-and-crop-thumbnails.md)에서 정확히 해당 크기의 썸네일을 제공합니다.

## <a name="persisting-client-id"></a>클라이언트 ID 유지
Bing Search API의 응답에는 후속 요청과 함께 API로 다시 전송되어야 하는 `X-MSEdge-ClientID` 헤더가 포함될 수 있습니다. 여러 개의 Bing Search API를 사용하는 경우 모두 동일한 클라이언트 ID를 사용해야 합니다(가능한 경우).

`X-MSEdge-ClientID` 헤더를 제공하면 Bing API가 모든 사용자 검색을 연결할 수 있습니다. 이렇게 하면 두 가지 중요한 이점이 있습니다.

첫째, Bing 검색 엔진이 이전 컨텍스트를 검색에 적용하여 사용자에게 보다 만족스러운 결과를 찾을 수 있습니다. 예를 들어 사용자가 이전에 항해와 관련된 용어를 검색한 경우 나중에 “knots”를 검색하면 항해에 사용되는 노트에 대한 정보가 우선적으로 반환될 수 있습니다.

둘째, Bing을 통해 임의로 선택된 사용자가 새로운 기능이 폭넓게 사용되기 전에 이러한 새 기능을 경험해볼 수 있습니다. 각 요청에 동일한 클라이언트 ID를 제공하면 기능을 보는 사용자에게 항상 기능이 표시됩니다. 클라이언트 ID가 없으면 사용자의 검색 결과에서 기능이 임의로 나타나거나 사라지는 것처럼 보일 수 있습니다.

브라우저 보안 정책(CORS) 때문에 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용하지 못할 수 있습니다. 이러한 제한은 검색 응답의 원본이 해당 응답을 요청한 페이지와 다른 경우에 발생합니다. 프로덕션 환경에서는 웹 페이지와 동일한 도메인에 대해 API 호출을 수행하는 서버 쪽 스크립트를 호스트하여 이 정책 문제를 해결해야 합니다. 스크립트의 원본은 웹 페이지와 동일하므로 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용할 수 있습니다.

> [!NOTE]
> 프로덕션 웹 애플리케이션의 경우 서버 쪽에서 요청을 수행해야 합니다. 그렇지 않은 경우 Bing Search API 키를 웹 페이지에 포함해야만 원본을 보는 누구나 사용할 수 있게 됩니다. 권한 없는 사람이 수행한 요청을 포함하여 API 구독 키를 통한 모든 사용량에 요금이 청구되므로, 키를 노출하지 않는 것이 중요합니다.

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
> [Bing News Search API 참조](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)
---
title: " 단일 페이지 웹앱 빌드 - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API를 단일 페이지 웹 애플리케이션에 통합하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829602"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Visual Search 단일 페이지 웹앱 만들기

Bing Visual Search API는 이미지에 대한 인사이트를 반환합니다. 이미지를 업로드하거나 이미지에 대한 URL을 제공할 수 있습니다. 인사이트는 시각적으로 비슷한 이미지, 쇼핑 소스, 이미지가 포함된 웹 페이지 등입니다. Bing Visual Search API가 반환하는 인사이트는 Bing.com/이미지에 표시되는 것과 유사합니다.

이 자습서에서는 Bing Image Search API에 대 한 단일 페이지 웹 앱을 확장 하는 방법에 설명 합니다. 해당 자습서를 보거나 여기에 사용되는 소스 코드를 가져오려면 [자습서: Bing Image Search API용 단일 페이지 앱 만들기](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)를 참조하세요.

이 애플리케이션에 대한 전체 소스 코드(Bing Visual Search API를 사용하도록 확장한 후)는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Bing Visual Search API 호출 및 응답 처리

Bing Image Search 자습서를 편집하고 `<script>` 요소의 끝(및 닫는 `</script>` 태그의 앞)에 다음 코드를 추가합니다. 다음 코드는 API의 비주얼 검색 응답을 처리 하 고 결과 반복 표시:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

다음 코드는 검색 요청을 보내 api를 호출 하는 이벤트 수신기를 사용 하 여 `handleVisualSearchResponse()`:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>인사이트 토큰 캡처

다음 코드를 추가 합니다 `searchItemsRenderer` 개체입니다. 이 코드는 클릭할 때 `bingVisualSearch` 함수를 호출하는 **유사 항목 찾기** 링크를 추가합니다. 함수를 수신 합니다 `imageInsightsToken` 인수로 합니다.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>유사한 이미지 표시

다음 HTML 코드를 601줄에 추가합니다. 이 태그 코드를 Bing Visual Search API 호출의 결과 표시 하는 요소를 추가 합니다.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

모든 새 JavaScript 코드 및 HTML 요소가 있으면 검색 결과는 **유사 항목 찾기** 링크로 표시됩니다. 링크를 클릭하면 선택한 것과 비슷한 이미지로 **유사 항목** 섹션을 채웁니다. **유사 항목** 섹션을 확장하여 이미지를 표시해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 에 대 한 Bing Visual Search SDK를 사용 하 여 이미지 잘라내기C#](tutorial-visual-search-crop-area-results.md)

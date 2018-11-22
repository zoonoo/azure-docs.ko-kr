---
title: '자습서: 단일 페이지 웹앱 빌드 - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: 단일 페이지 웹 응용 프로그램에서 Bing Visual Search API를 사용하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: b493f65e47f5e4c932ed1229e4c00ee1af3cd5ab
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161857"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>자습서: Visual Search 단일 페이지 웹앱

Bing Visual Search API에서는 Bing.com/images에 표시된 이미지 세부 정보와 비슷한 환경을 제공합니다. Visual Search를 사용하면 이미지를 지정하고 시각적으로 유사한 이미지, 쇼핑 원본, 해당 이미지를 포함한 웹 페이지 등의 이미지에 대한 인사이트를 얻을 수 있습니다. 

이 자습서에서는 Bing Image Search 자습서에서 단일 페이지 웹앱을 확장합니다([단일 페이지 웹앱](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md) 참조). 이 자습서를 시작하는 전체 소스 코드는 [단일 페이지 웹앱(소스 코드)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md)을 참조하세요. 이 자습서의 최종 소스 코드는 [Visual Search 단일 페이지 웹앱](tutorial-bing-visual-search-single-page-app-source.md)을 참조하세요.

설명하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 이미지 인사이트 토큰으로 Bing Visual Search API 호출
> * 유사한 이미지 표시

## <a name="call-bing-visual-search"></a>Bing Visual Search 호출
Bing Visual Search 자습서를 편집하고 409 줄의 스크립트 요소 끝에 다음 코드를 추가합니다. 이 코드는 Bing Visual Search API를 호출하고 결과를 표시합니다.

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
다음 코드를 151줄의 `searchItemsRenderer` 개체에 추가합니다. 이 코드는 클릭할 때 `bingVisualSearch` 함수를 호출하는 **유사 항목 찾기** 링크를 추가합니다. 함수는 인수로 imageInsightsToken을 받습니다.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>유사한 이미지 표시
다음 HTML 코드를 601줄에 추가합니다. 이 태그 코드는 Bing Visual Search API 호출의 결과를 표시하는 데 사용되는 요소를 추가합니다.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

모든 새 JavaScript 코드 및 HTML 요소가 있으면 검색 결과는 **유사 항목 찾기** 링크로 표시됩니다. 링크를 클릭하면 선택한 것과 비슷한 이미지로 **유사 항목** 섹션을 채웁니다. **유사 항목** 섹션을 확장하여 이미지를 표시해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Visual Search 단일 페이지 웹앱 원본](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual Search API 참조](https://aka.ms/bingvisualsearchreferencedoc)
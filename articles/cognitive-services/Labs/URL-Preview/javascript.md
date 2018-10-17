---
title: '빠른 시작: Project URL Preview, JavaScript'
titlesuffix: Azure Cognitive Services
description: JavaScript를 통해 Bing URL Preview API를 사용하여 빠르게 시작하는 스크립트 샘플입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: b10e33a0947057c35bb6227cc43f92c4f0a56ceb
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869468"
---
# <a name="quickstart-url-preview-in-javascript"></a>빠른 시작: JavaScript에서 URL Preview 사용 

다음 단일 페이지 응용 프로그램은 JavaScript를 사용하여 SwiftKey 사이트에 대한 URL 미리 보기를 만듭니다. https://swiftkey.com/en 

## <a name="prerequisites"></a>필수 조건

[Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview) 평가판에 대한 액세스 키 가져오기

## <a name="code-scenario"></a>코드 시나리오
다음 JavaScript 예제에는 사용자가 미리 보기할 URL을 입력하는 텍스트 상자 입력 개체가 포함되어 있습니다.  사용자가 **미리 보기** 단추를 클릭하면 onclick 메서드가 `getPreview`로 라우팅됩니다. 여기서 코드를 통해 **UrlPreview** 엔드포인트에 대한 웹 요청이 생성됩니다.

코드에서 *XMLHttpRequest*를 만들고 *Ocp-Apim-Subscription-Key* 헤더와 키를 추가한 다음, 요청을 전송합니다.  응답을 처리하기 위한 비동기 이벤트 처리기가 추가됩니다.

응답이 성공적으로 반환되면 처리기가 응답의 JSON 텍스트를 페이지의 `demo` 단락에 할당합니다. 기타 응답 요소는 표시를 위해 다음 단락으로 설정됩니다.

**원시 JSON 응답**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**실행 중인 데모**

![JavaScript URL 미리 보기 예제](./media/java-script-demo.png)

## <a name="running-the-application"></a>응용 프로그램 실행

응용 프로그램을 실행하려면:

1. `YOUR-SUBSCRIPTION-KEY` 값을 구독에 유효한 액세스 키로 바꿉니다.
2. HTML 및 스크립트를 확장명이 .html인 파일에 저장합니다.
3. 브라우저에서 웹 페이지를 실행합니다.
4. 기존 URL을 사용하거나, 텍스트 상자에 다른 URL을 입력합니다.
5. **미리 보기** 단추를 클릭합니다.

**소스 코드:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](csharp.md)
- [Java 빠른 시작](java-quickstart.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)

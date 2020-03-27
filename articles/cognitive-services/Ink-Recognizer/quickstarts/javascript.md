---
title: '빠른 시작: Ink Recognizer REST API와 Node.js로 디지털 잉크 인식'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서 Ink Recognizer API를 사용하여 디지털 잉크 스트로크 인식을 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75448136"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>빠른 시작: Ink Recognizer REST API와 JavaScript로 디지털 잉크 인식

이 빠른 시작을 사용하여 디지털 잉크 스트로크에 Ink Recognizer API를 사용하기 시작합니다. 이 JavaScript 애플리케이션은 JSON 형식의 잉크 스트로크 데이터가 포함된 API 요청을 보내고 응답을 표시합니다.

이 애플리케이션은 Javascript로 작성되어 웹 브라우저에서 실행되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

일반적으로 API는 디지털 잉크 입력 앱에서 호출합니다. 이 빠른 시작은 JSON 파일에서 다음 필기 샘플에 대한 잉크 스트로크 데이터를 보냅니다.

![필기 문자 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 웹 브라우저
- 이 빠른 시작의 잉크 스트로크 데이터 예제는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)에서 찾을 수 있습니다.

### <a name="create-an-ink-recognizer-resource"></a>Ink Recognizer 리소스 만들기

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 원하는 IDE 또는 편집기에서 새 `.html` 파일을 만듭니다. 그런 다음, 나중에 추가할 코드에 기본 HTML을 추가합니다.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. `<body>` 태그 내에 다음 html을 추가합니다.
    1. JSON 요청 및 응답을 표시하기 위한 텍스트 영역 2개
    2. 나중에 만들 `recognizeInk()` 함수를 호출하는 단추
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>예제 JSON 데이터 로드

1. `<script>` 태그 내에서 sampleJson에 대한 변수를 만듭니다. 그런 다음, JSON 파일을 선택할 수 있도록 파일 탐색기를 여는 `openFile()`이라는 JavaScript 함수를 만듭니다. `Recognize ink` 단추를 클릭하면 이 함수가 호출되어 파일 읽기가 시작됩니다.
2. `FileReader` 개체의 `onload()` 함수를 사용하여 파일을 비동기식으로 처리합니다. 
    1. 파일의 `\n` 또는 `\r` 문자를 빈 문자열로 바꿉니다. 
    2. `JSON.parse()`를 사용하여 텍스트를 유효한 JSON으로 변환합니다.
    3. 애플리케이션에서 `request` 텍스트 상자를 업데이트합니다. `JSON.stringify()`를 사용하여 JSON 문자열 형식을 지정합니다. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Ink Recognizer API에 요청 보내기

1. `<script>` 태그 내에서 `recognizeInk()`라는 함수를 만듭니다. 이 함수는 나중에 API를 호출하고 응답으로 페이지를 업데이트합니다. 이 함수 내에서 다음 단계의 코드를 추가합니다. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. 엔드포인트 URL, 구독 키 및 샘플 JSON에 대한 변수를 만듭니다. 그런 다음, API 요청을 보내는 `XMLHttpRequest` 개체를 만듭니다. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. `XMLHttpRequest` 개체에 대한 반환 함수를 만듭니다. 이 함수는 성공한 요청의 API 응답을 구문 분석하여 애플리케이션에 표시합니다. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 요청 개체에 대한 오류 함수를 만듭니다. 이 함수는 콘솔에 오류를 기록합니다. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 요청 개체의 `onreadystatechange` 속성에 대한 함수를 만듭니다. 요청 개체의 준비 상태가 변경되면 위의 반환 및 오류 함수가 적용됩니다.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. API 요청을 보냅니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가하고 `content-type`을 `application/json`으로 설정합니다.
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>애플리케이션 실행 및 응답 보기

이 애플리케이션은 웹 브라우저에서 실행할 수 있습니다. 성공 응답이 JSON 형식으로 반환됩니다. [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)에서 JSON 응답을 찾을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)

Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)

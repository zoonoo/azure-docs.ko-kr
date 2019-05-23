---
title: '빠른 시작: 잉크 인식기 REST API 및 Node.js를 사용 하 여 디지털 잉크를 인식 합니다.'
description: 디지털 잉크 스트로크를 인식 하려면 잉크 인식기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 651474fd538123e760022ac59efbbaf0b9b83d70
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519669"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>빠른 시작: 잉크 인식기 REST API 및 JavaScript를 사용 하 여 디지털 잉크를 인식 합니다.

디지털 잉크 스트로크에 잉크 인식기 API 사용을 시작 하려면이 빠른 시작을 사용 합니다. 이 JavaScript 응용 프로그램에는 JSON 형식 잉크 스트로크 데이터를 포함 하는 API 요청을 보내고 응답을 표시 합니다.

이 응용 프로그램이 Javascript로 작성 된 웹 브라우저에서 실행 하는 동안 API는 대부분의 프로그래밍 언어와 호환 되는 RESTful 웹 서비스.

일반적으로 디지털 잉크 입력 기능 앱에서 API를 호출 합니다. 이 빠른 시작이에서는 JSON 파일에서 다음 필기 샘플에 대 한 잉크 스트로크 데이터를 보냅니다.

![필기 문자의 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 웹 브라우저
- 이 빠른 시작 예제 잉크 스트로크 데이터에서 찾을 수 있습니다 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)합니다.


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 즐겨 찾는 IDE 또는 편집기에서 만드는 새 `.html` 파일입니다. 나중에 추가한 코드를 기본 HTML을 추가 합니다.
    
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

2. 내는 `<body>` 태그를 다음 html을 추가 합니다.
    1. JSON 요청 및 응답을 표시 하기 위한 두 가지 텍스트 영역입니다.
    2. 호출에 대 한 단추는 `recognizeInk()` 나중에 생성 되는 함수입니다.
    
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

## <a name="load-the-example-json-data"></a>예제 JSON 데이터를 로드 합니다.

1. 내는 `<script>` 태그는 sampleJson에 대 한 변수를 만듭니다. 라는 JavaScript 함수를 만든 `openFile()` JSON 파일을 선택할 수 있도록 파일 탐색기가 열립니다. 경우는 `Recognize ink` 단추를 클릭 하면,이 함수를 호출 하 고 파일을 읽기 시작 합니다.
2. 사용 된 `FileReader` 개체의 `onload()` 파일을 비동기적으로 처리 하는 함수입니다. 
    1. 기능을 대체할 `\n` 또는 `\r` 빈 문자열을 사용 하 여 파일의 문자입니다. 
    2. 사용 하 여 `JSON.parse()` 텍스트가 유효한 JSON으로 변환 하려면
    3. 업데이트 된 `request` 응용 프로그램에서 텍스트 상자입니다. 사용 하 여 `JSON.stringify()` JSON 문자열 형식을 지정 합니다. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>잉크 인식기 API 요청 보내기

1. 내 합니다 `<script>` 태그를 호출 하는 함수 만들기 `recognizeInk()`합니다. 나중에이 함수는 API를 호출를 응답과 함께 페이지를 업데이트 합니다. 이 함수 내에서 다음 단계에서 코드를 추가 합니다. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. 끝점 URL, 구독 키 및 JSON 샘플에 대 한 변수를 만듭니다. 만든는 `XMLHttpRequest` 개체 API 요청을 보내야 합니다. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. 반환 함수를 만듭니다.는 `XMLHttpRequest` 개체입니다. 이 함수는 성공적인 요청에서 API 응답을 구문 분석 하 고 응용 프로그램에 표시 됩니다. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 요청 개체에 대 한 오류 함수를 만듭니다. 이 함수는 콘솔에 오류를 기록합니다. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 요청 개체에 대 한 함수를 만드는 `onreadystatechange` 속성입니다. 요청 개체의 준비 상태가 변경 되 면 위의 반환 및 오류 함수를 적용 됩니다.
            
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
    
    5. API 요청을 보냅니다. 구독 키를 추가 합니다 `Ocp-Apim-Subscription-Key` 집합과 헤더는 `content-type` 를 `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)

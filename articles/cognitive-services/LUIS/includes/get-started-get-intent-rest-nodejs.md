---
title: Node.js에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: b158f3738e5d5e33c831e7312c167e5185d19e95
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414459"
---
## <a name="prerequisites"></a>필수 조건

* [Node.js](https://nodejs.org/) 프로그래밍 언어 
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

Node.js를 사용하여 [예측 엔드포인트](https://aka.ms/luis-apim-v3-prediction)를 쿼리하고 예측 결과를 가져옵니다.

1. 다음 코드 조각을 `predict.js` 파일에 복사합니다.

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');
    
    // Analyze text
    //
    getPrediction = async () => {
    
        // YOUR-KEY - Language Understanding starter key
        var endpointKey = "YOUR-KEY";
    
        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is westus2.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";
    
        // Set the LUIS_APP_ID environment variable 
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.    
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
    
        var utterance = "turn on all lights";
    
        // Create query string 
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }
    
        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`
    
        // HTTP Request
        const response = await requestpromise(URI);
    
        // HTTP Response
        console.log(response);
    
    }
    
    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. 다음 값을 바꿉니다.

    * `YOUR-KEY`를 시작 키로 바꿉니다.
    * `YOUR-ENDPOINT`를 엔드포인트 URL로 바꿉니다. 예: `westus2.api.cognitive.microsoft.com`

1. 다음 명령을 사용하여 `request`, `request-promise` 및 `querystring` 종속성을 설치합니다. 

    ```console
    npm install request request-promise querystring
    ```

1. 다음 명령을 사용하여 앱을 실행합니다.

    ```console
    node predict.js
    ```

 1. JSON으로 반환되는 예측 응답을 검토합니다.   
    
    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    가독성을 위한 JSON 응답 형식: 

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료한 후 파일 시스템에서 파일을 삭제합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [발화 및 학습 추가](../get-started-get-model-rest-apis.md)
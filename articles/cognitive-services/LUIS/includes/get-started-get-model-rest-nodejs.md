---
title: C#에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: cc8f01d5608b36521185695cd5f7be531ba7aeaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500713"
---
## <a name="prerequisites"></a>필수 조건

* 시작 키.
* cognitive-services-language-understanding GitHub 리포지토리에서 [TravelAgent 앱](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json)을 가져옵니다.
* 가져온 TravelAgent 앱의 LUIS 애플리케이션 ID입니다. 애플리케이션 ID는 애플리케이션 대시보드에 표시됩니다.
* 발화를 수신하는 애플리케이션 내의 버전 ID입니다. 기본 ID는 "0.1"입니다.
* [Node.js](https://nodejs.org/) 프로그래밍 언어 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

Go를 사용하여 기계 학습된 엔터티 [API](https://aka.ms/luis-apim-v3-authoring)를 애플리케이션에 추가합니다. 

1. 이름이 `model.js`인 새 파일을 만듭니다. 다음 코드를 추가합니다.

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const reponse = await requestpromise(options)
        console.log(reponse.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const reponse = await requestpromise(options)
        console.log(reponse.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. 다음 값을 바꿉니다.

    * `YOUR-KEY`를 시작 키로
    * `YOUR-ENDPOINT`를 엔드포인트로(예: `westus2.api.cognitive.microsoft.com`)
    * `YOUR-APP-ID`를 앱 ID로

1. 파일을 만든 위치와 같은 디렉터리에서 명령 프롬프트에 다음 명령을 입력하여 파일을 실행합니다.

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료한 후 파일 시스템에서 파일을 삭제합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)
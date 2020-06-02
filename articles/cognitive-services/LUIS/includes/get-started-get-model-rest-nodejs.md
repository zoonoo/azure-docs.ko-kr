---
title: C#에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 19f72dbb62fc2084bf0c9609fb3782e083c911af
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655515"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure Language Understanding - 작성 리소스 32자 키 및 작성 엔드포인트 URL입니다. [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 또는 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)를 사용하여 만듭니다.
* `Azure-Samples/cognitive-services-sample-data-files` GitHub 리포지토리에서 [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) 앱을 가져옵니다.
* 가져온 Pizza 앱의 LUIS 애플리케이션 ID입니다. 애플리케이션 ID는 애플리케이션 대시보드에 표시됩니다.
* 발언을 수신하는 애플리케이션 내의 버전 ID입니다.
* [Node.js](https://nodejs.org/) 프로그래밍 언어
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>Node.js 프로젝트 만들기

1. `node-model-with-rest`와 같은 Node.js 프로젝트를 보관할 새 폴더를 만듭니다.

1. 새 명령 프롬프트를 열어 만든 폴더로 이동하고 다음 명령을 실행합니다.

    ```console
    npm init
    ```

    각 프롬프트에서 Enter를 눌러 기본 설정을 적용합니다.

1. 다음 명령을 입력하여 요청 약속 모듈을 설치합니다.

    ```console
    npm install --save request-promise
    ```

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

1. 이름이 `model.js`인 새 파일을 만듭니다. 다음 코드를 추가합니다.

    ```javascript
    var request = require('request-promise');

    //////////
    // Values to modify.

    // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
    const LUIS_appId = "YOUR-APP-ID";

    // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
    const LUIS_authoringKey = "YOUR-AUTHORING-KEY";

    // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
    // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
    const LUIS_endpoint = "YOUR-AUTHORING-ENDPOINT";

    // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
    const LUIS_versionId = "0.1";
    //////////

    const addUtterancesURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
        {
            'text': 'order a pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'order a large pepperoni pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 14,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 8,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'I want two large pepperoni pizzas on thin crust',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 17,
                    'endCharIndex': 32
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 11,
                    'endCharIndex': 15
                },
                {
                    'entityName': 'Quantity',
                    'startCharIndex': 7,
                    'endCharIndex': 9
                },
                {
                    'entityName': 'Crust',
                    'startCharIndex': 37,
                    'endCharIndex': 46
                }
            ]
        }
    ];

    // Main function.
    const main = async() =>{

        await addUtterances(utterances);
        await train("POST");
        await train("GET");

    }

    // Adds the utterances to the model.
    const addUtterances = async (utterances) => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await request(options)
        console.log("addUtterance:\n" + JSON.stringify(response, null, 2));
    }

    // With verb === "POST", sends a training request.
    // With verb === "GET", obtains the training status.
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

        const response = await request(options)
        console.log("train " + verb + ":\n" + JSON.stringify(response, null, 2));
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err));
    ```

1. `YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-APP-ID`| LUIS 앱 ID입니다. |
    |`YOUR-AUTHORING-KEY`|32자 작성 키입니다.|
    |`YOUR-AUTHORING-ENDPOINT`| 작성 URL 엔드포인트입니다. `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`)을 입력합니다. 리소스를 만들 때 리소스 이름을 설정합니다.|

    할당된 키와 리소스는 **Azure Resources** 페이지의 관리 섹션에 있는 LUIS 포털에 표시됩니다. 앱 ID는 **애플리케이션 설정** 페이지의 동일한 관리 섹션에서 사용할 수 있습니다.

1. 명령 프롬프트에서 다음 명령을 입력하여 프로젝트를 실행합니다.

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)
---
title: Node.js를 사용하여 LUIS 앱에 발언을 추가하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 Node.js를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264229"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>자습서: Node.js를 사용하여 발언 앱 추가 
이 자습서에서는 Node.js로 API 작성을 사용하여 의도에 발언을 추가하는 프로그램을 작성합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio 콘솔 프로젝트 만들기 
> * 발언을 추가하고 앱을 학습하기 위해 LUIS API를 호출하는 메서드 추가
> * BookFlight 의도에 대해 예제 발언을 사용하여 JSON 파일 추가
> * 콘솔을 실행하고 발언에 대한 학습 상태 확인

자세한 정보는 [의도에 예제 발언 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [학습](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) 및 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API의 기술 설명서를 참조하세요.

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="prerequisites"></a>필수 조건

* NPM를 사용하는 최신 [**Node.js**](https://nodejs.org/en/download/)
* 이 아티클에 대한 NPM 종속성은 [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra)입니다.  
* **[권장]** IntelliSense 및 디버깅용 [Visual Studio Code](https://code.visualstudio.com/)
* LUIS  **[작성 키](luis-concept-keys.md#authoring-key)** [LUIS](luis-reference-regions.md) 웹 사이트의 계정 설정에서 이 키를 찾을 수 있습니다.
* 기존 LUIS [**응용 프로그램 ID**](./luis-get-started-create-app.md) 응용 프로그램 ID는 응용 프로그램 대시보드에 표시됩니다. `utterances.json` 파일에서 사용된 의도 및 엔터티를 포함한 LUIS 응용 프로그램은 `add-utterances.js`에서 코드를 실행하기 전에 설치되어야 합니다. 이 아티클의 코드는 의도 및 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발언만을 추가합니다. 
* 발언을 수신하는 응용 프로그램 내의 **버전 ID**입니다. 기본 ID는 "0.1"입니다.
* VSCode에서 `add-utterances.js` 프로젝트라는 새 파일을 만듭니다.

> [!NOTE] 
> 전체 `add-utterances.js` 파일은 [**LUIS 샘플** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs)에서 지원됩니다.


## <a name="write-the-nodejs-code"></a>Node.js 코드 작성

파일에 NPM 종속성을 추가합니다.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

파일에 LUIS 상수를 추가합니다. 다음 코드를 복사하고 작성 키, 응용 프로그램 ID 및 버전 ID로 변경합니다.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

사용자 발언을 포함하는 업로드 파일의 이름 및 위치를 추가합니다. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

명령줄 값을 보유하는 변수를 추가합니다.

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


`sendUtteranceToApi` 함수를 추가하여 HTTP 호출을 주고 받습니다. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

`addUtterance` 함수에서 사용하는 구성 JSON 개체를 추가합니다.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

`addUtterance` 함수를 추가하여 `SendUtteranceToApp`에서 사용하는 API 요청 및 응답을 관리합니다.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

`train` 함수에서 사용하는 구성 JSON 개체를 추가합니다.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

`train` 함수를 추가하여 학습 프로세스를 시작합니다. 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

명령줄 변수를 기반으로 수행할 작업(발언 또는 학습 추가)을 선택하는 코드를 추가합니다.

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>추가할 발언 지정
`utterances.json` 파일을 만들고 편집하여 LUIS 앱에 추가하려는 **발언의 배열**을 지정합니다. 의도 및 엔터티는 이미 LUIS 앱에 포함되어 **있어야** 합니다.

> [!NOTE]
> `utterances.json` 파일에서 사용된 의도 및 엔터티를 포함한 LUIS 응용 프로그램은 `add-utterances.js`에서 코드를 실행하기 전에 설치되어야 합니다. 이 아티클의 코드는 의도 및 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발언만을 추가합니다.

`text` 필드에는 발언의 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱에 있는 의도의 이름에 해당해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티의 레이블을 지정하지 않으려면 다음 예제에서 표시된 대로 빈 목록을 제공합니다.

entityLabels 목록이 비어 있는 경우는 `startCharIndex` 및 `endCharIndex`는 `entityName` 필드에서 참조되는 엔터티를 표시해야 합니다. 두 인덱스는 모두 0부터 시작하는 계수입니다. 즉 위의 예제에서 6은 시애틀(Seattle)의 "S"를 의미하고 대문자 S 앞에 있는 공백이 아닙니다.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>명령줄의 발언 추가

Node.js를 사용하여 명령줄의 응용 프로그램을 실행합니다.

인수 없이 add-utterance를 호출하면 발언을 학습하지 않고 앱에 추가합니다.
````
> node add-utterances.js
````

이 샘플은 파일을 만듭니다는 `results.json` add utterances API 호출 결과가 들어 있는입니다. `response` 필드는 추가된 발언에서 이 형식으로 표시됩니다. `hasError`는 false이며 발언이 추가되었음을 나타냅니다.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>발언 추가 및 명령줄에서 학습
`-train` 인수를 사용하여 add-utterance를 호출하면 학습할 요청을 보내고 이후에 학습 상태를 요청합니다. 상태는 학습이 시작된 후에 즉시 큐에 대기합니다. 상태 세부 정보는 파일에 작성됩니다.

````
> node add-utterances.js -train
````

> [!NOTE]
> 중복 발언은 다시 추가할 수 없지만 오류가 발생하지 않습니다. `response`에는 원래 발언의 ID가 포함됩니다.

`-train` 인수를 사용하여 샘플을 호출하는 경우 LUIS 앱을 학습하는 요청이 성공적으로 큐에 대기함을 나타내는 `training-results.json` 파일을 만듭니다. 

학습 요청이 성공한 결과는 다음과 같습니다.
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

학습에 대한 요청이 큐에 대기된 후에 학습을 완료하려면 시간이 걸릴 수 있습니다.

## <a name="get-training-status-from-the-command-line"></a>명령줄의 학습 상태 가져오기
`-status` 인수를 사용하여 샘플을 호출하면 학습 상태를 확인하고 상태 세부 정보를 파일에 작성합니다.

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>리소스 정리
이 자습서를 완료하고 Visual Studio 및 콘솔 응용 프로그램이 더 이상 필요없는 경우 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

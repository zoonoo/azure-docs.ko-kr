---
title: Node.js에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd25ef6ff44f1d62d553db518f7389da0e5d5019
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91534634"
---
[참조 설명서](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-model-with-rest/model.js)

## <a name="prerequisites"></a>필수 구성 요소

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
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

1. 이름이 `model.js`인 새 파일을 만듭니다. 다음 코드를 추가합니다.

    [!code-javascript[Code snippet](~/cognitive-services-quickstart-code/javascript/LUIS/node-model-with-rest/model.js)]

1. `YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-APP-ID`| LUIS 앱 ID입니다. |
    |`YOUR-AUTHORING-KEY`|32자 작성 키입니다.|
    |`YOUR-AUTHORING-ENDPOINT`| 작성 URL 엔드포인트입니다. 예들 들어 `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`입니다. 리소스를 만들 때 리소스 이름을 설정합니다.|

    할당된 키와 리소스는 **Azure Resources** 페이지의 관리 섹션에 있는 LUIS 포털에 표시됩니다. 앱 ID는 **애플리케이션 설정** 페이지의 동일한 관리 섹션에서 사용할 수 있습니다.

1. 명령 프롬프트에서 다음 명령을 입력하여 프로젝트를 실행합니다.

    ```console
    node model.js
    ```

1. 제작 응답을 검토합니다.

    ```json
    addUtterance:
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    train POST:
    {
      "statusId": 9,
      "status": "Queued"
    }
    train GET:
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    done
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)
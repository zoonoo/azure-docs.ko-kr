---
title: '빠른 시작: Go를 사용하여 모델을 변경하고 LUIS 앱 학습시키기 - Azure Cognitive Services | Microsoft Docs'
description: Go 빠른 시작에서는 Home Automation 앱에 예제 발언을 추가하여 앱을 학습시킵니다. 예제 발언은 의도에 매핑된 대화형 사용자 텍스트입니다. 의도에 대한 예제 발언을 제공하여, 사용자가 제공한 텍스트의 종류가 어떤 의도에 속하는지 LUIS에 알려줍니다.
titleSuffix: Microsoft Cognitive Services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 6fa95a31e3a5f81adc3092ba7f272cd282f45e48
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43750580"
---
# <a name="quickstart-change-model-using-go"></a>빠른 시작: Go를 사용하여 모델 변경

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Go](https://golang.org/) 프로그래밍 언어가 설치되어 있습니다.
* [VSCode](https://code.visualstudio.com) 

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기 

1. VSCode로 `add-utterances.go`를 만듭니다. 

2. 종속성을 추가합니다. 

   [!code-go[Add dependencies.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=2-10 "Add dependencies.")]

3. 일반 HTTP 요청 함수를 추가합니다. 여기에는 헤더에 작성 키 전달이 포함됩니다. 

   [!code-go[Add HTTP request function which includes passing authoring key in header. ](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=12-36 "Add HTTP request function, which includes passing authoring key in header. ")]

4. JSON 파일에서 예제 발언을 추가합니다.

   [!code-go[Add example utterances from JSON file.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=62-76 "Add example utterances from JSON file.")]

5. 교육을 요청합니다. 도우미 함수를 사용하여 교육 상태와 동일한 경로에 대한 동사를 설정합니다. 

   [!code-go[Request training. ](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=77-86 "Request training. ")]

6. 교육 상태를 요청합니다. 도우미 함수를 사용하여 교육 요청과 동일한 경로에 대한 동사를 설정합니다. 

   [!code-go[Request training status. ](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=87-90 "Request training status. ")]

7. 명령줄 구문 분석을 처리하는 main 함수를 추가합니다.

   [!code-go[Add main function to handle command line parsing. ](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=38-60 "Add main function to handle command-line parsing.")]

## <a name="add-an-utterance-from-the-command-line-train-and-get-status"></a>명령줄에서 발언 추가, 학습 및 상태 가져오기

1. Go 파일을 만든 디렉터리에서 명령 프롬프트에 `go build add-utterances.go`를 입력하여 Go 파일을 컴파일합니다. 명령 프롬프트에는 빌드 성공에 대한 정보가 반환되지 않습니다.

2. 명령 프롬프트에서 다음 텍스트를 입력하여 명령줄에서 Go 응용 프로그램을 실행합니다. 

    ```CMD
    add-utterances -appID <your-app-id> -authoringKey <add-your-authoring-key> -version <your-version-id> -region westus -utteranceFile utterances.json

    ```

    `<add-your-authoring-key>`를 작성 키(시작 키라고도 함)의 값으로 대체합니다. `<your-app-id>`를 앱 ID의 값으로 대체합니다. `<your-version-id>`를 버전의 값으로 대체합니다. 기본 버전은 `0.1`입니다.

    아래 명령 프롬프트에 결과가 표시됩니다.

    ```CMD
    add example utterances requested
    [
        {
            "text": "go lang 1",
            "intentName": "None",
            "entityLabels": []
        }
    ,
        {
            "text": "go lang 2",
            "intentName": "None",
            "entityLabels": []
        }
    ]
    201
    [
        {
            "value": {
                "ExampleId": 77783998,
                "UtteranceText": "go lang 1"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 77783999,
                "UtteranceText": "go lang 2"
            },
            "hasError": false
        }
    ]
    training selected
    202
    {"statusId":9,"status":"Queued"}
    training status selected
    200
    [
        {
            "modelId": "c52d6509-9261-459e-90bc-b3c872ee4a4b",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "5119cbe8-97a1-4c1f-85e6-6449f3a38d77",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "01e6b6bc-9872-47f9-8a52-da510cddfafe",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "33b409b2-32b0-4b0c-9e91-31c6cfaf93fb",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "1fb210be-2a19-496d-bb72-e0c2dd35cbc1",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "3d098beb-a1aa-423f-a0ae-ce08ced216d6",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "cce854f8-8f8f-4ed9-a7df-44dfea562f62",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "4d97bf0d-5213-4502-9712-2d6e77c96045",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        }
    ]
    ```

    이 응답에는 세 가지 HTTP 호출 각각에 대한 HTTP 상태 코드는 물론 응답 본문에 반환된 JSON 응답이 포함됩니다. 

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [사용자 지정 도메인으로 앱 빌드](luis-quickstart-intents-only.md) 
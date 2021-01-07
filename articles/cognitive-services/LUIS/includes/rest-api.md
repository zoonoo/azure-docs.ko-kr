---
title: REST API 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/30/2020
ms.topic: include
ms.openlocfilehash: ac5d4ce3080bad36b0f68289bee32910bdc58837
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941523"
---
이 빠른 시작에서는 세 개의 REST 호출을 순서대로 수행합니다.

- 첫 번째로, [REST 레이블 추가 일괄 처리](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) 호출을 사용하여 피자 앱 모델을 학습시키는 데 사용되는 예제 발화 일괄 처리를 업로드합니다.
- 다음으로, [REST 애플리케이션 버전 학습](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) 호출을 사용하여 피자 앱에 대한 학습 세션을 시작합니다.
- 마지막으로, [REST 버전 학습 상태 가져오기](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) 호출을 사용하여 피자 앱의 학습 세션 상태를 가져옵니다.

[참조 설명서](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>필수 구성 요소

* 체험 [LUIS](https://www.luis.ai) 계정.
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 텍스트 편집기.
* 명령줄 프로그램 cURL. cURL 프로그램은 macOS, 대부분의 Linux 배포판 및 Windows 10 빌드 1803 이상에 이미 설치되어 있습니다.

    cURL을 설치해야 하는 경우 [cURL 다운로드 페이지](https://curl.haxx.se/download.html)에서 cURL을 다운로드할 수 있습니다.

## <a name="create-pizza-app"></a>Pizza 앱 만들기

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]


## <a name="create-a-json-file-to-train-the-pizza-app"></a>피자 앱을 학습시키는 JSON 파일 만들기

세 가지 예제 발화가 포함된 JSON 파일을 만들려면 다음 JSON 데이터를 `ExampleUtterances.JSON`이라는 파일에 저장합니다.

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

예제 발화 JSON은 특정 형식을 따릅니다.

`text` 필드에는 예시 발언 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱의 기존 의도 이름과 일치해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티에 레이블을 지정하지 않으려면 빈 배열을 제공하세요.

entityLabels 배열이 비어 있지 않으면 `startCharIndex` 및 `endCharIndex`에서 `entityName` 필드에 언급된 엔터티를 표시해야 합니다. 인덱스는 0부터 시작합니다. 텍스트의 공간에서 레이블을 시작하거나 종료하는 경우 발화를 추가하기 위한 API 호출이 실패합니다.

## <a name="add-example-utterances"></a>예제 발화 추가

1. 예제 발화 일괄 처리를 업로드하려면 다음 명령을 텍스트 편집기에 복사합니다.

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT**_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    _*_

1. `_*_YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`_*_YOUR-AUTHORING-ENDPOINT_*_`| 작성 URL 엔드포인트입니다. 예: "https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/". 리소스를 만들 때 리소스 이름을 설정합니다.|
    |`_*_YOUR-APP-ID_*_`| LUIS 앱 ID입니다. |
    |`_*_YOUR-APP-VERSION_*_`| LUIS 앱 버전입니다. 피자 앱의 경우 버전 번호는 따옴표 없이 "0.1"입니다.|
    |`_*_YOUR-AUTHORING-KEY_*_`|32자 작성 키입니다.|

    할당된 키와 리소스는 _ *Azure 리소스** 페이지의 관리 섹션에 있는 LUIS 포털에 표시됩니다. 앱 ID는 **애플리케이션 설정** 페이지의 동일한 관리 섹션에서 사용할 수 있습니다.

1. 명령 프롬프트(Windows) 또는 터미널(macOS 및 Linux)을 시작하고, 디렉터리를 `ExampleUtterances.JSON` 파일을 저장한 디렉터리로 변경합니다.

1. 편집기에서 cURL 명령을 복사하여 명령 프롬프트(Windows) 또는 터미널(macOS 및 Linux)에 붙여넣습니다. Enter 키를 눌러 명령을 실행합니다.

    다음과 같은 응답이 표시됩니다.

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    가독성을 위해 형식이 지정된 출력은 다음과 같습니다.

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>피자 앱 모델 학습

1. 피자 앱의 학습 세션을 시작하려면 다음 명령을 텍스트 편집기에 복사합니다.

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "**_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. 앞에서 했던 것처럼, `_*_YOUR-`로 시작하는 값을 각자 해당하는 값으로 바꿉니다.

1. 편집기에서 cURL 명령을 복사하여 명령 프롬프트(Windows) 또는 터미널(macOS 및 Linux)에 붙여넣습니다. Enter 키를 눌러 명령을 실행합니다.

    다음과 같은 응답이 표시됩니다.

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    가독성을 위해 형식이 지정된 출력은 다음과 같습니다.

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>학습 상태 가져오기

1. 학습 세션의 학습 상태를 가져오려면 다음 명령을 텍스트 편집기에 복사합니다.

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. 앞에서 했던 것처럼, `_*_YOUR-`로 시작하는 값을 각자 해당하는 값으로 바꿉니다.

1. 편집기에서 cURL 명령을 복사하여 명령 프롬프트(Windows) 또는 터미널(macOS 및 Linux)에 붙여넣습니다. Enter 키를 눌러 명령을 실행합니다.

    다음과 같은 응답이 표시됩니다.

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    가독성을 위해 형식이 지정된 출력은 다음과 같습니다.

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```


## <a name="get-intent-from-the-prediction-endpoint"></a>예측 엔드포인트에서 의도 가져오기

cURL을 사용하여 [예측 엔드포인트](https://aka.ms/luis-apim-v3-prediction)를 쿼리하고 예측 결과를 가져옵니다.

> [!NOTE]
> 이 명령은 예측 엔드포인트를 사용합니다.

1. 이 명령을 텍스트 편집기에 복사합니다.

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    _*_

1. `_*_YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`_*_YOUR-PREDICTION-ENDPOINT_*_`| 예측 URL 엔드포인트입니다. LUIS 포털, 앱의 Azure Resources 설정 페이지에 있습니다.<br>예들 들어 `https://westus.api.cognitive.microsoft.com/`입니다.|
    |`_*_YOUR-APP-ID_*_`|앱 ID. LUIS 포털, 앱의 애플리케이션 설정 페이지에 있습니다.
    |`_*_YOUR-PREDICTION-KEY_**`|32자 예측 키입니다. LUIS 포털, 앱의 Azure Resources 설정 페이지에 있습니다.

1. 텍스트를 콘솔 창에 복사하고 Enter를 눌러 명령을 실행합니다.

1. JSON으로 반환되는 예측 응답을 검토합니다.

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    가독성을 위한 JSON 응답 형식:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
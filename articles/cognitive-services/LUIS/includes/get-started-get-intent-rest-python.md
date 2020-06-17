---
title: Python에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/03/2020
ms.author: diberry
ms.openlocfilehash: f5a4c49e9cad9f4e16124d2fad67c6302239c6b7
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418056"
---
[참조 설명서](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-predict-with-rest/predict.py)

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.6](https://www.python.org/downloads/) 이상
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-pizza-app"></a>Pizza 앱 만들기

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>예측 엔드포인트에서 의도 가져오기

Python을 사용하여 [예측 엔드포인트](https://aka.ms/luis-apim-v3-prediction)를 쿼리하고 예측 결과를 가져옵니다.

1. 이 코드 조각을 `predict.py`라는 파일에 복사합니다.

    [!code-python[Code snippet](~/cognitive-services-quickstart-code/python/LUIS/python-predict-with-rest/predict.py)]

1. `YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-APP-ID`|앱 ID. LUIS 포털, 앱의 애플리케이션 설정 페이지에 있습니다.
    |`YOUR-PREDICTION-KEY`|32자 예측 키입니다. LUIS 포털, 앱의 Azure Resources 설정 페이지에 있습니다.
    |`YOUR-PREDICTION-ENDPOINT`| 예측 URL 엔드포인트입니다. LUIS 포털, 앱의 Azure Resources 설정 페이지에 있습니다.<br>예들 들어 `https://westus.api.cognitive.microsoft.com/`입니다.|

1. `requests` 종속성을 설치합니다. `requests`라이브러리는 HTTP 요청을 수행하는 데 사용됩니다.

    ```console
    pip install requests
    ```

1. 다음 콘솔 명령을 사용하여 스크립트를 실행합니다.

    ```console
    python predict.py
    ```

1. JSON으로 반환되는 예측 응답을 검토합니다.

    ```console
    {'query': 'I want two large pepperoni pizzas on thin crust please', 'prediction': {'topIntent': 'ModifyOrder', 'intents': {'ModifyOrder': {'score': 1.0}, 'None': {'score': 8.55e-09}, 'Greetings': {'score': 1.82222226e-09}, 'CancelOrder': {'score': 1.47272727e-09}, 'Confirmation': {'score': 9.8125e-10}}, 'entities': {'Order': [{'FullPizzaWithModifiers': [{'PizzaType': ['pepperoni pizzas'], 'Size': [['Large']], 'Quantity': [2], 'Crust': [['Thin']], '$instance': {'PizzaType': [{'type': 'PizzaType', 'text': 'pepperoni pizzas', 'startIndex': 17, 'length': 16, 'score': 0.9978157, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Size': [{'type': 'SizeList', 'text': 'large', 'startIndex': 11, 'length': 5, 'score': 0.9984481, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Quantity': [{'type': 'builtin.number', 'text': 'two', 'startIndex': 7, 'length': 3, 'score': 0.999770939, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Crust': [{'type': 'CrustList', 'text': 'thin crust', 'startIndex': 37, 'length': 10, 'score': 0.933985531, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], '$instance': {'FullPizzaWithModifiers': [{'type': 'FullPizzaWithModifiers', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.90681237, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], 'ToppingList': [['Pepperoni']], '$instance': {'Order': [{'type': 'Order', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.9047088, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'ToppingList': [{'type': 'ToppingList', 'text': 'pepperoni', 'startIndex': 17, 'length': 9, 'modelTypeId': 5, 'modelType': 'List Entity Extractor', 'recognitionSources': ['model']}]}}}}
    ```

    가독성을 위한 JSON 응답 형식:

    ```JSON
    {
      'query': 'I want two large pepperoni pizzas on thin crust please',
      'prediction': {
        'topIntent': 'ModifyOrder',
        'intents': {
          'ModifyOrder': {
            'score': 1.0
          },
          'None': {
            'score': 8.55e-9
          },
          'Greetings': {
            'score': 1.82222226e-9
          },
          'CancelOrder': {
            'score': 1.47272727e-9
          },
          'Confirmation': {
            'score': 9.8125e-10
          }
        },
        'entities': {
          'Order': [
            {
              'FullPizzaWithModifiers': [
                {
                  'PizzaType': [
                    'pepperoni pizzas'
                  ],
                  'Size': [
                    [
                      'Large'
                    ]
                  ],
                  'Quantity': [
                    2
                  ],
                  'Crust': [
                    [
                      'Thin'
                    ]
                  ],
                  '$instance': {
                    'PizzaType': [
                      {
                        'type': 'PizzaType',
                        'text': 'pepperoni pizzas',
                        'startIndex': 17,
                        'length': 16,
                        'score': 0.9978157,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Size': [
                      {
                        'type': 'SizeList',
                        'text': 'large',
                        'startIndex': 11,
                        'length': 5,
                        'score': 0.9984481,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Quantity': [
                      {
                        'type': 'builtin.number',
                        'text': 'two',
                        'startIndex': 7,
                        'length': 3,
                        'score': 0.999770939,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Crust': [
                      {
                        'type': 'CrustList',
                        'text': 'thin crust',
                        'startIndex': 37,
                        'length': 10,
                        'score': 0.933985531,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ]
                  }
                }
              ],
              '$instance': {
                'FullPizzaWithModifiers': [
                  {
                    'type': 'FullPizzaWithModifiers',
                    'text': 'two large pepperoni pizzas on thin crust',
                    'startIndex': 7,
                    'length': 40,
                    'score': 0.90681237,
                    'modelTypeId': 1,
                    'modelType': 'Entity Extractor',
                    'recognitionSources': [
                      'model'
                    ]
                  }
                ]
              }
            }
          ],
          'ToppingList': [
            [
              'Pepperoni'
            ]
          ],
          '$instance': {
            'Order': [
              {
                'type': 'Order',
                'text': 'two large pepperoni pizzas on thin crust',
                'startIndex': 7,
                'length': 40,
                'score': 0.9047088,
                'modelTypeId': 1,
                'modelType': 'Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ],
            'ToppingList': [
              {
                'type': 'ToppingList',
                'text': 'pepperoni',
                'startIndex': 17,
                'length': 9,
                'modelTypeId': 5,
                'modelType': 'List Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ]
          }
        }
      }
    }
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료한 후 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [발화 및 학습 추가](../get-started-get-model-rest-apis.md)
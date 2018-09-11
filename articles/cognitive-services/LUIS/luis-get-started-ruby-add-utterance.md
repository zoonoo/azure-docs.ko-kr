---
title: '빠른 시작: Ruby를 사용하여 모델을 변경하고 LUIS 앱 학습시키기 - Azure Cognitive Services | Microsoft Docs'
description: Ruby 빠른 시작에서는 Home Automation 앱에 예제 발언을 추가하여 앱을 학습시킵니다. 예제 발언은 의도에 매핑된 대화형 사용자 텍스트입니다. 의도에 대한 예제 발언을 제공하여, 사용자가 제공한 텍스트의 종류가 어떤 의도에 속하는지 LUIS에 알려줍니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 537ebe2d008e313d2fb29d05143804c3478567e8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159455"
---
# <a name="quickstart-change-model-using-ruby"></a>빠른 시작: Ruby를 사용하여 모델 변경

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Ruby](http://rubyinstaller.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기 

`add-utterances.rb` 파일에 종속성을 추가합니다.

   [!code-ruby[Ruby and LUIS Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=1-21 "Ruby and LUIS Dependencies")]

학습 상태에 사용되는 GET 요청을 추가합니다.

   [!code-ruby[SendGet](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=23-33 "SendGet")]

발화를 만들거나 학습을 시작하는 데 사용되는 POST 요청을 추가합니다. 

   [!code-ruby[SendPost](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=35-47 "SendPost")]

`AddUtterances` 함수를 추가합니다.

   [!code-ruby[AddUtterances method](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=49-54 "AddUtterances method")]


`Train` 함수를 추가합니다. 

   [!code-ruby[Train](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=56-62 "Train")]

`Status` 함수를 추가합니다.

   [!code-ruby[Status](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=64-68 "Status")]

인수를 관리하려면 기본 코드를 추가합니다.

   [!code-ruby[Main code](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=70-72 "Main code")]

## <a name="run-code"></a>코드 실행

Ruby를 사용하여 명령줄에서 응용 프로그램을 실행합니다.

### <a name="add-an-utterance-from-the-command-line"></a>명령줄에서 발화 추가

`add-utterances.rb`를 호출하면 발언이 추가되고, 학습이 진행되고, 학습 상태를 가져옵니다.

```CMD
> ruby add-utterances.rb 
```

이 결과에는 발화 추가 API를 호출한 결과가 표시됩니다. `response` 필드는 추가된 발화에 대한 형식입니다. `hasError`는 false이며, 이는 발화가 추가되었음을 나타냅니다.  

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

다음 응답에는 대기 중인 학습이 표시됩니다. 그리고 다음 응답에는 각 의도의 상태가 표시됩니다. 

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md)
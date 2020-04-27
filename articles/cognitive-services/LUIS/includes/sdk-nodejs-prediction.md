---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732060"
---
Node.js용 LUIS(Language Understanding) 런타임 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 슬롯별 예측
* 버전별 예측

[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [런타임 패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>사전 요구 사항

* Language Understanding 런타임 리소스: [Azure Portal에서 하나 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.JS](https://nodejs.org)
* LUIS 앱 ID - `df67dcdb-c37d-46af-88e1-8b97951ca1c2`의 공용 IoT 앱 ID를 사용합니다. 빠른 시작 코드에 사용되는 사용자 쿼리는 해당 앱에만 적용됩니다.

## <a name="setting-up"></a>설치

### <a name="get-your-language-understanding-luis-runtime-key"></a>LUIS(Language Understanding) 런타임 키 가져오기

LUIS 런타임 리소스를 만들어 [런타임 키](../luis-how-to-azure-subscription.md)를 가져옵니다. 다음 단계를 위해 키와 키의 엔드포인트를 유지합니다.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>새 JavaScript(Node.js) 파일 만들기

이름이 `luis_prediction.js`인 기본 설정된 편집기 또는 IDE에서 새 JavaScript 파일을 만듭니다.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>LUIS 런타임용 NPM 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 종속성을 설치합니다.

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>개체 모델

LUIS(Language Understanding) 작성 클라이언트는 Azure를 인증하는 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 개체이며, 이 개체에는 작성 키가 포함되어 있습니다.

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* `staging` 또는 `production` 슬롯별 [예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [버전별 예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 LUIS(Language Understanding) 예측 런타임 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [슬롯별 예측](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>종속성 추가

프로젝트 디렉터리의 원하는 편집기나 IDE에서 `luis_prediction.js` 파일을 엽니다. 다음 종속성을 추가합니다.

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>클라이언트 인증

1. 사용자 고유의 필수 LUIS 정보에 대한 변수를 만듭니다.

    `LUIS_RUNTIME_KEY`라는 환경 변수에서 끌어온 예측 키를 관리하는 변수를 추가합니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다. 메서드는 나중에 생성됩니다.

    리소스 이름 `LUIS_RUNTIME_ENDPOINT`을 보유할 변수를 만듭니다.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. `LUIS_APP_ID`라는 환경 변수로 앱 ID에 대한 변수를 만듭니다. 환경 변수를 공용 IoT 앱 **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** 로 설정합니다. `production`의 게시된 슬롯을 설정하는 변수를 만듭니다.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. 키를 사용하여 msRest.ApiKeyCredentials 개체를 만들고 엔드포인트에서 이를 사용하여 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 개체를 만듭니다.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>런타임에서 예측 가져하기

다음 메서드를 추가하여 예측 런타임에 대한 요청을 만듭니다.

사용자 발화는 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 개체의 일부입니다.

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 메서드는 요청을 처리할 여러 매개 변수(예: 앱 ID, 슬롯 이름 및 예측 요청 개체)가 필요합니다. verbose, show all intents, log와 같은 다른 옵션은 선택 사항입니다.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>예측의 Main 코드

다음과 같은 Main 메서드를 사용하여 변수와 메서드를 연결하여 예측을 얻습니다.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `node luis_prediction.js` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node luis_prediction.js
```

예측 결과는 JSON 개체를 반환합니다.

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>리소스 정리

예측을 완료했으면 파일과 해당 하위 디렉터리를 삭제하여 이 빠른 시작의 작업을 정리합니다.

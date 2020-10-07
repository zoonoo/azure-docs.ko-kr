---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: 03680a2a6b4792a2bf522eff1462e29439e0f61b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055398"
---
[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org) 및 NPM.
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer 리소스 만들기"  target="_blank">Personalizer 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Personalizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

`npm init -y` 명령을 실행하여 `package.json` 파일을 만듭니다.

```console
npm init -y
```

기본 설정된 편집기 또는 IDE에서 `sample.js`라는 새 Node.js 애플리케이션을 만들고 리소스의 엔드포인트 및 구독 키에 대한 변수를 만듭니다. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Personalizer용 Node.js 라이브러리 설치

다음 명령을 사용하여 Node.js용 Personalizer 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/cognitiveservices-personalizer --save
```

이 빠른 시작에 대해 남아 있는 NPM 패키지를 설치합니다.

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>개체 모델

Personalizer 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) 개체입니다.

콘텐츠의 가장 좋은 단일 항목을 요청하려면 [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)를 만든 다음, [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) 메서드에 전달합니다. Rank 메서드는 RankResponse를 반환합니다.

보상을 Personalizer에 보내려면 [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)를 만든 다음, Events 클래스의 [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) 메서드에 전달합니다.

이 빠른 시작에서 보상을 결정하는 것은 간단합니다. 프로덕션 시스템에서 [보상 점수](../concept-rewards.md)에 영향을 주는 요소와 크기를 결정하는 것은 복잡한 프로세스일 수 있으며, 시간이 지남에 따라 변경될 수 있습니다. 이는 Personalizer 아키텍처에서 기본 설계 결정 중 하나여야 합니다.

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Node.js용 Personalizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [Personalizer 클라이언트 만들기](#authenticate-the-client)
* [순위 API](#request-the-best-action)
* [보상 API](#send-a-reward)

## <a name="authenticate-the-client"></a>클라이언트 인증

이전에 만든 `serviceKey` 및 `baseUri`를 사용하여 `PersonalizerClient`를 인스턴스화합니다.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>작업으로 표현되는 콘텐츠 선택 항목 가져오기

작업은 Personalizer에서 최상의 콘텐츠 항목을 선택할 수 있는 콘텐츠 선택 항목을 나타냅니다. Program 클래스에 다음 메서드를 추가하여 작업 세트와 해당 기능을 표시합니다.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>학습 루프 만들기

Personalizer 학습 루프는 [순위](#request-the-best-action) 및 [보상](#send-a-reward) 호출의 주기입니다. 이 빠른 시작에서는 각 순위 호출을 수행하여 콘텐츠를 맞춤 설정한 다음, 보상 호출을 수행하여 서비스에서 얼마나 잘 수행되었는지 Personalizer에 알려줍니다.

다음 코드는 명령줄에서 사용자에게 해당 기본 설정을 요청하고, 해당 정보를 Personalizer로 보내어 최상의 작업을 선택하고, 고객에게 해당 선택 항목을 제시하여 목록에서 선택한 다음, Personalizer에 대한 보상을 서비스에서 얼마나 잘 지정했는지 알려주는 주기를 반복합니다.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

다음 섹션에서 순위 및 보상 호출에 대해 자세히 살펴보겠습니다.

코드 파일을 실행하기 전에 [콘텐츠 선택을 가져오는](#get-content-choices-represented-as-actions) 다음 메서드를 추가합니다.

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>최상의 작업 요청

프로그램에서는 순위 요청을 수행하기 위해 사용자의 기본 설정에서 콘텐츠 선택 항목을 만들도록 요청합니다. 이 프로세스는 작업에서 제외할 콘텐츠(`excludeActions`로 표시됨)를 만들 수 있습니다. 순위 요청에는 순위가 지정된 응답을 받을 수 있는 [작업](../concepts-features.md#actions-represent-a-list-of-options)과 해당 기능, currentContext 기능, excludeActions 및 고유한 순위 이벤트 ID가 필요합니다.

이 빠른 시작에는 시간 및 사용자 음식 기본 설정에 대한 간단한 컨텍스트 기능이 있습니다. 프로덕션 시스템에서 [작업 및 기능](../concepts-features.md)을 결정하고 [평가](../concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>보상 보내기

보상 요청에서 보상 점수를 가져오기 위해 프로그램은 명령줄에서 사용자의 선택 사항을 가져와서 숫자 값을 각 선택 항목에 할당한 다음, 고유한 이벤트 ID와 보상 점수를 숫자 값으로 보상 API에 보냅니다.

이 빠른 시작에서는 0 또는 1의 간단한 숫자를 보상 점수로 할당합니다. 프로덕션 시스템에서 특정 요구 사항에 따라 [보상](../concept-rewards.md) 호출에 보내는 시기와 대상을 결정하는 것은 간단한 문제가 아닐 수 있습니다.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>프로그램 실행

애플리케이션 디렉터리에서 Node.js를 사용하여 애플리케이션을 실행합니다.

```console
node sample.js
```

![빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

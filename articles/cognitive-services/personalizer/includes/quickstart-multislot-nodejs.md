---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: 551b6901b60e55e9496cf5c4483aa3a05dfd72a7
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382336"
---
[참조 설명서](/javascript/api/@azure/cognitiveservices-personalizer/) | [다중 슬롯 개념](..\concept-multi-slot-personalization.md) | [샘플](https://aka.ms/personalizer/ms-nodejs)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org) 및 NPM.
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer 리소스 만들기"  target="_blank">Personalizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Personalizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

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
const axios = require('axios');
const { v4: uuidv4 } = require('uuid');
const readline = require('readline-sync');
// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const PersonalizationBaseUrl = 'https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com';
// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const ResourceKey = '<REPLACE-WITH-YOUR-PERSONALIZER-KEY>';
```

### <a name="install-the-npm-packages-for-quickstart"></a>빠른 시작용 NPM 패키지 설치

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid axios --save
```

## <a name="object-model"></a>개체 모델

각 슬롯에 대한 콘텐츠의 가장 적합한 단일 항목을 요청하려면 [rankRequest]를 만든 다음, 게시 요청을 [multislot/rank] 엔드포인트(https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/Rank) 로 보냅니다. 그러면 응답이 [rankResponse]로 구문 분석됩니다.

Personalizer에 보상 점수를 보내려면 [rewards]를 만든 다음, 게시 요청을 [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/Events_Reward)로 보냅니다.

이 빠른 시작에서 보상 점수를 결정하는 방법은 간단합니다. 프로덕션 시스템에서 [보상 점수](../concept-rewards.md)에 영향을 주는 요소와 크기를 결정하는 것은 복잡한 프로세스일 수 있으며, 시간이 지남에 따라 변경될 수 있습니다. 이 설계 결정은 Personalizer 아키텍처에서 기본 설계 결정 중 하나여야 합니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 NodeJS에 대한 HTTP 요청을 전송하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [기본 URL 만들기](#create-base-urls)
* [순위 API](#request-the-best-action)
* [보상 API](#send-a-reward)


## <a name="create-base-urls"></a>기본 URL 만들기

이 섹션에서는 다음과 같은 두 가지 작업을 수행합니다.
* 순위 및 보상 URL 생성
* 순위/보상 요청 헤더 생성

기본 URL을 사용하는 순위/보상 URL 및 리소스 키를 사용하는 요청 헤더를 생성합니다.

```javascript
const MultiSlotRankUrl = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/rank');
const MultiSlotRewardUrlBase = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/events/');
const Headers = {
    'ocp-apim-subscription-key': ResourceKey,
    'Content-Type': 'application/json'
};
```

## <a name="get-content-choices-represented-as-actions"></a>작업으로 표현되는 콘텐츠 선택 항목 가져오기

작업은 Personalizer에서 최상의 콘텐츠 항목을 선택할 수 있는 콘텐츠 선택 항목을 나타냅니다. 스크립트에 다음 메서드를 추가하여 동작 집합과 해당 기능을 표시합니다. 

```javascript
function getActions() {
    return [
        {
            'id': 'Red-Polo-Shirt-432',
            'features': [
                {
                    'onSale': 'true',
                    'price': 20,
                    'category': 'Clothing'
                }
            ]
        },
        {
            'id': 'Tennis-Racket-133',
            'features': [
                {
                    'onSale': 'false',
                    'price': 70,
                    'category': 'Sports'
                }
            ]
        },
        {
            'id': '31-Inch-Monitor-771',
            'features': [
                {
                    'onSale': 'true',
                    'price': 200,
                    'category': 'Electronics'
                }
            ]
        },
        {
            'id': 'XBox-Series X-117',
            'features': [
                {
                    'onSale': 'false',
                    'price': 499,
                    'category': 'Electronics'
                }
            ]
        }
    ];
}
```

## <a name="get-user-preferences-for-context"></a>컨텍스트에 대한 사용자 기본 설정 검색

스크립트에 다음 메서드를 추가하여 명령줄에서 시간 및 사용자가 사용 중인 디바이스 유형에 대한 사용자의 입력을 가져옵니다. 이러한 메서드는 컨텍스트 기능으로 사용됩니다.

```javascript
function getContextFeatures() {
    const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
    const deviceFeatures = ['mobile', 'tablet', 'desktop'];

    let answer = readline.question('\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n');
    let selection = parseInt(answer);
    const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

    answer = readline.question('\nWhat type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n');
    selection = parseInt(answer);
    const device = selection >= 1 && selection <= 3 ? deviceFeatures[selection - 1] : deviceFeatures[0];

    console.log('Selected features:\n');
    console.log('Time of day: ' + timeOfDay + '\n');
    console.log('Device: ' + device + '\n');

    return [
        {
            'time': timeOfDay
        },
        {
            'device': device
        }
    ];
}
```

## <a name="get-slots"></a>슬롯 가져오기

슬롯은 사용자가 상호 작용할 페이지를 구성합니다. Personalizer는 정의된 각 슬롯에 표시할 작업을 결정합니다. 동작은 `ExcludeActions`로 표시된 특정 슬롯에서 제외할 수 있습니다. `BaselineAction`은 Personalizer를 사용하지 않고 표시되는 슬롯에 대한 기본 동작입니다.


이 빠른 시작에는 간단한 슬롯 기능이 있습니다. 프로덕션 시스템에서 [기능](../concepts-features.md)을 결정하고 [평가](../concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.

```javascript
function getSlots() {
    return [
        {
            'id': 'BigHeroPosition',
            'features': [
                {
                    'size': 'large',
                    'position': 'left',
                }
            ],
            'excludedActions': ['31-Inch-Monitor-771'],
            'baselineAction': 'Red-Polo-Shirt-432'
        },
        {
            'id': 'SmallSidebar',
            'features': [
                {
                    'size': 'small',
                    'position': 'right',
                }
            ],
            'excludedActions': ['Tennis-Racket-133'],
            'baselineAction': 'XBox-Series X-117'
        }
    ];
}
```

## <a name="make-http-requests"></a>HTTP 요청 만들기

다중 슬롯 순위 및 보상 호출에 대한 게시 요청을 Personalizer 엔드포인트로 보냅니다.

```javascript
async function sendMultiSlotRank(rankRequest) {
    try {
        let response = await axios.post(MultiSlotRankUrl, rankRequest, { headers: Headers })
        return response.data;
    }
    catch (err) {
        console.log(err);
    }
}
```

```javascript
async function sendMultiSlotReward(rewardRequest, eventId) {
    try {
        let rewardUrl = MultiSlotRewardUrlBase.concat(eventId, '/reward');
        let response = await axios.post(rewardUrl, rewardRequest, { headers: Headers })
    }
    catch (err) {
        console.log(err);
    }
}
```

## <a name="get-feedback-for-personalizer-decisions"></a>Personalizer 결정에 대한 피드백 얻기


스크립트에 다음 메서드를 추가합니다. 명령줄 프롬프트를 통해 Personalizer가 각 슬롯에 대해 좋은 결정을 내렸는지 여부를 알릴 수 있습니다.

```javascript
function getRewardForSlot() {
    let answer = readline.question('\nIs this correct? (y/n)\n').toUpperCase();
    if (answer === 'Y') {
        console.log('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n');
        return 1;
    }
    else if (answer === 'N') {
        console.log('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n');
        return 0;
    }
    console.log('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n');
    return 0;
}
```

## <a name="create-the-learning-loop"></a>학습 루프 만들기

Personalizer 학습 루프는 [순위](#request-the-best-action) 및 [보상](#send-a-reward) 호출의 주기입니다. 이 빠른 시작에서는 각 순위 호출을 수행하여 콘텐츠를 맞춤 설정한 다음, 보상 호출을 수행하여 서비스에서 얼마나 잘 수행되었는지 Personalizer에 알려줍니다.

다음 코드는 명령줄을 통해 사용자에게 해당 기본 설정을 요청하고, 해당 정보를 Personalizer로 보내어 각 슬롯에 대한 최상의 작업을 선택하고, 고객에게 해당 선택 항목을 제시하여 목록에서 선택한 다음, 보상 점수를 Personalizer에 전달하여 서비스에서 얼마나 잘 지정했는지 알려주는 주기를 반복합니다.

```javascript
runLoop = true;

(async () => {
    do {

        let multiSlotRankRequest = {};

        // Generate an ID to associate with the request.
        multiSlotRankRequest.eventId = uuidv4();

        // Get context information from the user.
        multiSlotRankRequest.contextFeatures = getContextFeatures();

        // Get the actions list to choose from personalization with their features.
        multiSlotRankRequest.actions = getActions();

        // Get the list of slots for which Personalizer will pick the best action.
        multiSlotRankRequest.slots = getSlots();

        multiSlotRankRequest.deferActivation = false;

        //Rank the actions for each slot
        try {
            var multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
        }
        catch (err) {
            console.log(err);
        }

        let multiSlotrewards = {};
        multiSlotrewards.reward = [];

        for (i = 0; i < multiSlotRankResponse.slots.length; i++) {
            console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));

            let slotReward = {};
            slotReward.slotId = multiSlotRankResponse.slots[i].id;
            // User agrees or disagrees with Personalizer decision for slot
            slotReward.value = getRewardForSlot();
            multiSlotrewards.reward.push(slotReward);
        }

        // Send the rewards for the event
        try {
            await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
        }
        catch (err) {
            console.log(err);
        }

        let answer = readline.question('\nPress q to break, any other key to continue:\n').toUpperCase();
        if (answer === 'Q') {
            runLoop = false;
        }

    } while (runLoop);
})()
```

다음 섹션에서 순위 및 보상 호출에 대해 자세히 살펴보겠습니다.

[콘텐츠 선택 항목을 가져오고](#get-content-choices-represented-as-actions), [컨텍스트에 대한 사용자 기본 설정을 가져오고](#get-user-preferences-for-context), [슬롯을 가져오고](#get-slots), [HTTP 요청을 수행하고](#make-http-requests), 코드 파일을 실행하기 전에 [각 슬롯에 대해 보상을 받는](#get-feedback-for-personalizer-decisions) 다음 메서드를 추가합니다.

* getActions
* getContextFeatures
* getSlots
* sendRank
* sendReward
* getRewardForSlot

## <a name="request-the-best-action"></a>최상의 작업 요청

프로그램에서는 순위 요청을 수행하기 위해 사용자의 기본 설정에서 콘텐츠 선택 항목을 만들도록 요청합니다. 요청 본문에는 응답을 받을 수 있는 컨텍스트 기능, 작업 및 해당 기능, 슬롯 및 해당 기능과 고유한 이벤트 ID가 포함됩니다. `sendMultiSlotRank` 메서드에서 다중 슬롯 순위 요청을 보내려면 rankRequest가 필요합니다.

이 빠른 시작에는 시간 및 사용자 디바이스에 대한 간단한 컨텍스트 기능이 있습니다. 프로덕션 시스템에서 [작업 및 기능](../concepts-features.md)을 결정하고 [평가](../concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.

```javascript
let multiSlotRankRequest = {};

// Generate an ID to associate with the request.
multiSlotRankRequest.eventId = uuidv4();

// Get context information from the user.
multiSlotRankRequest.contextFeatures = getContextFeatures();

// Get the actions list to choose from personalization with their features.
multiSlotRankRequest.actions = getActions();

// Get the list of slots for which Personalizer will pick the best action.
multiSlotRankRequest.slots = getSlots();

multiSlotRankRequest.deferActivation = false;

//Rank the actions for each slot
try {
    var multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
}
catch (err) {
    console.log(err);
}
```

## <a name="send-a-reward"></a>보상 보내기

보상 요청에서 보낼 보상 점수를 가져오기 위해 프로그램은 명령줄을 통해 각 슬롯에 대한 사용자의 선택 사항을 가져와서 숫자 값을 각 선택 항목에 할당한 다음, 고유한 이벤트 ID, 슬롯 ID 및 보상 점수를 숫자 값으로 `sendMultiSlotReward` 메서드에 보냅니다. 각 슬롯에 대해 보상을 정의할 필요가 없습니다.

이 빠른 시작에서는 0 또는 1의 간단한 숫자를 보상 점수로 할당합니다. 프로덕션 시스템에서 특정 요구 사항에 따라 [보상](../concept-rewards.md) 호출에 보내는 시기와 대상을 결정하는 것은 간단한 문제가 아닐 수 있습니다.

```javascript
let multiSlotrewards = {};
multiSlotrewards.reward = [];

for (i = 0; i < multiSlotRankResponse.slots.length; i++) {
    console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));

    let slotReward = {};
    slotReward.slotId = multiSlotRankResponse.slots[i].id;
    // User agrees or disagrees with Personalizer decision for slot
    slotReward.value = getRewardForSlot();
    multiSlotrewards.reward.push(slotReward);
}

// Send the rewards for the event
try {
    await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
}
catch (err) {
    console.log(err);
}
```

## <a name="run-the-program"></a>프로그램 실행

애플리케이션 디렉터리에서 Node.js를 사용하여 애플리케이션을 실행합니다.

```console
node sample.js
```

![빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


[이 빠른 시작의 소스 코드](https://aka.ms/personalizer/ms-nodejs)가 제공됩니다.

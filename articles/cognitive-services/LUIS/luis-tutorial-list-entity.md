---
title: Nodejs를 사용하여 자동으로 엔터티에 목록 엔터티로 레이블 붙이기 | Microsoft Docs
description: 단어 또는 구의 LUIS 레이블 변형에 유용한 목록 엔터티를 추가하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: diberry
ms.openlocfilehash: 42fde2b24f851129e24257bbfe6d65a96e235485
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036782"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>엔터티 검색을 높이기 위한 목록 엔터티 사용 
이 자습서에서는 엔터티 검색을 높이기 위한 [목록 엔터티](luis-concept-entity-types.md) 사용을 설명합니다. 목록 엔터티는 용어가 정확히 일치하므로 레이블을 지정할 필요가 없습니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
* 목록 엔터티 만들기 
* 정규화된 값 및 동의어 추가
* 향상된 엔터티 ID의 유효성 검사

## <a name="prerequisites"></a>필수 조건

> [!div class="checklist"]
> * 최신 [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS 앱](luis-get-started-create-app.md). Home Automation 앱을 만들지 않은 경우 새 앱을 만들고 미리 작성된 도메인 **HomeAutomation**을 추가합니다. 앱을 학습하고 게시합니다. 
> * LUIS 앱에 대한 [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key)(여러 번 쿼리하는 경우), 앱 ID, 버전 ID 및 [지역](luis-reference-regions.md).

> [!Tip]
> 아직 구독이 없는 경우 [체험판 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

이 자습서에서는 모든 코드를 [LUIS 샘플 github 리포지토리](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity)에서 사용할 수 있습니다. 

## <a name="use-homeautomation-app"></a>HomeAutomation 앱 사용
HomeAutomation 앱은 조명, 엔터테인먼트 시스템 및 환경 제어(예: 냉난방)와 같은 장치 제어 기능을 제어합니다. 이러한 시스템은 제조업체 이름, 애칭, 머리글자어 및 속어를 포함할 수 있는 여러 개의 다른 이름이 있습니다. 

서로 다른 문화권 및 인구 통계에서 많은 이름을 갖는 하나의 시스템은 thermostat(자동 온도 조절기)입니다. thermostat은 집이나 빌딩의 냉난방 시스템을 제어할 수 있습니다.

다음 발언은 이상적으로 미리 작성된 엔터티 **HomeAutomation.Device**를 해결해야 합니다.

|#|발언|식별된 엔터티|score에 대한 임계값 수준 보기|
|--|--|--|--|
|1|turn on the ac|HomeAutomation.Device - “ac”|0.8748562|
|2|turn up the heat|HomeAutomation.Device - “heat”|0.784990132|
|3|make it colder|||

처음 두 발언은 다른 장치에 매핑됩니다. 세 번째 발언 “make it colder”는 장치에 매핑되지 않고 결과를 요청합니다. LUIS는 “colder”라는 용어를 알지 못합니다. 즉, thermostat이 요청된 장치입니다. 이상적으로 LUIS는 동일한 장치에 대한 이러한 모든 발언을 해결해야 합니다. 

## <a name="use-a-list-entity"></a>목록 엔터티 사용
HomeAutomation.Device 엔터티는 적은 수의 장치 또는 이름 변형이 적은 경우에 적합합니다. 사무실 건물 또는 캠퍼스의 경우 장치 이름은 HomeAutomation.Device 엔터티의 유용성 이상으로 증가합니다. 

건물 또는 캠퍼스 내 장치에 대한 용어 집합이 알려진 집합이므로 그것이 거대한 집합인 경우라도 **목록 엔터티**는 이 시나리오에 적합합니다. 목록 엔터티를 사용하면 LUIS가 thermostat에 대한 집합에서 가능한 값을 수신하고, 단일 장치 “thermostat”에서 해결할 수 있습니다. 

이 자습서에서는 thermostat으로 엔터티 목록을 만듭니다. 이 자습서에서 thermostat에 대한 대체 이름은 다음과 같습니다. 

|thermostat에 대한 대체 이름|
|--|
| ac |
| a/c|
| a-c|
|heater|
|hot|
|hotter|
|cold|
|colder|

LUIS가 새로운 대안을 결정해야 하는 경우에는 [구 목록](luis-concept-feature.md#how-to-use-phrase-lists)이 더 적절한 답입니다.

## <a name="create-a-list-entity"></a>목록 엔터티 만들기
Node.js 파일을 만들고 다음 코드를 복사합니다. authoringKey, appId, versionId 및 지역 값을 변경합니다.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

다음 명령을 사용하여 NPM 종속성을 설치하고, 코드를 실행하여 목록 엔터티를 만듭니다.

```Javascript
npm install && node add-entity-list.js
```

실행의 출력은 목록 엔터티의 ID입니다.

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>모델 학습
새 목록이 쿼리에 적용될 수 있도록 LUIS를 학습합니다. 학습은 두 부분으로 이루어집니다. 교육과 교육이 완료된 경우 상태 확인입니다. 여러 모델을 사용한 앱은 학습하는 데 몇 분 정도 걸릴 수 있습니다. 다음 코드는 앱을 학습한 다음, 학습에 성공할 때까지 대기합니다. 코드는 기다렸다 다시 시도하는 전략을 사용하여 429 “요청이 너무 많음” 오류를 방지합니다. 

Node.js 파일을 만들고 다음 코드를 복사합니다. authoringKey, appId, versionId 및 지역 값을 변경합니다.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

다음 명령을 사용하여 앱을 학습하는 코드를 실행합니다.

```Javascript
node train.js
```

실행의 출력은 LUIS 모델의 학습에서 각 반복의 상태입니다. 다음 실행은 교육에서 단 하나의 검사가 필요합니다.

```Javascript
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>모델 게시
목록 엔터티를 엔드포인트에서 사용할 수 있도록 게시합니다.

Node.js 파일을 만들고 다음 코드를 복사합니다. endpointKey, appId 및 지역 값을 변경합니다. 할당량 제한을 초과하여 이 파일을 호출하지 않을 경우 사용자의 authoringKey를 사용할 수 있습니다.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

다음 명령을 사용하여 앱을 쿼리하는 코드를 실행합니다.

```Javascript
node publish.js
```

다음 출력에는 모든 쿼리에 대한 엔드포인트 URL이 포함됩니다. 실제 JSON 결과에는 실제 appID가 포함될 수 있습니다. 

```JSON
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>앱 쿼리 
LUIS가 장치 유형을 확인하는 데 목록 엔터티가 유용하다는 점을 증명하기 위해 엔드포인트에서 앱을 쿼리합니다.

Node.js 파일을 만들고 다음 코드를 복사합니다. endpointKey, appId 및 지역 값을 변경합니다. 할당량 제한을 초과하여 이 파일을 호출하지 않을 경우 사용자의 authoringKey를 사용할 수 있습니다.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

다음 명령을 사용하여 코드를 실행하고 앱을 쿼리합니다.

```Javascript
node train.js
```

출력은 쿼리 결과입니다. 코드가 **verbose** 이름/값 쌍을 쿼리 문자열에 추가했으므로 출력에는 모든 의도와 해당 점수가 포함됩니다.

```JSON
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

**Thermostat**의 특정 장치는 “turn up the heat”의 결과 지향 쿼리를 통해 식별됩니다. 원래 HomeAutomation.Device 엔터티는 계속 앱에 있으므로 해당 결과도 확인할 수 있습니다. 

두 개의 다른 발언을 시도하여 thermostat으로 반환되는지 확인합니다. 

|#|발언|엔터티|형식|값|
|--|--|--|--|--|
|1|turn on the ac| ac | DevicesList | Thermostat|
|2|turn up the heat|heat| DevicesList |Thermostat|
|3|make it colder|colder|DevicesList|Thermostat|

## <a name="next-steps"></a>다음 단계

다른 목록 엔터티를 만들어 장치 위치를 방, 층 또는 건물로 확장할 수 있습니다. 

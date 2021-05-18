---
title: V3 API의 예측 엔드포인트 변경 내용
description: 쿼리 예측 엔드포인트 V3 API가 변경되었습니다. 이 가이드를 사용하여 버전 3 엔드포인트 API로 마이그레이션하는 방법을 이해할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98624306"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3에 대한 예측 엔드포인트 변경

쿼리 예측 엔드포인트 V3 API가 변경되었습니다. 이 가이드를 사용하여 버전 3 엔드포인트 API로 마이그레이션하는 방법을 이해할 수 있습니다.

**일반 공급 상태** - 이 V3 API에는 V2 API의 중요한 JSON 요청 및 응답 변경 내용이 포함되어 있습니다.

V3 API는 다음과 같은 새로운 기능을 제공합니다.

* [외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [동적 목록](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [미리 빌드된 엔터티 JSON 변경](#prebuilt-entity-changes)

예측 엔드포인트 [요청](#request-changes) 및 [응답](#response-changes)에는 다음을 포함하여 위에 나열된 새 기능을 지원하기 위해 중요한 변경 사항이 있습니다.

* [응답 개체 변경](#top-level-json-changes)
* [엔터티 이름 대신 엔터티 역할 이름 참조](#entity-role-name-instead-of-entity-name)
* [발화에 엔터티를 표시하는 속성](#marking-placement-of-entities-in-utterances)

[참조 설명서](https://aka.ms/luis-api-v3)는 V3에서 사용할 수 있습니다.

## <a name="v3-changes-from-preview-to-ga"></a>V3가 미리 보기에서 GA로 변경

V3가 GA로 전환되는 과정에서 다음과 같이 변경되었습니다.

* 다음 미리 빌드된 엔터티는 서로 다른 JSON 응답을 갖습니다.
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * 측정 가능한 단위 키 이름이 `units`에서 `unit`으로 변경

* 요청 본문 JSON 변경:
    * `preferExternalEntities` ~ `preferExternalEntities`
    * 외부 엔터티에 대한 선택적 `score` 매개 변수

* 응답 본문 JSON 변경:
    * `normalizedQuery`가 제거됨

## <a name="suggested-adoption-strategy"></a>제안된 채택 전략

Bot Framework, Bing Spell Check V7을 사용하거나 LUIS 앱 작성만 마이그레이션하려면 V2 엔드포인트를 계속 사용합니다.

클라이언트 애플리케이션 또는 통합(Bot Framework 및 Bing Spell Check V7)이 영향을 받지 않고 LUIS 앱 작성과 예측 엔드포인트를 동시에 마이그레이션하는 것을 알고 있는 경우 V3 예측 엔드포인트 사용을 시작합니다. V2 예측 엔드포인트는 계속 사용할 수 있으며 좋은 대체 전략입니다.


## <a name="not-supported"></a>지원되지 않음

### <a name="bing-spell-check"></a>Bing 맞춤법 검사

이 API는 V3 예측 엔드포인트에서 지원되지 않습니다. 계속해서 V2 API 예측 엔드포인트를 사용하여 맞춤법을 수정하세요. V3 API를 사용하는 동안 맞춤법 수정이 필요한 경우 LUIS API로 텍스트를 보내기 전에 클라이언트 애플리케이션에서 [Bing Spell Check](../bing-spell-check/overview.md) API를 호출하고 텍스트를 올바른 철자로 변경합니다.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework 및 Azure Bot Service 클라이언트 애플리케이션

Bot Framework V4.7이 릴리스될 때까지 V2 API 예측 엔드포인트를 계속 사용합니다.


## <a name="endpoint-url-changes"></a>엔드포인트 URL 변경

### <a name="changes-by-slot-name-and-version-name"></a>슬롯 이름 및 버전 이름 변경 내용

[V3 엔드포인트 HTTP 형식](developer-reference-resource.md#rest-endpoints)의 호출이 변경되었습니다.

버전을 기준으로 쿼리하려면 먼저 [를 사용하여 ](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)API를 통해 게시`"directVersionPublish":true`해야 합니다. 슬롯 이름 대신 버전 ID를 참조하는 엔드포인트를 쿼리합니다.

|`SLOT-NAME`에 대한 유효한 값|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>변경 내용 요청

### <a name="query-string-changes"></a>문자열 변경 내용 쿼리

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3 POST 본문

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|속성|유형|버전|기본값|목적|
|--|--|--|--|--|
|`dynamicLists`|array|V3만|필수 아님.|[동적 목록](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)을 사용하면 이미 LUIS 앱에 있는 기존의 학습되고 게시된 목록 엔터티를 확장할 수 있습니다.|
|`externalEntities`|array|V3만|필수 아님.|[외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)를 통해 LUIS 앱은 런타임 중에 엔터티를 식별하고 레이블을 지정하여 이를 기존 엔터티에 대한 기능으로 사용할 수 있습니다. |
|`options.datetimeReference`|문자열|V3만|기본값 없음|[datetimeV2 오프셋](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)을 확인하는 데 사용됩니다. datetimeReference의 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)입니다.|
|`options.preferExternalEntities`|boolean|V3만|false|예측에 사용자의 [외부 엔터티(기존 엔터티와 이름이 같음)](schema-change-prediction-runtime.md#override-existing-model-predictions)를 사용할지 모델의 기존 엔터티를 예측에 사용할지 지정합니다. |
|`query`|문자열|V3만|필수 요소.|**V2에서** 예측할 발화는 `q` 매개 변수에 있습니다. <br><br>**V3에서** 기능은 `query` 매개 변수로 전달됩니다.|

## <a name="response-changes"></a>응답 변경

쿼리 응답 JSON은 가장 자주 사용되는 데이터에 대한 대규모 프로그래밍 방식의 액세스를 허용하도록 변경되었습니다.

### <a name="top-level-json-changes"></a>최상위 수준의 JSON 변경



V2의 최상위 JSON 속성은 `verbose`가 true로 설정된 경우 `intents` 속성에서 모든 의도 및 해당 점수를 반환합니다.

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3의 최상위 JSON 속성은 다음과 같습니다.

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

`intents` 개체는 순서가 지정되지 않은 목록입니다. `intents`의 첫 번째 하위가 `topIntent`에 해당하는 것으로 가정하지 않습니다. 대신 `topIntent` 값을 사용하여 점수를 찾습니다.

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

응답 JSON 스키마 변경 내용에서는 다음을 허용합니다.

* 원래 발화 `query`와 반환된 예측 `prediction` 간의 명확한 구분.
* 예측된 데이터에 대한 프로그래밍 방식의 용이한 액세스. V2에서 배열을 열거하는 대신 의도 및 엔터티 모두에 대한 **이름** 으로 값에 액세스할 수 있습니다. 예측된 엔터티 역할의 경우 역할 이름이 전체 앱에서 고유하므로 반환됩니다.
* 데이터 형식이 결정되면 적용됩니다. 숫자는 더 이상 문자열로 반환되지 않습니다.
* `$instance` 개체에 반환되는 첫 번째 우선 순위 예측 정보와 추가 메타데이터 간의 구분.

### <a name="entity-response-changes"></a>엔터티 응답 변경

#### <a name="marking-placement-of-entities-in-utterances"></a>발화에서 엔터티 배치 표시

**V2에서** 엔터티는 발화에서 `startIndex` 및 `endIndex`로 표시되었습니다.

**V3에서** 엔터티는 `startIndex` 및 `entityLength`로 표시됩니다.

#### <a name="access-instance-for-entity-metadata"></a>엔터티 메타데이터에 대한 `$instance` 액세스

엔터티 메타데이터가 필요한 경우 쿼리 문자열은 `verbose=true` 플래그를 사용해야 하며 응답에는 `$instance` 개체의 메타데이터가 포함됩니다. 다음 섹션의 JSON 응답에 예가 나와 있습니다.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>예측된 각 엔터티는 배열로 표시됩니다.

발화에서 각 엔터티를 두 번 이상 예측할 수 있으므로 `prediction.entities.<entity-name>` 개체는 배열을 포함합니다.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>미리 빌드된 엔터티 변경

V3 응답 개체에는 미리 빌드된 엔터티에 대한 변경 내용이 포함됩니다. 자세한 내용은 [특정 미리 빌드된 엔터티](luis-reference-prebuilt-entities.md)를 참조하세요.

#### <a name="list-entity-prediction-changes"></a>목록 엔터티 예측 변경

목록 엔터티 예측에 대한 JSON이 배열의 배열로 변경되었습니다.

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
각 내부 배열은 발화 내부의 텍스트에 해당합니다. 동일한 텍스트가 목록 엔터티의 여러 하위 목록에 나타날 수 있기 때문에 내부 개체는 배열입니다.

`entities` 개체를 `$instance` 개체로 매핑할 때 목록 엔터티 예측에 대해 개체의 순서가 유지됩니다.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>엔터티 이름 대신 엔터티 역할 이름

V2에서 `entities` 배열은 엔터티 이름이 고유 식별자인 예측된 엔터티를 모두 반환했습니다. V3에서 엔터티가 역할을 사용하고 예측을 엔터티 역할에 사용하는 경우 기본 식별자는 역할 이름입니다. 이는 엔터티 역할 이름이 다른 모델(의도, 엔터티) 이름을 포함하여 전체 앱에서 고유해야 하기 때문에 가능합니다.

다음 예에서는 텍스트 `Yellow Bird Lane`을 포함하는 발화를 고려합니다. 이 텍스트는 `Destination`의 사용자 지정 `Location` 엔터티의 역할로 예측됩니다.

|발화 텍스트|엔터티 이름|역할 이름|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2에서 엔터티는 역할을 개체의 속성으로 사용하여 _엔터티 이름_ 으로 식별됩니다.

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

V3에서 엔터티는 역할에 대한 예측이 다음과 같을 경우 _엔터티 역할_ 에서 참조됩니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3에서는 `verbose` 플래그를 사용하여 엔터티 메타데이터를 반환할 때와 동일한 결과를 나타냅니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>예측 시간에 앱 확장

예측 런타임에서 앱을 확장하는 방법에 대한 [개념](schema-change-prediction-runtime.md)을 알아봅니다.


## <a name="next-steps"></a>다음 단계

V3 API 설명서를 사용하여 LUIS [엔드포인트](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) API의 기존 REST 호출을 업데이트합니다.

---
title: V3 API의 예측 끝점 변경 내용
description: 쿼리 예측 끝점 V3 Api가 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 끝점 Api로 마이그레이션하는 방법을 이해할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.author: diberry
ms.openlocfilehash: 3e4567eea02b3b7db9514f4e03c7f7f36496449b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309435"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3에 대 한 예측 끝점 변경

쿼리 예측 끝점 V3 Api가 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 끝점 Api로 마이그레이션하는 방법을 이해할 수 있습니다.

**일반적으로 사용 가능한 상태** -이 V3 api에는 V2 api의 중요 한 JSON 요청 및 응답 변경 내용이 포함 되어 있습니다.

V3 API는 다음과 같은 새로운 기능을 제공 합니다.

* [외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [동적 목록](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [미리 작성 한 엔터티 JSON 변경 내용](#prebuilt-entity-changes)

예측 끝점 [요청](#request-changes) 및 [응답](#response-changes) 은 다음을 포함 하 여 위에 나열 된 새 기능을 지원 하기 위해 중요 한 변경 사항이 있습니다.

* [응답 개체 변경](#top-level-json-changes)
* [엔터티 이름 대신 엔터티 역할 이름 참조](#entity-role-name-instead-of-entity-name)
* [길이 발언에 엔터티를 표시 하는 속성](#marking-placement-of-entities-in-utterances)

[참조 설명서](https://aka.ms/luis-api-v3) 는 V3에서 사용할 수 있습니다.

## <a name="v3-changes-from-preview-to-ga"></a>V3이 미리 보기에서 GA로 변경 됩니다.

V 3에서 GA로 이동 하는 과정의 일부로 다음과 같이 변경 했습니다.

* 다음 미리 작성 된 엔터티에는 서로 다른 JSON 응답이 있습니다.
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * 에서로 측정 가능한 단위 키 이름 `units``unit`

* 요청 본문 JSON 변경:
    * 시작 `preferExternalEntities` 까지 `preferExternalEntities`
    * `score`외부 엔터티에 대 한 선택적 매개 변수

* 응답 본문 JSON 변경 내용:
    * `normalizedQuery` 제거

## <a name="suggested-adoption-strategy"></a>제안 된 채택 전략

Bot Framework를 사용 하거나 V7를 Bing Spell Check 하거나 LUIS 앱 제작만 마이그레이션하려면 V2 끝점을 계속 사용 합니다.

클라이언트 응용 프로그램 또는 통합 (Bot Framework 및 Bing Spell Check V7)이 영향을 받지 않고 LUIS 앱 작성과 예측 끝점을 동시에 마이그레이션하는 것을 알고 있는 경우 V3 예측 끝점 사용을 시작 합니다. V2 예측 끝점은 계속 사용할 수 있으며 좋은 복구 전략입니다.


## <a name="not-supported"></a>지원되지 않음

### <a name="bing-spell-check"></a>Bing 맞춤법 검사

이 API는 V3 예측 끝점에서 지원 되지 않습니다. 계속 해 서 V2 API 예측 끝점을 사용 하 여 맞춤법을 수정 하세요. V3 API를 사용 하는 동안 맞춤법 수정이 필요한 경우 LUIS API로 텍스트를 보내기 전에 클라이언트 응용 프로그램에서 [Bing Spell Check](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) api를 호출 하 고 텍스트를 올바른 철자에 맞게 변경 합니다.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework 및 Azure Bot Service 클라이언트 응용 프로그램

Bot Framework의 V 4.7이 릴리스될 때까지 V2 API 예측 끝점을 계속 사용 합니다.

## <a name="v2-api-deprecation"></a>V2 API 사용 중단

V2 예측 API는 V3 preview 이후 최소 9 개월 동안 (6 월 8 일, 2020)에는 사용 되지 않습니다.

## <a name="endpoint-url-changes"></a>끝점 URL 변경

### <a name="changes-by-slot-name-and-version-name"></a>슬롯 이름 및 버전 이름 변경

[V3 끝점 HTTP 호출의 형식이](developer-reference-resource.md#rest-endpoints) 변경 되었습니다.

버전을 기준으로 쿼리하려면 먼저를 사용 하 여 API를 [통해 게시](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) 해야 `"directVersionPublish":true` 합니다. 슬롯 이름 대신 버전 ID를 참조 하는 끝점을 쿼리 합니다.

|유효한 값 `SLOT-NAME`|
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

|속성|형식|버전|기본값|목적|
|--|--|--|--|--|
|`dynamicLists`|array|V3만|필요하지 않습니다.|[동적 목록을](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) 사용 하면 이미 LUIS 앱에 있는 기존의 학습 및 게시 된 목록 엔터티를 확장할 수 있습니다.|
|`externalEntities`|array|V3만|필요하지 않습니다.|[외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) 를 통해 LUIS 앱은 런타임 중에 엔터티를 식별 하 고 레이블을 지정 하는 기능을 기존 엔터티에 대 한 기능으로 사용할 수 있습니다. |
|`options.datetimeReference`|문자열|V3만|기본값 없음|[DatetimeV2 오프셋](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)을 확인 하는 데 사용 됩니다. DatetimeReference의 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)입니다.|
|`options.preferExternalEntities`|boolean|V3만|false|사용자의 [외부 엔터티 (기존 엔터티와 이름이 같은)](schema-change-prediction-runtime.md#override-existing-model-predictions) 를 사용 하거나 모델의 기존 엔터티를 예측에 사용 하는지 여부를 지정 합니다. |
|`query`|문자열|V3만|필수 요소.|**V2에서**예측할 utterance는 `q` 매개 변수입니다. <br><br>**V3에서**기능은 `query` 매개 변수로 전달 됩니다.|

## <a name="response-changes"></a>응답 변경

쿼리 응답 JSON은 가장 자주 사용 되는 데이터에 대 한 프로그래밍 방식의 액세스를 허용 하도록 변경 되었습니다.

### <a name="top-level-json-changes"></a>최상위 수준의 JSON 변경 내용



V 2의 상위 JSON 속성은 `verbose` 가 true로 설정 된 경우 속성에서 모든 의도 및 해당 점수를 반환 합니다 `intents` .

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3의 top JSON 속성은 다음과 같습니다.

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

`intents`개체가 순서가 지정 되지 않은 목록입니다. 의 첫 번째 자식이에 해당 하는 것으로 가정 하지 마십시오 `intents` `topIntent` . 대신 값을 사용 `topIntent` 하 여 점수를 찾습니다.

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

응답 JSON 스키마 변경 내용:

* 원래 utterance의 차이를 명확 하 게 구분 하 `query` 고 예측을 반환 `prediction` 합니다.
* 예측 된 데이터에 대 한 프로그래밍 방식의 액세스를 용이 하 게 합니다. V 2에서 배열을 열거 하는 대신 **이름** 및 엔터티 모두에 대 한 값에 액세스할 수 있습니다. 예측 된 엔터티 역할의 경우 전체 앱에서 고유 하므로 역할 이름이 반환 됩니다.
* 데이터 형식이 결정 되 면 적용 됩니다. 숫자는 더 이상 문자열로 반환 되지 않습니다.
* 첫 번째 우선 순위 예측 정보와 추가 메타 데이터의 차이 (개체에 반환 됨) `$instance`

### <a name="entity-response-changes"></a>엔터티 응답 변경

#### <a name="marking-placement-of-entities-in-utterances"></a>길이 발언에서 엔터티 배치 표시

V 2 **에서**엔터티는 and를 사용 하 여 utterance로 표시 되었습니다 `startIndex` `endIndex` .

**V3에서**엔터티는 및로 표시 됩니다 `startIndex` `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>`$instance`엔터티 메타 데이터에 대 한 액세스

엔터티 메타 데이터가 필요한 경우 쿼리 문자열은 플래그를 사용 해야 하 `verbose=true` 고 응답에는 개체의 메타 데이터가 포함 `$instance` 됩니다. 다음 섹션의 JSON 응답에 예제가 나와 있습니다.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>예측 된 각 엔터티는 배열로 표시 됩니다.

`prediction.entities.<entity-name>`Utterance에서 각 엔터티를 두 번 이상 예측할 수 있으므로 개체는 배열을 포함 합니다.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>미리 작성 한 엔터티 변경

V3 response 개체에는 미리 빌드된 엔터티에 대 한 변경 내용이 포함 됩니다. 미리 작성 한 [특정 엔터티](luis-reference-prebuilt-entities.md) 를 검토 하 여 자세히 알아보세요.

#### <a name="list-entity-prediction-changes"></a>엔터티 예측 변경 내용 나열

목록 엔터티 예측에 대 한 JSON이 배열의 배열로 변경 되었습니다.

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
각 내부 배열은 utterance 내부의 텍스트에 해당 합니다. 동일한 텍스트가 목록 엔터티의 여러 하위 목록에 나타날 수 있기 때문에 내부 개체는 배열입니다.

개체 `entities` 를 개체로 매핑할 때 `$instance` 목록 엔터티 예측에 대해 개체의 순서가 유지 됩니다.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>엔터티 이름 대신 엔터티 역할 이름

V 2에서 `entities` 배열은 엔터티 이름이 고유 식별자 인 예측 된 엔터티를 모두 반환 했습니다. V3에서 엔터티가 역할을 사용 하 고 예측을 엔터티 역할에 사용 하는 경우 기본 식별자는 역할 이름입니다. 이는 엔터티 역할 이름이 다른 모델 (의도, 엔터티) 이름을 포함 하 여 전체 앱에서 고유 해야 하기 때문에 가능 합니다.

다음 예제에서는 텍스트를 포함 하는 utterance을 고려 `Yellow Bird Lane` 합니다. 이 텍스트는의 사용자 지정 `Location` 엔터티의 역할로 예측 됩니다 `Destination` .

|Utterance 텍스트|엔터티 이름|역할 이름|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V 2에서 엔터티는 역할을 개체의 속성으로 사용 하 여 _엔터티 이름_ 으로 식별 됩니다.

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

V3에서 엔터티는 역할에 대 한 예측 인 경우 _엔터티 역할_에서 참조 됩니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3에서 다음과 같이 플래그를 사용 `verbose` 하 여 엔터티 메타 데이터를 반환 합니다.

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

예측 런타임에서 앱을 확장 하는 방법에 대 한 [개념](schema-change-prediction-runtime.md) 을 알아봅니다.

## <a name="deprecation"></a>사용 중단

V2 API는 V3 미리 보기 후 9 개월 이상 사용 되지 않습니다.

## <a name="next-steps"></a>다음 단계

V3 API 설명서를 사용 하 여 LUIS [끝점](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) api에 대 한 기존 REST 호출을 업데이트 합니다.

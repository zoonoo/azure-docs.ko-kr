---
title: V3 API의 예측 끝점 변경
description: 쿼리 예측 끝점 V3 API가 변경되었습니다. 이 가이드를 사용하여 버전 3 끝점 API로 마이그레이션하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117378"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3에 대한 예측 끝점 변경

쿼리 예측 끝점 V3 API가 변경되었습니다. 이 가이드를 사용하여 버전 3 끝점 API로 마이그레이션하는 방법을 이해합니다.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**일반적으로 사용 가능한 상태** - 이 V3 API에는 중요한 JSON 요청 및 V2 API의 응답 변경 사항이 포함됩니다.

V3 API는 다음과 같은 새로운 기능을 제공합니다.

* [외부 엔터티](#external-entities-passed-in-at-prediction-time)
* [동적 목록](#dynamic-lists-passed-in-at-prediction-time)
* [미리 빌드된 엔터티 JSON 변경 내용](#prebuilt-entity-changes)

예측 끝점 [요청](#request-changes) 및 [응답에는](#response-changes) 다음을 포함하여 위에 나열된 새 기능을 지원하기 위해 상당한 변경 사항이 있습니다.

* [응답 개체 변경](#top-level-json-changes)
* [엔터티 이름 대신 엔터티 역할 이름 참조](#entity-role-name-instead-of-entity-name)
* [발언에서 엔터티를 표시하는 속성](#marking-placement-of-entities-in-utterances)

참조 [문서는](https://aka.ms/luis-api-v3) V3에 사용할 수 있습니다.

## <a name="v3-changes-from-preview-to-ga"></a>미리 보기에서 GA로 V3 변경

V3는 GA로의 이동의 일부로 다음과 같은 변경 사항을 수행했습니다.

* 다음과 같은 미리 빌드된 엔터티에는 다른 JSON 응답이 있습니다.
    * [서수V1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [날짜 시간V2](luis-reference-prebuilt-datetimev2.md)
    * 측정 가능한 단위 키 `units` 이름부터`unit`

* 요청 본문 JSON 변경:
    * 에서 `preferExternalEntities``preferExternalEntities`
    * 외부 `score` 엔터티에 대한 선택적 매개 변수

* 응답 본문 JSON 변경 사항:
    * `normalizedQuery`제거

## <a name="suggested-adoption-strategy"></a>제안된 채택 전략

봇 프레임워크, Bing 맞춤법 검사 V7을 사용하거나 LUIS 앱 작성만 마이그레이션하려는 경우 V2 끝점을 계속 사용합니다.

클라이언트 응용 프로그램 또는 통합(봇 프레임워크 및 Bing 맞춤법 검사 V7)이 영향을 받지 않고 LUIS 앱 작성 및 예측 끝점을 동시에 마이그레이션할 수 있는 경우 V3 예측 끝점을 사용하기 시작합니다. V2 예측 끝점은 여전히 사용할 수 있으며 좋은 폴백 전략입니다.


## <a name="not-supported"></a>지원되지 않음

### <a name="bing-spell-check"></a>Bing 맞춤법 검사

이 API는 V3 예측 끝점에서 지원되지 않습니다 - 맞춤법 수정에 V2 API 예측 끝점을 계속 사용합니다. V3 API를 사용하는 동안 맞춤법 수정이 필요한 경우 클라이언트 응용 프로그램에서 [Bing 맞춤법 검사](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) API를 호출하고 LUIS API로 텍스트를 보내기 전에 텍스트를 올바른 맞춤법으로 변경하도록 합니다.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>봇 프레임워크 및 Azure 봇 서비스 클라이언트 응용 프로그램

봇 프레임워크의 V4.7이 릴리스될 때까지 V2 API 예측 끝점을 계속 사용합니다.

## <a name="v2-api-deprecation"></a>V2 API 사용 중단

V2 예측 API는 2020년 6월 8일 V3 미리 보기 이후 최소 9개월 동안 더 이상 사용되지 않습니다.

## <a name="endpoint-url-changes"></a>끝점 URL 변경

### <a name="changes-by-slot-name-and-version-name"></a>슬롯 이름 및 버전 이름별 변경 사항

V3 끝점 HTTP 호출의 형식이 변경되었습니다.

버전별로 쿼리하려면 먼저 `"directVersionPublish":true`을 사용하여 [API를 통해 게시해야](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) 합니다. 슬롯 이름 대신 버전 ID를 참조하는 끝점을 쿼리합니다.

|예측 API 버전|METHOD|URL|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v3.0</b>/앱/<b>{APP-ID}</b>/슬롯/<b>{SLOT-NAME}</b>/예측?쿼리=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v3.0</b>/앱/<b>{APP-ID}</b>/슬롯/<b>{SLOT-NAME}</b>/예측|
|V2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v3.0</b>/앱/<b>{APP-ID}</b>/버전/{버전-ID} /예측?쿼리=<b>{QUERY}</b> <b>{VERSION-ID}</b>|
|V2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v3.0</b>/앱/<b>{APP-ID}</b>/버전/<b>{VERSION-ID}</b>/예측|

|에 대한 유효한 값`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>변경 내용 요청

### <a name="query-string-changes"></a>쿼리 문자열 변경

V3 API에는 서로 다른 쿼리 문자열 매개 변수가 있습니다.

|매개 람 이름|Type|버전|기본값|목적|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|로그 파일에 쿼리를 저장합니다. 기본값은 False입니다.|
|`query`|문자열|V3만|기본값 없음 - GET 요청에 필요하지 않습니다.|**V2에서**예측할 발화는 매개 변수에 `q` 있습니다. <br><br>**V3에서**기능은 매개 변수에 `query` 전달됩니다.|
|`show-all-intents`|boolean|V3만|false|**prediction.intents** 개체에서 해당 점수로 모든 의도를 반환합니다. 의도는 상위 `intents` 개체의 개체로 반환됩니다. 이렇게 하면 배열에서 의도를 찾을 필요 없이 프로그래밍 `prediction.intents.give`방식으로 액세스할 수 있습니다. V2에서는 배열로 반환되었습니다. |
|`verbose`|boolean|V2 & V3|false|**V2에서**true로 설정하면 예측된 모든 의도가 반환됩니다. 모든 예측 된 의도가 필요한 경우 의 V3 매개 변수를 `show-all-intents`사용합니다.<br><br>**V3에서**이 매개 변수는 엔터티 예측의 엔터티 메타데이터 세부 정보만 제공합니다.  |
|`timezoneOffset`|문자열|V2|-|timetimeV2 엔터티에 적용되는 표준 시간대입니다.|
|`datetimeReference`|문자열|V3|-|timetimeV2 엔터티에 적용되는 [표준 시간대입니다.](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) V2에서 대체합니다. `timezoneOffset`|


### <a name="v3-post-body"></a>V3 포스트 바디

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

|속성|Type|버전|기본값|목적|
|--|--|--|--|--|
|`dynamicLists`|array|V3만|필요하지 않습니다.|[동적 목록을](#dynamic-lists-passed-in-at-prediction-time) 사용하면 LUIS 앱에서 이미 학습되고 게시된 기존 목록 엔터티를 확장할 수 있습니다.|
|`externalEntities`|array|V3만|필요하지 않습니다.|[외부 엔터티는](#external-entities-passed-in-at-prediction-time) LUIS 앱에 런타임 동안 엔터티를 식별하고 레이블을 지정하는 기능을 제공하므로 기존 엔터티의 기능으로 사용할 수 있습니다. |
|`options.datetimeReference`|문자열|V3만|기본값 없음|[datetimeV2 오프셋을](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)결정하는 데 사용됩니다. datetime참조의 형식은 [ISO 8601입니다.](https://en.wikipedia.org/wiki/ISO_8601)|
|`options.preferExternalEntities`|boolean|V3만|false|사용자의 [외부 엔터티(기존 엔터티와 이름이 같은)가](#override-existing-model-predictions) 사용중이거나 모델의 기존 엔터티가 예측에 사용되는지 지정합니다. |
|`query`|문자열|V3만|필수 사항입니다.|**V2에서**예측할 발화는 매개 변수에 `q` 있습니다. <br><br>**V3에서**기능은 매개 변수에 `query` 전달됩니다.|



## <a name="response-changes"></a>응답 변경

쿼리 응답 JSON이 가장 자주 사용되는 데이터에 대한 프로그래밍 방식으로 더 큰 액세스를 허용하도록 변경되었습니다.

### <a name="top-level-json-changes"></a>최상위 JSON 변경 사항



V2의 상위 JSON 속성은 `verbose` true로 설정된 경우 속성의 `intents` 모든 의도와 점수를 반환합니다.

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3에 대한 상위 JSON 속성은 다음과 같습니다.

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

개체는 `intents` 정렬되지 않은 목록입니다. 의 첫 번째 자식이 `intents` 에 해당한다고 가정하지 `topIntent`마십시오. 대신 값을 `topIntent` 사용하여 점수를 찾습니다.

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

응답 JSON 스키마 변경은 다음을 허용합니다.

* 원래 발언과 `query`반환된 예측 간의 `prediction`명확한 구분은 .
* 예측된 데이터에 대한 프로그래밍 방식의 액세스를 더 쉽게 할 수 있습니다. V2의 배열을 통해 열거하는 대신 의도 및 엔터티 모두에 대한 **이름으로** 값에 액세스할 수 있습니다. 예측된 엔터티 역할의 경우 전체 앱에서 고유하므로 역할 이름이 반환됩니다.
* 데이터 형식(결정된 경우)이 적용됩니다. 숫자는 더 이상 문자열로 반환되지 않습니다.
* 첫 번째 우선 순위 예측 정보와 `$instance` 개체에서 반환되는 추가 메타데이터 간의 구분입니다.

### <a name="entity-response-changes"></a>엔터티 응답 변경

#### <a name="marking-placement-of-entities-in-utterances"></a>발언에 엔터티 배치 표시

**V2에서**엔터티는 `startIndex` 및 `endIndex`의 발언으로 표시되었습니다.

**V3에서**엔터티는 및 `startIndex` `entityLength`로 표시됩니다.

#### <a name="access-instance-for-entity-metadata"></a>엔터티 `$instance` 메타데이터에 대한 액세스

엔터티 메타데이터가 필요한 경우 쿼리 문자열은 `verbose=true` 플래그를 사용해야 하며 응답에는 `$instance` 개체의 메타데이터가 포함됩니다. 예는 다음 섹션의 JSON 응답에 나와 있습니다.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>각 예측 엔터티는 배열로 표시됩니다.

각 `prediction.entities.<entity-name>` 엔터티가 발언에서 두 번 이상 예측될 수 있기 때문에 개체에 배열이 포함되어 있습니다.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>미리 빌드된 엔터티 변경

V3 응답 개체에는 미리 빌드된 엔터티에 대한 변경 내용이 포함됩니다. [미리 빌드된 특정 엔터티를](luis-reference-prebuilt-entities.md) 검토하여 자세히 알아봅니다.

#### <a name="list-entity-prediction-changes"></a>엔터티 예측 변경 목록

목록 엔터티 예측에 대한 JSON이 배열배열로 변경되었습니다.

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
각 내부 배열은 발언 내부의 텍스트에 해당합니다. 내부 개체는 목록 엔터티의 두 개 이상의 하위 목록에 동일한 텍스트가 나타날 수 있기 때문에 배열입니다.

`entities` 개체간에 `$instance` 개체를 매핑할 때 목록 엔터티 예측에 대해 개체 순서가 유지됩니다.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>엔터티 이름 대신 엔터티 역할 이름

V2에서 배열은 `entities` 엔터티 이름이 고유 식별자인 모든 예측 엔터티를 반환했습니다. V3에서 엔터티가 역할을 사용하고 예측이 엔터티 역할에 대한 경우 기본 식별자는 역할 이름입니다. 엔터티 역할 이름은 다른 모델(의도, 엔터티) 이름을 포함하여 전체 앱에서 고유해야 하기 때문에 가능합니다.

다음 예제에서는 텍스트를 포함하는 발언을 `Yellow Bird Lane`고려합니다. 이 텍스트는 사용자 `Location` 지정 엔터티의 `Destination`역할로 예측됩니다.

|발화 텍스트|엔터티 이름|역할 이름|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2에서 엔터티는 개체의 속성으로 역할을 가진 _엔터티 이름으로_ 식별됩니다.

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

V3에서 엔터티는 _엔터티 역할에_의해 참조됩니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3에서 엔터티 메타데이터를 `verbose` 반환하는 플래그가 있는 동일한 결과:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>예측 시간에 전달된 외부 엔터티

외부 엔터티는 LUIS 앱에 런타임 동안 엔터티를 식별하고 레이블을 지정하는 기능을 제공하므로 기존 엔터티의 기능으로 사용할 수 있습니다. 이렇게 하면 예측 끝점으로 쿼리를 보내기 전에 고유한 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이 작업은 쿼리 예측 끝점에서 수행되므로 모델을 다시 학습하고 게시할 필요가 없습니다.

클라이언트 응용 프로그램은 엔터티 일치를 관리하고 일치하는 엔터티의 utterance 내에서 위치를 결정한 다음 요청과 함께 해당 정보를 전송하여 자체 엔터티 추출기(entity extractor)를 제공합니다.

외부 엔터티는 역할, 복합 체 및 기타 와 같은 다른 모델에 대한 신호로 계속 사용되는 동안 엔터티 형식을 확장하는 메커니즘입니다.

이 기능은 쿼리 예측 런타임에서만 사용할 수 있는 데이터가 있는 엔터티에 유용합니다. 이러한 유형의 데이터의 예로는 지속적으로 데이터 또는 사용자당 특정 데이터가 변경됩니다. 사용자의 연락처 목록에서 외부 정보를 사용하여 LUIS 연락처 엔터티를 확장할 수 있습니다.

### <a name="entity-already-exists-in-app"></a>앱에 엔터티가 이미 있습니다.

끝점 `entityName` 요청 POST 본문에 전달된 외부 엔터티의 값은 요청이 이루어질 때 학습되고 게시된 앱에 이미 있어야 합니다. 엔터티 의 형식은 중요하지 않으며 모든 형식이 지원됩니다.

### <a name="first-turn-in-conversation"></a>대화의 첫 번째 차례

사용자가 다음과 같은 불완전한 정보를 입력하는 채팅 봇 대화에서 첫 번째 발언을 생각해 보십시오.

`Send Hazem a new message`

채팅 봇에서 LUIS로의 요청은 POST 본문에 `Hazem` 정보를 전달할 수 있으므로 사용자의 연락처 중 하나로 직접 일치합니다.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

예측 응답에는 요청에 정의되어 있으므로 다른 모든 예측 엔터티와 함께 외부 엔터티가 포함됩니다.

### <a name="second-turn-in-conversation"></a>대화의 두 번째 차례

채팅 봇에 대한 다음 사용자 발언은 더 모호한 용어를 사용합니다.

`Send him a calendar reminder for the party.`

이전 발언에서 발언은 `him` `Hazem`에 대한 참조로 사용합니다. POST 본문에 있는 대화식 채팅 봇은 첫 번째 발언에서 추출된 엔터티 값에 매핑할 `him` 수 `Hazem`있습니다.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

예측 응답에는 요청에 정의되어 있으므로 다른 모든 예측 엔터티와 함께 외부 엔터티가 포함됩니다.

### <a name="override-existing-model-predictions"></a>기존 모델 예측 재정의

옵션 `preferExternalEntities` 속성은 사용자가 이름이 같은 예측 엔터티와 겹치는 외부 엔터티를 보내는 경우 LUIS가 전달된 엔터티 또는 모델에 있는 엔터티를 선택하도록 지정합니다.

예를 들어 `today I'm free` 쿼리를 고려할 수 있습니다. LUIS는 `today` 다음과 같은 응답으로 datetimeV2로 검색합니다.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

사용자가 외부 엔터티를 보내는 경우:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

을 `preferExternalEntities` 로 `false`설정된 경우 LUIS는 외부 엔터티가 전송되지 않은 것처럼 응답을 반환합니다.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

IS로 `preferExternalEntities` `true`설정된 경우 LUIS는 다음을 포함한 응답을 반환합니다.

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>해결 방법

_선택적_ `resolution` 속성은 예측 응답에서 반환되므로 외부 엔터티와 연결된 메타데이터를 전달한 다음 응답에서 다시 수신할 수 있습니다.

기본 목적은 미리 빌드된 엔터티를 확장하는 것이지만 해당 엔터티 유형에만 국한되지 않습니다.

속성은 `resolution` 숫자, 문자열, 개체 또는 배열일 수 있습니다.

* "댈러스"
* {"텍스트": "값"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>예측 시간에 전달된 동적 목록

동적 목록을 사용하면 LUIS 앱에서 이미 학습되고 게시된 기존 목록 엔터티를 확장할 수 있습니다.

목록 엔터티 값을 주기적으로 변경해야 하는 경우 이 기능을 사용합니다. 이 기능을 사용하면 이미 학습되고 게시된 목록 엔터티를 확장할 수 있습니다.

* 쿼리 예측 끝점 요청시입니다.
* 단일 요청의 경우.

LUIS 앱에서 목록 엔터티가 비어 있을 수 있지만 존재해야 합니다. LUIS 앱의 목록 엔터티는 변경되지 않지만 끝점의 예측 기능이 확장되어 약 1,000개의 항목이 있는 최대 2개의 목록을 포함하도록 확장됩니다.

### <a name="dynamic-list-json-request-body"></a>동적 목록 JSON 요청 본문

다음 JSON 본문을 보내 목록에 동의어가 있는 새 하위 목록을 추가하고 쿼리 예측 요청을 `LUIS`사용하여 `POST` 텍스트의 목록 엔터티를 예측합니다.

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

예측 응답에는 요청에 정의되어 있으므로 다른 모든 예측 엔터티와 함께 해당 목록 엔터티가 포함됩니다.

## <a name="deprecation"></a>사용 중단

V3 미리 보기 후 최소 9개월 동안 V2 API는 더 이상 사용되지 않습니다.

## <a name="next-steps"></a>다음 단계

V3 API 설명서를 사용하여 기존 REST 호출을 LUIS [끝점](https://aka.ms/luis-api-v3) API로 업데이트합니다.

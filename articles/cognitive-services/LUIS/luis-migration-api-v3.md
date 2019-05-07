---
title: V2-V3 API 마이그레이션
titleSuffix: Azure Cognitive Services
description: 버전 3 끝점 Api 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 끝점 Api로 마이그레이션하는 방법을 알아야 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d0b5537f31edf330b54b91ddf7268338df0c0a6b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148268"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>미리 보기: API 버전으로 마이그레이션하기 LUIS 앱에 대 한 3.x

쿼리 예측 끝점 Api 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 끝점 Api로 마이그레이션하는 방법을 알아야 합니다. 

이 V3 API는 중요 한 JSON 요청 및/또는 응답에 변경 내용을 포함 하는 새로운 기능을 제공 합니다. 

* [외부 엔터티](#external-entities-passed-in-at-prediction-time)
* [동적 목록](#dynamic-lists-passed-in-at-prediction-time)
* [미리 작성 된 엔터티 JSON 변경](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

쿼리 예측 끝점 [요청](#request-changes) 하 고 [응답](#response-changes) 다음을 포함 하 여 위에 나열 된 새 기능을 지 원하는 중요 한 변경:

* [응답 개체 변경](#top-level-json-changes)
* [엔터티 이름 대신 엔터티 역할 이름 참조](#entity-role-name-instead-of-entity-name)
* [표현에서 엔터티를 표시 하는 속성](#marking-placement-of-entities-in-utterances)

다음 LUIS 기능은 **지원 되지 않습니다** V3 api에서:

* Bing Spell Check v7 알아보기

[참조 설명서](https://aka.ms/luis-api-v3) V3에서 사용할 수 있습니다.

## <a name="prebuilt-domains-with-new-models-and-language-coverage"></a>새 모델 및 언어 범위를 사용 하 여 미리 작성 된 도메인

검토 합니다 [V3 API 미리 작성 된 도메인 목록을](luis-reference-prebuilt-domains.md)합니다. 이러한 도메인은 모델 및 언어 범위 모두에서 완료 더 합니다. 

## <a name="prebuilt-entities-with-new-json"></a>새 JSON 사용 하 여 미리 작성 된 엔터티

V3 응답 개체 변경 내용을 포함 [미리 빌드된 엔터티로](luis-reference-prebuilt-entities.md)합니다. 

## <a name="request-changes"></a>변경 내용 요청 

### <a name="query-string-parameters"></a>쿼리 문자열 매개 변수

V3 API에 다양 한 쿼리 문자열 매개 변수가 있습니다.

|매개 변수 이름|Type|Version|목적|
|--|--|--|--|
|`query`|문자열|V3만|**V2에서**, 예측할 utterance 중인는 `q` 매개 변수입니다. <br><br>**V3에서**, 기능에 전달 되는 `query` 매개 변수입니다.|
|`show-all-intents`|부울|V3만|해당 점수를 사용 하 여 모든 의도 반환 합니다 **prediction.intents** 개체입니다. 인 텐트에서 부모 개체로 반환 되므로 `intents` 개체입니다. 배열에서 의도 찾는 하지 않고도 프로그래밍 방식으로 액세스할 수 있게: `prediction.intents.give`합니다. V2에서는 이러한 배열에서 반환 되었습니다. |
|`verbose`|부울|V2 & V3|**V2의**로 설정할 경우 true이 고, 모든 예측 의도 된 반환 합니다. 모든 예측된 의도 해야 하는 경우의 V3 매개 변수를 사용 하 여 `show-all-intents`입니다.<br><br>**V3에서**,이 매개 변수 제공 엔터티 엔터티 예측의 메타 데이터 정보입니다.  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>에 대 한 쿼리 예측 JSON 본문을 `POST` 요청

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

## <a name="response-changes"></a>응답 변경

쿼리 응답 JSON 가장 자주 사용 되는 데이터에 대 한 큰 프로그래밍 방식 액세스를 허용 하도록 변경 합니다. 

### <a name="top-level-json-changes"></a>상위 수준 JSON 변경

V2에 대 한 최상위 JSON 속성은 때 `verbose` 모든 의도 및에서 점수를 반환 하는 true로 설정 된 `intents` 속성:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3에 대 한 최상위 JSON 속성을 다음과 같습니다.

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` 개체가 순서 없는 목록입니다. 첫 번째 자식 가정 하지 마십시오 합니다 `intents` 에 해당 하는 `topIntent`합니다. 대신는 `topIntent` 점수를 찾을 값입니다.

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

응답 JSON 스키마 변경을 허용 합니다.

* 원래 utterance 구분 지우기 `query`, 예측을 반환 하 고 `prediction`입니다.
* 예측된 데이터에 쉽게 프로그래밍 방식으로 액세스 합니다. V2의 배열을 통해 열거 하는 대신 하 여 값에 액세스할 수 있습니다 **라는** 의도 및 엔터티 모두에 대 한 합니다. 예측된 엔터티 역할에 대 한 고유 하기 때문에 전체 응용 프로그램에서 역할 이름을 반환 됩니다.
* 데이터 형식은 결정 하는 경우 적용 됩니다. 숫자는 더 이상 문자열로 반환 됩니다.
* 반환 된 첫 번째 우선 순위 예측 정보 및 추가 메타 데이터 간의 차이점을 `$instance` 개체입니다. 

### <a name="access-instance-for-entity-metadata"></a>액세스 `$instance` 엔터티 메타 데이터에 대 한

쿼리 문자열을 사용 해야 엔터티 메타 데이터를 해야 하는 경우는 `verbose=true` 플래그 및 응답의 메타 데이터를 포함 합니다 `$instance` 개체입니다. 예제는 JSON 응답의 다음 섹션에 나와 있습니다.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>예측 된 각 엔터티는 배열로 표시 됩니다.

`prediction.entities.<entity-name>` 개체를 utterance에서 각 엔터티를 두 번 이상 예측할 배열이 포함 되어 있습니다. 

### <a name="list-entity-prediction-changes"></a>엔터티 예측 변경 목록

목록 엔터티 예측에 대 한 JSON 배열의 배열 되도록 변경 되었습니다.

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
각 내부 배열은 utterance 내의 텍스트에 해당합니다. 내부 개체 배열 이므로 동일한 텍스트 목록 엔터티의 둘 이상의 하위 목록에 나타날 수 있습니다. 

간에 매핑할 때 합니다 `entities` 개체는 `$instance` 개체 목록 엔터티 예측에 대 한 개체의 순서는 유지 합니다.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>엔터티 이름 대신 엔터티 역할 이름 

V2에서는 `entities` 배열이 엔터티 이름을 중인 고유 식별자를 사용 하 여 예측 된 모든 엔터티를 반환 합니다. V3에서 역할을 사용 하 여 엔터티를 엔터티 역할에 대 한 예측이 계열용 임을 경우 기본 식별자에는 역할 이름입니다. 다른 모델 (의도 entity) 이름을 포함 한 전체 응용 프로그램에서 엔터티 역할 이름은 고유 해야 하기 때문에 이것이 가능 합니다.

다음 예제에서:를 utterance 텍스트를 포함 하는 것이 좋습니다 `Yellow Bird Lane`합니다. 이 텍스트는 사용자 지정 예측 `Location` 엔터티의 역할 `Destination`입니다.

|Utterance 텍스트|엔터티 이름|역할 이름|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2에서는 엔터티도 식별 되는 _엔터티 이름_ 개체의 속성으로 역할을 사용 하 여:

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

V3에서 엔터티를 참조 합니다 _엔터티 역할_역할에 대 한 예측이 계열용 임을 하는 경우:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3을 사용 하 여 결과 동일 합니다 `verbose` 엔터티 메타 데이터를 반환 하는 플래그:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>예측 시 전달 되는 외부 엔터티

외부 엔터티는 LUIS 앱 식별 및 기존 엔터티의 기능으로 사용할 수 있는 런타임 동안 엔터티를 레이블 하는 기능을 제공 합니다. 이 옵션을 사용 하면 예측 끝점으로 쿼리를 보내기 전에 고유한 별도 및 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이 작업은 쿼리 예측 끝점에서 수행을 재 학습 및 모델 게시 필요가 없습니다.

클라이언트 응용 프로그램은 일치 하는 엔터티는 일치 하는 엔터티를 utterance 내 위치를 확인 하 고 요청을 사용 하 여 해당 정보를 보내는 관리 하 여 자체 엔터티 추출기를 제공 합니다. 

외부 엔터티는 역할, 복합 및 기타와 같은 다른 모델에 대 한 신호도 사용 되는 동안 모든 엔터티 형식 확장 하기 위한 메커니즘입니다.

엔터티를 쿼리 예측 런타임에만 사용할 수 있는 데이터에 유용 합니다. 이러한 종류의 데이터에 예가 사용자 당 또는 데이터에 지속적으로 변경 됩니다. 사용자의 연락처 목록에서 외부 정보를 사용 하 여 LUIS 연락처 엔터티를 확장할 수 있습니다. 

### <a name="entity-already-exists-in-app"></a>엔터티 앱에 이미 있습니다.

변수의 `entityName` 끝점 요청 POST 본문에에서 전달 하는 외부 엔터티를 이미 있어야 학습 되 고 게시 된 앱에서 요청 시에 대 한 합니다. 엔터티의 형식이 중요 하지 않습니다, 그리고 모든 형식이 지원 됩니다.

### <a name="first-turn-in-conversation"></a>대화의 첫 번째 설정

사용자가 다음 불완전 한 정보를 입력 하는 채팅 봇 대화의 첫 번째 utterance 고려해 야 합니다.

`Send Hazem a new message`

LUIS 채팅 봇의 요청에 대 한 POST 본문에 대 한 정보를 전달할 수 `Hazem` 문자열이 사용자의 연락처 중 하나로 직접 일치 하도록 합니다.

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

예측 응답은 요청에 정의 되어 있으므로 모든는 다른 엔터티와의 예측 하는 외부 엔터티를 포함 합니다.  

### <a name="second-turn-in-conversation"></a>두 번째 대화의 설정

채팅 봇에 다음 사용자 utterance 더 모호한 용어를 사용합니다.

`Send him a calendar reminder for the party.`

utterance 사용 된 이전 utterance `him` 에 대 한 참조로 `Hazem`합니다. 대화형 채팅 봇의 POST 본문에 매핑할 수 있습니다 `him` 는 첫 번째 utterance에서 추출 된 엔터티 값 `Hazem`합니다.

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

예측 응답은 요청에 정의 되어 있으므로 모든는 다른 엔터티와의 예측 하는 외부 엔터티를 포함 합니다.  

#### <a name="resolution"></a>해결 방법

합니다 _선택적_ `resolution` 외부 엔터티와 연결 된 메타 데이터를 전달 하 고 받을 수 있도록 예측 응답에서 속성 반환 응답에서 다시 합니다. 

주 목적은 미리 작성 된 엔터티를 확장 하는 것 하지만 해당 엔터티 형식에 제한 되지 않습니다. 

`resolution` 숫자, 문자열, 개체 또는 배열 속성 수:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]


## <a name="dynamic-lists-passed-in-at-prediction-time"></a>동적 목록은 예측 시에 전달합니다.

동적 목록을 통해 LUIS 앱에서 이미 기존 학습된 및 게시 된 엔터티를 확장할 수 있습니다. 

목록 엔터티 값을 주기적으로 변경 해야 하는 경우이 기능을 사용 합니다. 이 기능을 사용 하면 이미 학습된 및 게시 된 엔터티를 확장할 수 있습니다.

* 쿼리 예측 끝점 요청 시.
* 단일 요청 합니다.

목록 엔터티 LUIS 앱 비어 있을 수 있습니다 되었지만 존재 합니다. LUIS 앱에서 목록 엔터티가 변경 되지 않습니다 하지만 끝점에서 예측 기능 약 1,000 개 항목을 사용 하 여 최대 2 개의 그룹을 포함 하도록 확장 되었습니다.

### <a name="dynamic-list-json-request-body"></a>동적 목록 JSON 요청 본문

목록에 동의어를 사용 하 여 새 하위 목록을 추가 하려면 다음 JSON 본문에 보낼 및 텍스트에 대해 목록 엔터티 예측할 `LUIS`를 사용 하 여는 `POST` 쿼리 예측 요청:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

예측 응답은 요청에 정의 되어 있으므로 모든는 다른 엔터티와의 예측 하는 목록 엔터티를 포함 합니다. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset datetimeReference로 변경

**V2에서**서 `timezoneOffset` [매개 변수](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) 요청은 GET 또는 POST 요청으로 전송 하는 경우와 상관 없이 쿼리 문자열 매개 변수를 예측 요청에 전송 됩니다. 

**V3에서**, POST 본문 매개 변수를 사용 하 여 동일한 기능을 제공 하는 `datetimeReference`합니다. 

## <a name="marking-placement-of-entities-in-utterances"></a>길이 발언의 엔터티 배치를 표시합니다.

**V2의**를 사용 하 여를 utterance에 표시 된 엔터티는 `startIndex` 및 `endIndex`. 

**V3에서**, 엔터티 표시 되어 `startIndex` 및 `entityLength`합니다.


## <a name="next-steps"></a>다음 단계

사용 하 여 LUIS를 호출 하는 기존 나머지 부분을 업데이트 하려면 V3 API 설명서 [끝점](https://aka.ms/luis-api-v3) Api. 
---
title: 런타임시 앱 확장 - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538655"
---
# <a name="extend-app-at-prediction-runtime"></a>예측 런타임시 앱 확장

앱의 스키마(모델 및 기능)가 학습되어 예측 끝점에 게시됩니다. 이 게시된 모델은 예측 런타임에 사용됩니다. 사용자의 발언과 함께 새 정보를 예측 런타임에 전달하여 예측을 보강할 수 있습니다.

두 가지 예측 런타임 스키마 변경 사항은 다음과 같습니다.
* [외부 엔터티](#external-entities)
* [동적 목록](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>외부 엔터티

외부 엔터티는 LUIS 앱에 런타임 동안 엔터티를 식별하고 레이블을 지정하는 기능을 제공하므로 기존 엔터티의 기능으로 사용할 수 있습니다. 이렇게 하면 예측 끝점으로 쿼리를 보내기 전에 고유한 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이 작업은 쿼리 예측 끝점에서 수행되므로 모델을 다시 학습하고 게시할 필요가 없습니다.

클라이언트 응용 프로그램은 엔터티 일치를 관리하고 일치하는 엔터티의 utterance 내에서 위치를 결정한 다음 요청과 함께 해당 정보를 전송하여 자체 엔터티 추출기(entity extractor)를 제공합니다.

외부 엔티티는 다른 모델에 대한 신호로 계속 사용되는 동안 모든 엔터티 형식을 확장하는 메커니즘입니다.

이 기능은 쿼리 예측 런타임에서만 사용할 수 있는 데이터가 있는 엔터티에 유용합니다. 이러한 유형의 데이터의 예로는 지속적으로 데이터 또는 사용자당 특정 데이터가 변경됩니다. 사용자의 연락처 목록에서 외부 정보를 사용하여 LUIS 연락처 엔터티를 확장할 수 있습니다.

외부 엔터티는 V3 작성 API의 일부입니다. 이 버전으로 [마이그레이션하는](luis-migration-api-v3.md) 방법에 대해 자세히 알아보세요.

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

이 대화 회전에서 발언은 `him` `Hazem`에 대한 참조로 사용합니다. POST 본문에 있는 대화식 채팅 봇은 첫 번째 발언에서 추출된 엔터티 값에 매핑할 `him` 수 `Hazem`있습니다.

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

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>동적 목록

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

## <a name="next-steps"></a>다음 단계

* [예측 점수](luis-concept-prediction-score.md)
* [API V3 변경 사항 작성](luis-migration-api-v3.md)

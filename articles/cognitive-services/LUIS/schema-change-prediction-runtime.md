---
title: 런타임에 앱 확장 - LUIS
description: 이미 게시된 예측 엔드포인트를 확장하여 새 정보를 전달하는 방법을 알아봅니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953024"
---
# <a name="extend-app-at-prediction-runtime"></a>예측 런타임에 앱 확장

앱의 스키마(모델 및 기능)가 학습되고 예측 엔드포인트에 게시됩니다. 게시된 이 모델은 예측 런타임에서 사용됩니다. 사용자의 발화와 함께 새 정보를 예측 런타임에 전달하여 예측을 강화할 수 있습니다.

다음 두 가지 예측 런타임 스키마 변경이 있습니다.
* [외부 엔터티](#external-entities)
* [동적 목록](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>외부 엔터티

외부 엔터티를 통해 LUIS 앱은 런타임 중에 엔터티를 식별하고 레이블을 지정하는 기능을 기존 엔터티에 대한 기능으로 사용할 수 있습니다. 이렇게 하면 예측 엔드포인트에 쿼리를 보내기 전에 별도의 고유한 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이 작업은 쿼리 예측 엔드포인트에서 수행되므로 모델을 다시 학습하고 게시할 필요가 없습니다.

클라이언트 애플리케이션은 엔터티 일치를 관리하고 일치하는 엔터티의 발화 내에서 위치를 확인한 다음 요청을 통해 해당 정보를 전송하여 자체 엔터티 추출기를 제공합니다.

외부 엔터티는 다른 모델에 대한 신호로 계속 사용되는 동안 엔터티 형식을 확장하는 메커니즘입니다.

이는 쿼리 예측 런타임에서만 데이터를 사용할 수 있는 엔터티에 유용합니다. 이러한 데이터 형식의 예는 지속적으로 데이터를 변경하거나 사용자별로 지정합니다. 사용자의 연락처 목록에서 외부 정보를 사용하여 LUIS 연락처 엔터티를 확장할 수 있습니다.

외부 엔터티는 V3 작성 API의 일부입니다. 이 버전으로 [마이그레이션](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

### <a name="entity-already-exists-in-app"></a>엔터티가 앱에 이미 있음

엔드포인트 요청 POST 본문에 전달되는 외부 엔터티의 `entityName` 값은 요청 시 학습된 앱 및 게시된 앱에 이미 있어야 합니다. 엔터티 형식은 중요하지 않습니다. 모든 형식이 지원됩니다.

### <a name="first-turn-in-conversation"></a>대화의 첫 번째 턴

사용자가 다음과 같은 불완전한 정보를 입력하는 챗봇 대화에서 첫 번째 발화를 고려합니다.

`Send Hazem a new message`

챗봇에서 LUIS로의 요청은 `Hazem`에 대한 POST 본문의 정보를 전달하여 사용자의 연락처 중 하나와 직접 일치하도록 할 수 있습니다.

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

예측 응답은 요청에 정의되어 있기 때문에 다른 모든 예측된 엔터티와 함께 해당 외부 엔터티를 포함합니다.

### <a name="second-turn-in-conversation"></a>대화의 두 번째 턴

챗봇에 대한 다음 사용자 발화는 더 모호한 용어를 사용합니다.

`Send him a calendar reminder for the party.`

이 대화 턴에서 발화는 `him`을 `Hazem`에 대한 참조로 사용합니다. 대화형 챗봇은 POST 본문에서 첫 번째 발화에서 추출된 엔터티 값 `Hazem`에 `him`을 매핑할 수 있습니다.

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

예측 응답은 요청에 정의되어 있기 때문에 다른 모든 예측된 엔터티와 함께 해당 외부 엔터티를 포함합니다.

### <a name="override-existing-model-predictions"></a>기존 모델 예측 재정의

`preferExternalEntities` 옵션 속성은 사용자가 동일한 이름의 예측된 엔터티와 겹치는 외부 엔터티를 보내는 경우 LUIS에서 전달된 엔터티 또는 모델에 있는 기존 엔터티를 선택하도록 지정합니다.

예를 들어 `today I'm free` 쿼리를 고려할 수 있습니다. LUIS는 다음 응답에서 `today`를 datetimeV2로 검색합니다.

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

사용자가 외부 엔터티를 전송하는 경우:

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

`preferExternalEntities`가 `false`로 설정된 경우 LUIS는 외부 엔터티가 전송되지 않은 것처럼 응답을 반환합니다.

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

`preferExternalEntities`가 `true`로 설정된 경우 LUIS는 다음과 같은 응답을 반환합니다.

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>해결 방법

_선택적_ `resolution` 속성은 예측 응답에서 반환하여 외부 엔터티와 연결된 메타데이터를 전달한 다음 응답에서 다시 받을 수 있도록 합니다.

기본 목적은 미리 빌드된 엔터티를 확장하는 것이지만 해당 엔터티 형식으로 제한되지 않습니다.

`resolution` 속성은 숫자, 문자열, 개체 또는 배열일 수 있습니다.

* "댈러스"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>동적 목록

동적 목록을 사용하면 이미 LUIS 앱에 있는 기존의 학습 및 게시된 목록 엔터티를 확장할 수 있습니다.

목록 엔터티 값을 주기적으로 변경해야 하는 경우 이 기능을 사용합니다. 다음의 경우 이 기능을 사용하여 이미 학습되고 게시된 목록 엔터티를 확장할 수 있습니다.

* 쿼리 예측 엔드포인트 요청 시
* 단일 요청의 경우

LUIS 앱에서 목록 엔터티는 비어 있을 수 있지만 존재해야 합니다. LUIS 앱의 목록 엔터티는 변경되지 않지만 엔드포인트의 예측 기능은 약 1,000개 항목이 포함된 최대 2개의 목록을 포함하도록 확장됩니다.

### <a name="dynamic-list-json-request-body"></a>동적 목록 JSON 요청 본문

다음 JSON 본문으로 보내서 동의어를 사용하여 새 하위 목록을 목록에 추가하고 `POST` 쿼리 예측 요청을 사용하여 텍스트에 `LUIS`에 대한 목록 엔터티를 예측할 수 있습니다.

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

예측 응답은 요청에 정의되어 있기 때문에 다른 모든 예측된 엔터티와 함께 해당 목록 엔터티를 포함합니다.

## <a name="next-steps"></a>다음 단계

* [예측 점수](luis-concept-prediction-score.md)
* [작성 API V3 변경](luis-migration-api-v3.md)

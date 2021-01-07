---
title: 런타임에 앱 확장-LUIS
description: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 69e2608fb01ece81f555aae2f3d4a2e4a05cfc90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322804"
---
# <a name="extend-app-at-prediction-runtime"></a>예측 런타임에서 앱 확장

앱의 스키마 (모델 및 기능)를 학습 하 고 예측 끝점에 게시 합니다. 이 게시 된 모델은 예측 런타임에 사용 됩니다. 사용자의 utterance와 함께 새 정보를 예측 런타임에 전달 하 여 예측을 강화할 수 있습니다.

다음 두 가지 예측 런타임 스키마 변경이 있습니다.
* [외부 엔터티](#external-entities)
* [동적 목록](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>외부 엔터티

외부 엔터티를 통해 LUIS 앱은 런타임 중에 엔터티를 식별 하 고 레이블을 지정 하는 기능을 기존 엔터티에 대 한 기능으로 사용할 수 있습니다. 이렇게 하면 예측 끝점에 쿼리를 보내기 전에 별도의 고유한 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이는 쿼리 예측 끝점에서 수행 되므로 모델을 다시 학습 하 고 게시할 필요가 없습니다.

클라이언트 응용 프로그램은 엔터티 일치를 관리 하 고 일치 하는 엔터티의 utterance 내에서 위치를 확인 한 다음 요청을 통해 해당 정보를 전송 하 여 자체 엔터티 추출기를 제공 합니다.

외부 엔터티는 다른 모델에 대 한 신호로 계속 사용 되는 동안 엔터티 형식을 확장 하는 메커니즘입니다.

이는 쿼리 예측 런타임에만 데이터를 사용할 수 있는 엔터티에 유용 합니다. 이러한 데이터 형식의 예는 지속적으로 데이터를 변경 하거나 사용자별로 특정 합니다. 사용자의 연락처 목록에서 외부 정보를 사용 하 여 LUIS contact 엔터티를 확장할 수 있습니다.

외부 엔터티는 V3 authoring API의 일부입니다. 이 버전으로 [마이그레이션하](luis-migration-api-v3.md) 는 방법에 대해 자세히 알아보세요.

### <a name="entity-already-exists-in-app"></a>엔터티가 앱에 이미 있습니다.

`entityName`끝점 요청 게시 본문에 전달 되는 외부 엔터티의 값은 요청 시 학습 된 앱 및 게시 된 앱에 이미 있어야 합니다. 엔터티 형식이 중요 하지 않습니다. 모든 형식이 지원 됩니다.

### <a name="first-turn-in-conversation"></a>대화를 먼저 전환

사용자가 다음과 같은 불완전 한 정보를 입력 하는 chat bot 대화의 첫 번째 utterance을 고려 합니다.

`Send Hazem a new message`

채팅 봇에서 LUIS로의 요청은 게시 본문의 정보를 전달 하 여 `Hazem` 사용자의 연락처 중 하 나와 직접 일치 하도록 할 수 있습니다.

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

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 외부 엔터티를 포함 합니다.

### <a name="second-turn-in-conversation"></a>대화의 두 번째 설정

채팅 봇에 utterance 하는 다음 사용자는 더 모호한 용어를 사용 합니다.

`Send him a calendar reminder for the party.`

이 대화의 utterance에서는 `him` 에 대 한 참조로를 사용 합니다 `Hazem` . 게시물 본문의 대화형 채팅 봇은 `him` 첫 번째 utterance에서 추출 된 엔터티 값에 매핑될 수 있습니다 `Hazem` .

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

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 외부 엔터티를 포함 합니다.

### <a name="override-existing-model-predictions"></a>기존 모델 예측 재정의

`preferExternalEntities`Options 속성은 사용자가 동일한 이름의 예측 된 엔터티와 겹치는 외부 엔터티를 보내는 경우 LUIS에서 전달 된 엔터티 또는 모델에 있는 기존 엔터티를 선택 하도록 지정 합니다.

예를 들어 `today I'm free` 쿼리를 고려할 수 있습니다. LUIS `today` 는 다음 응답으로 datetimeV2로 검색 됩니다.

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

사용자가 외부 엔터티를 전송 하는 경우:

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

`preferExternalEntities`가로 설정 된 경우 `false` LUIS는 외부 엔터티가 전송 되지 않은 것 처럼 응답을 반환 합니다.

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

`preferExternalEntities`가로 설정 된 경우 `true` LUIS는 다음과 같은 응답을 반환 합니다.

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>해결 방법

_선택적_ `resolution` 속성은 예측 응답에서를 반환 하 여 외부 엔터티와 연결 된 메타 데이터를 전달한 다음 응답에서 다시 받을 수 있도록 합니다.

기본 목적은 미리 작성 된 엔터티를 확장 하는 것 이지만 해당 엔터티 형식으로 제한 되지 않습니다.

`resolution`속성은 숫자, 문자열, 개체 또는 배열일 수 있습니다.

* 달라스
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>동적 목록

동적 목록을 사용 하면 이미 LUIS 앱에 있는 기존의 학습 및 게시 된 목록 엔터티를 확장할 수 있습니다.

목록 엔터티 값을 주기적으로 변경 해야 하는 경우이 기능을 사용 합니다. 이 기능을 사용 하 여 이미 학습 되 고 게시 된 목록 엔터티를 확장할 수 있습니다.

* 쿼리 예측 끝점 요청 시
* 단일 요청에 대 한입니다.

LUIS 앱에서 목록 엔터티는 비어 있을 수 있지만 존재 해야 합니다. LUIS 앱의 목록 엔터티는 변경 되지 않지만 끝점의 예측 기능은 약 1000 항목을 포함 하는 최대 2 개의 목록을 포함 하도록 확장 됩니다.

### <a name="dynamic-list-json-request-body"></a>동적 목록 JSON 요청 본문

다음 JSON 본문으로 보내서 동의어를 사용 하 여 새 하위 목록을 목록에 추가 하 고 쿼리 예측 요청을 사용 하 여 텍스트에 대 한 목록 엔터티를 예측할 수 `LUIS` `POST` 있습니다.

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

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 목록 엔터티를 포함 합니다.

## <a name="next-steps"></a>다음 단계

* [예측 점수](luis-concept-prediction-score.md)
* [API V3 변경 내용 작성](luis-migration-api-v3.md)

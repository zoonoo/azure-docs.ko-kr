---
title: 복합 엔터티 형식
titleSuffix: Language Understanding - Azure Cognitive Services
description: 복합 엔터티는 미리 작성 된 엔터티, 단순, 정규식, 목록 엔터티 등의 다른 엔터티로 구성 됩니다. 개별 엔터티가 전체 엔터티를 형성합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 55dcc5666e63b8a87ddcb13696991fe02843fbbd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480300"
---
# <a name="composite-entity"></a>복합 엔터티 

복합 엔터티는 미리 작성 된 엔터티, 단순, 정규식, 목록 엔터티 등의 다른 엔터티로 구성 됩니다. 개별 엔터티가 전체 엔터티를 형성합니다. 

**이 엔터티는 데이터에 적합 합니다.**

* 서로 관련이 있습니다. 
* 발언의 컨텍스트에서 서로 관련되어 있습니다.
* 다양한 엔터티 형식을 사용합니다.
* 클라이언트 애플리케이션에서 정보 단위로 그룹화되고 처리되어야 합니다.
* 기계 학습을 요구하는 다양한 사용자 발언이 포함됩니다.

![복합 엔터티](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>예제 JSON

다음 utterance을 `Location::ToLocation` 사용 하 여 `number` 미리 작성 된의 복합 엔터티를 고려 합니다.

`book 2 tickets to paris`

`2`(number)와 `paris`(ToLocation) 사이에 엔터티에 속하지 않는 단어가 있음을 알 수 있습니다. [LUIS](luis-reference-regions.md) 웹 사이트의 레이블이 지정된 발언에서 사용된 녹색 밑줄은 복합 엔터티를 나타냅니다.

![복합 엔터티](./media/luis-concept-data-extraction/composite-entity.png)

복합 엔터티는 `compositeEntities` 배열로 반환되고 복합 내의 모든 엔터티도 `entities` 배열로 반환됩니다.

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|데이터 개체|엔터티 이름|값|
|--|--|--|
|미리 빌드된 엔터티 - number|“builtin.number”|“2”|
|미리 작성 한 엔터티-GeographyV2|“Location::ToLocation”|“paris”|

## <a name="next-steps"></a>다음 단계

이 [자습서](luis-tutorial-composite-entity.md)에서는 **복합 엔터티** 를 추가 하 여 다양 한 형식의 추출 된 데이터를 포함 하는 단일 엔터티에 추가 합니다. 데이터를 묶어서 클라이언트 애플리케이션이 다른 데이터 형식의 관련된 데이터를 쉽게 추출할 수 있습니다.

---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: acf3968510bc45838f26c4b3cf366abdee06f298
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655534"
---
예제 발화는 특정 형식을 따릅니다.

`text` 필드에는 예시 발언 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱의 기존 의도 이름과 일치해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티에 레이블을 지정하지 않으려면 빈 배열을 제공하세요.

entityLabels 배열이 비어 있지 않으면 `startCharIndex` 및 `endCharIndex`에서 `entityName` 필드에 언급된 엔터티를 표시해야 합니다. 인덱스는 0부터 시작합니다. 텍스트의 공간에서 레이블을 시작하거나 종료하는 경우 발화를 추가하기 위한 API 호출이 실패합니다.

```JSON
[
    {
        "text": "order a cheese pizza",
        "intentName": "ModifyOrder",
        "entityLabels":[]
    },
    {
        "text": "order a large pepperoni pizza",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 8,
                "endCharIndex": 28
            }
        ]
    },
    {
        "text": "order 2 large pepperoni pizzas on thin crust",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entityName": "FullPizzaWithModifiers",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entity": "Quantity",
                "startPos": 6,
                "endPos": 7
            },
            {
                "entity": "PizzaType",
                "startPos": 14,
                "endPos": 22
            },
            {
                "entity": "Size",
                "startPos": 8,
                "endPos": 12
            },
            {
                "entity": "Crust",
                "startPos": 34,
                "endPos": 37
            }
        ]
    }
]
```

---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500705"
---
예제 발화는 특정 형식을 따릅니다. 

`text` 필드에는 예시 발언 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱의 기존 의도 이름과 일치해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티에 레이블을 지정하지 않으려면 빈 배열을 제공하세요.

entityLabels 배열이 비어 있지 않으면 `startCharIndex` 및 `endCharIndex`에서 `entityName` 필드에 언급된 엔터티를 표시해야 합니다. 인덱스는 0부터 시작하며, 맨 위 예에서 6은 대문자 S 앞 공백이 아니라 Seattle의 "S"를 참조합니다. 텍스트에서 라벨 앞뒤에 공백을 붙이면 발언을 추가하는 API가 실패합니다.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```

---
title: PersonName 미리 빌드된 엔터티
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding(LUIS)의 personName 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140237"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 PersonName 미리 빌드된 엔터티
미리 빌드된 personName 엔터티는 사용자 이름을 검색합니다. 이 엔터티를 이미 학습했기 때문에 personName을 포함하는 예제 발화를 응용 프로그램 의도에 추가할 필요가 없습니다. personName 엔터티는 영어 및 중국어 [문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="resolution-for-personname-entity"></a>personName 엔터티의 해결
다음 예제에서는 **builtin.personName** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다. 
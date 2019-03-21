---
title: Keyphrase 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 keyPhrase 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338317"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 keyPhrase 미리 빌드된 엔터티
keyPhrase는 발언에서 다양한 핵심 구를 추출합니다. keyphrase를 포함하는 예제 발언을 애플리케이션에 추가할 필요는 없습니다. keyPhrase 엔터티는 [텍스트 분석](../text-analytics/overview.md) 기능의 일부로 [여러 문화권](luis-language-support.md#languages-supported)에서 지원됩니다. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>미리 빌드된 keyPhrase 엔터티의 해결
다음 예제에서는 **builtin.keyPhrase** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [age](luis-reference-prebuilt-age.md) 엔터티에 대해 알아봅니다.

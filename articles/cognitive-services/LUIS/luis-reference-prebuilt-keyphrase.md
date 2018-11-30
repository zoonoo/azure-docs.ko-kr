---
title: LUIS 미리 빌드된 엔터티 keyPhrase 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 keyPhrase 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a543e60c6e77ed9fdb825ad6cb2a936119677671
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334587"
---
# <a name="keyphrase-entity"></a>keyPhrase 엔터티
keyPhrase는 발언에서 다양한 핵심 구를 추출합니다. keyphrase를 포함하는 예제 발언을 응용 프로그램에 추가할 필요는 없습니다. keyPhrase 엔터티는 [텍스트 분석](../text-analytics/overview.md) 기능의 일부로 [여러 문화권](luis-language-support.md#languages-supported)에서 지원됩니다. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>미리 빌드된 keyPhrase 엔터티의 해결
다음 예제에서는 **builtin.keyPhrase** 엔터티의 해결을 보여 줍니다.

```JSON
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

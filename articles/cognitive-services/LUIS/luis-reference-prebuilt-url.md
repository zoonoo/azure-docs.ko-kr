---
title: URL 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 URL 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3e5f6edf213838345c21598213b4fc2065e66335
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884140"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 URL 미리 빌드된 엔터티
URL 엔터티는 도메인 이름 또는 IP 주소를 사용하여 URL을 추출합니다. 이 엔터티를 이미 학습했기 때문에 URL을 애플리케이션에 포함하는 예제 발언을 추가할 필요가 없습니다. URL 엔터티는 `en-us` 문화권에서만 지원됩니다. 

## <a name="types-of-urls"></a>URL 유형
URL은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-url-entity"></a>미리 빌드된 URL 엔터티의 해결
다음 예제에서는 **builtin.url** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다.

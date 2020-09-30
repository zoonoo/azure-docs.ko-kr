---
title: 감정 분석 - LUIS
titleSuffix: Azure Cognitive Services
description: 감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 7c3c0f603b9e7d83e40f43ab00d31f4ca63f2b4f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535375"
---
# <a name="sentiment-analysis"></a>감정 분석
감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다. [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) 설명서에서 감정 분석에 대해 자세히 알아봅니다.

LUIS는 Text Analytics V2를 사용 합니다. 

## <a name="resolution-for-sentiment"></a>감정에 대 한 해결 방법

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

#### <a name="english-language"></a>[영어](#tab/english)

문화권이 `en-us`인 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[기타 언어](#tab/other-languages)

다른 모든 문화권의 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.


---
title: 단순 엔터티 형식
titleSuffix: Language Understanding - Azure Cognitive Services
description: 단순 엔터티는 단일 개념을 설명하고 기계 학습 컨텍스트에서 학습되는 일반 엔터티입니다. 단순한 엔터티는 일반적으로 회사 이름, 제품 이름 또는 다른 이름 범주와 같은 이름을 사용 하므로 간단한 엔터티를 사용 하 여 사용 되는 이름의 신호를 상승 시킬 때 구 목록을 추가 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 3f03b33f685bb5c7c9ba8f2267b8556c5dadade4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480312"
---
# <a name="simple-entity"></a>단순 엔터티 

단순 엔터티는 단일 개념을 설명하고 기계 학습 컨텍스트에서 학습되는 일반 엔터티입니다. 단준 엔터티 이름이 일반적으로 회사 이름, 제품 이름 또는 이름의 기타 범주와 같은 이름이기 때문에 사용된 이름의 신호를 향상시키기 위해 간단한 엔터티를 사용하는 경우 [구 목록](luis-concept-feature.md)을 추가합니다. 

**엔터티는 다음과 같은 경우에 적합 합니다.**

* 데이터는 일관되게 형식이 지정되지 않고 동일한 작업을 나타냅니다. 

![단순 엔터티](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>예제 JSON

`Bob Jones wants 3 meatball pho`

이전 발화에서 `Bob Jones`는 단순 `Customer` 엔터티로 레이블이 지정됩니다.

엔드포인트에서 반환된 데이터에는 엔터티 이름, 발화에서 검색된 텍스트, 검색된 텍스트의 위치 및 점수가 포함됩니다.

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|데이터 개체|엔터티 이름|값|
|--|--|--|
|단순 엔터티|`Customer`|`bob jones`|

## <a name="next-steps"></a>다음 단계

이 [자습서](luis-quickstart-primary-and-secondary-data.md)에서는 **간단한 엔터티**를 사용 하 여 utterance에서 고용 작업 이름에 대 한 컴퓨터의 학습 된 데이터를 추출 합니다. 추출 정확도를 높이려면 간단한 엔터티와 관련 된 용어 [목록 문구](luis-concept-feature.md) 를 추가 합니다.
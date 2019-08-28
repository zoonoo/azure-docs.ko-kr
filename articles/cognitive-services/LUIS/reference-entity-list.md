---
title: 엔터티 형식 나열-LUIS
titleSuffix: Azure Cognitive Services
description: 목록 엔터티는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. 권장 기능을 사용 하 여 현재 목록을 기반으로 하는 새 단어에 대 한 제안을 볼 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ca9f8b570ee28b1913c8ec81c66a5b70827c04d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559963"
---
# <a name="list-entity"></a>목록 엔터티 

목록 엔터티는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다. 

목록 엔터티가 컴퓨터에서 학습 되지 않았습니다. 정확히 일치하는 텍스트입니다. LUIS는 모든 목록의 항목과 일치하는 항목을 응답의 엔터티로 표시합니다. 

**엔터티는 텍스트 데이터의 경우에 적합 합니다.**

* 알려진 세트입니다.
* 자주 변경 되지 않습니다. 목록을 자주 변경 하거나 목록을 자체 확장 하려는 경우에는 문구 목록과 함께 간단한 엔터티를 사용 하는 것이 더 좋습니다. 
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. LUIS는 정확한 텍스트 일치를 벗어나는 목록을 사용하지 않습니다. 유사 항목 일치, 대/소문자 구분 안 함, 형태소 분석, 복수 및 기타 변형은 목록 엔터티로 확인 되지 않습니다. 변형을 관리하려면 선택적인 텍스트 구문이 포함된 [패턴](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)을 사용하는 것이 좋습니다.

![목록 엔터티](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>예제 JSON

앱에 공항(Sea-tac), 공항 코드(SEA), 우편 번호(98101) 및 전화 지역 코드(206)를 사용하는 도시를 포함하여 도시 이름의 변형을 허용하는 `Cities` 목록이 있다고 가정합니다.

|목록 항목|항목 동의어|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

이전 발화에서 `paris` 단어는 `Cities` 목록 엔터티의 일부로 paris 항목에 매핑됩니다. 목록 엔터티는 항목 동의어뿐 아니라 항목의 정규화된 이름과도 일치합니다.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

파리의 동의어를 사용하는 다른 예제 발화:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|데이터 개체|엔터티 이름|값|
|--|--|--|
|단순 엔터티|`Customer`|`bob jones`|

## <a name="next-steps"></a>다음 단계

이 [자습서](luis-quickstart-intent-and-list-entity.md)에서는 **목록 엔터티** 를 사용 하 여 알려진 항목 목록에서 정확히 일치 하는 텍스트를 추출 하는 방법에 대해 알아봅니다. 

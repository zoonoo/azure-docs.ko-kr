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
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 1307e6cfca0debe7623eb775c69527a74584033d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011981"
---
# <a name="list-entity"></a>목록 엔터티 

목록 엔터티는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다. 

목록 엔터티가 컴퓨터에서 학습 되지 않았습니다. 정확히 일치하는 텍스트입니다. LUIS는 모든 목록의 항목과 일치하는 항목을 응답의 엔터티로 표시합니다. 

**엔터티는 텍스트 데이터의 경우에 적합 합니다.**

* 알려진 세트입니다.
* 자주 변경 되지 않습니다. 목록을 자주 변경 하거나 목록을 자체 확장 하려는 경우에는 문구 목록과 함께 간단한 엔터티를 사용 하는 것이 더 좋습니다. 
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. LUIS는 정확한 텍스트 일치를 벗어나는 목록을 사용하지 않습니다. 유사 항목 일치, 대/소문자 구분 안 함, 형태소 분석, 복수 및 기타 변형은 목록 엔터티로 확인 되지 않습니다. 변형을 관리하려면 선택적인 텍스트 구문이 포함된 [패턴](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)을 사용하는 것이 좋습니다.

![목록 엔터티](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>예제. json을 목록 엔터티로 가져오기

  다음 json 형식을 사용 하 여 기존 목록 엔터티로 값을 가져올 수 있습니다.

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]  
  ```

## <a name="example-json-response"></a>예제 JSON 응답

앱에 공항(Sea-tac), 공항 코드(SEA), 우편 번호(98101) 및 전화 지역 코드(206)를 사용하는 도시를 포함하여 도시 이름의 변형을 허용하는 `Cities` 목록이 있다고 가정합니다.

|목록 항목|항목 동의어|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

이전 발화에서 `paris` 단어는 `Cities` 목록 엔터티의 일부로 paris 항목에 매핑됩니다. 목록 엔터티는 항목 동의어뿐 아니라 항목의 정규화된 이름과도 일치합니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)


쿼리 문자열에 `verbose=false` 설정 된 경우이 JSON은 다음과 같습니다.

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

쿼리 문자열에 `verbose=true` 설정 된 경우이 JSON은 다음과 같습니다.

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|데이터 개체|엔터티 이름|값|
|--|--|--|
|엔터티 나열|`Cities`|`paris`|


## <a name="next-steps"></a>다음 단계

이 [자습서](luis-quickstart-intent-and-list-entity.md)에서는 **목록 엔터티** 를 사용 하 여 알려진 항목 목록에서 정확히 일치 하는 텍스트를 추출 하는 방법에 대해 알아봅니다. 

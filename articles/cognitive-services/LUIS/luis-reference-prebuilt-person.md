---
title: PersonName 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding(LUIS)의 personName 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535426"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 PersonName 미리 빌드된 엔터티
미리 빌드된 personName 엔터티는 사용자 이름을 검색합니다. 이 엔터티를 이미 학습했기 때문에 personName을 포함하는 예제 발화를 애플리케이션 의도에 추가할 필요가 없습니다. personName 엔터티는 영어 및 중국어 [문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="resolution-for-personname-entity"></a>personName 엔터티의 해결

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 응답](#tab/V3)


다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `false` .

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `true` .

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.personName** 엔터티의 해결을 보여 줍니다.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다.

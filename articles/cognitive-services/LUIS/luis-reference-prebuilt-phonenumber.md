---
title: Phone number 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 phone number 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6b6474375b0f62f80903feb4eeee47f03016786b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208244"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Phonenumber 미리 빌드된 엔터티
`phonenumber` 엔터티는 국가 코드를 포함하는 다양한 전화 번호를 추출합니다. 이 엔터티를 이미 학습했기 때문에 애플리케이션에 예제 발언을 추가할 필요가 없습니다. `phonenumber` 엔터티는 `en-us` 문화권에서만 지원됩니다. 

## <a name="types-of-phonenumber"></a>Phonenumber 유형
Phonenumber는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>미리 빌드된 phonenumber 엔터티의 해결
다음 예제에서는 **builtin.phonenumber** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>다음 단계

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 

---
title: 단어 맞춤 정보 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API에서 단어 맞춤 정보를 받습니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.custom: seodec18
ms.openlocfilehash: 040d122810529daafcebc2e66f2fa7b2730a6a0d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514661"
---
# <a name="how-to-receive-word-alignment-information"></a>단어 맞춤 정보를 받는 방법

## <a name="receiving-word-alignment-information"></a>단어 맞춤 정보 받기
맞춤 정보를 받으려면 Translate 메서드를 사용하고 선택 사항인 includeAlignment 매개 변수를 포함합니다.

## <a name="alignment-information-format"></a>맞춤 정보 형식
맞춤은 원본의 모든 단어에 대해 다음 형식의 문자열 값으로 반환됩니다. 각 단어에 대한 정보는 중국어와 같이 공백으로 구분되지 않는 언어(스크립트)를 포함하여 공백으로 구분됩니다.

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

맞춤 문자열 예제: “0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21”.

즉, 콜론은 시작 및 끝 인덱스를 구분하고, 대시는 언어를 구분하고, 공백은 단어를 구분합니다. 한 단어는 다른 언어의 0개, 1개 또는 여러 단어와 정렬될 수 있고, 정렬된 단어가 비연속적일 수도 있습니다. 맞춤 정보를 사용할 수 없는 경우 Alignment 요소가 비어 있습니다. 이 경우에는 메서드가 오류를 반환하지 않습니다.

## <a name="restrictions"></a>제한
이 시점에는 다음 언어 쌍의 하위 집합에 대해서만 맞춤이 반환됩니다.
* 영어에서 다른 모든 언어로
* 다른 모든 언어에서 영어로(중국어 간체, 중국어 번체 및 라트비아어에서 영어로 번역하는 경우 제외)
* 일본어에서 한국어로 또는 한국어에서 일본어로. 문장이 미리 준비된 번역인 경우에는 맞춤 정보가 표시되지 않습니다. 미리 준비된 번역의 예로 “테스트입니다.”, “당신을 사랑합니다.”, 기타 빈도가 높은 문장 등이 있습니다.

## <a name="example"></a>예

예제 JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```

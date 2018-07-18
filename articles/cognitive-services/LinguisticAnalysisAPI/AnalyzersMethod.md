---
title: Linguistic Analysis API의 Analyzers 메서드 | Microsoft Docs
description: analyzers REST API는 현재 Microsoft Cognitive Services의 서비스에서 지원되는 분석기 목록을 제공합니다.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372846"
---
# <a name="analyzers-method"></a>Analyzers 메서드

**analyzers** REST API는 현재 서비스에서 지원되는 분석기 목록을 제공합니다.
응답에는 분석기 [이름](Analyzer-Names.md)과 각 분석기에서 지원되는 언어(예: 영어를 나타내는 "en")가 포함됩니다.

## <a name="request-parameters"></a>요청 매개 변수
없음

<br>

## <a name="response-parameters"></a>응답 매개 변수
Name | type | 설명
-----|------|--------------
언어 | 문자열 목록 | 이 분석기를 사용할 수 있는 2자로 된 ISO 언어 코드 목록.
id   | string | 이 분석기의 고유 ID
kind | string | 여기 있는 분석기의 다양한 형식
사양 | string | 이 분석기에 사용되는 사양의 이름
구현 | string | 이 분석기의 기반이 되는 모델 및/또는 알고리즘에 대한 설명

<br>
## <a name="example"></a>예
GET/분석기

응답: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3", 
        "implementation": "SplitMerge"
    }, 
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags", 
        "specification": "PennTreebank3", 
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens", 
        "specification":"PennTreebank3", 
        "implementation": "regexes"
    } 
]
```

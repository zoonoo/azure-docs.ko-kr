---
title: Analyzers 메서드 - Lingistic Analysis API
titlesuffix: Azure Cognitive Services
description: Analyzers REST API는 현재 Linguistic Analysis API에서 지원되는 분석기 목록을 제공합니다.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 9338e87644554ac8b3121c5341cea6f2bc512a97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878190"
---
# <a name="analyzers-method"></a>Analyzers 메서드

> [!IMPORTANT]
> Linguistic Analysis 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

**analyzers** REST API는 현재 서비스에서 지원되는 분석기 목록을 제공합니다.
응답에는 분석기 [이름](Analyzer-Names.md)과 각 분석기에서 지원되는 언어(예: 영어를 나타내는 "en")가 포함됩니다.

## <a name="request-parameters"></a>요청 매개 변수
없음

<br>

## <a name="response-parameters"></a>응답 매개 변수
Name | Type | 설명
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

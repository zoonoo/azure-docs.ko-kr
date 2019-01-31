---
title: 요청 제한 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 이 문서는 Translator Text API에 대한 요청 제한을 나열합니다. 비용은 요청당 5,000자의 제한으로 요청 빈도가 아닌 문자 수에 따라 청구됩니다. 문자 제한은 시간당 2백만 자의 F0 제한으로 구독 기반입니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: 2115baae3f13c6ca6c0b9aca73d6fd43f5fcdbb4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455714"
---
# <a name="request-limits-for-translator-text"></a>Translator Text에 대한 요청 제한

이 문서는 Translator Text API에 대한 제한 한도를 제공합니다. 서비스는 번역, 음차, 문장 길이 감지, 언어 감지 및 대체 번역을 포함합니다.

## <a name="character-limits-per-request"></a>요청당 문자 제한

각 요청은 5,000자로 제한됩니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 짧은 요청을 전송하고, 특정 시점에 해결되지 않은 일부 요청을 수행하는 것이 좋습니다.

Translator Text API에 대한 미해결 요청 수에 제한이 없습니다.

## <a name="character-limits-per-hour"></a>시간당 문자 제한

시간당 문자 제한은 Translator Text 구독 계층을 기반으로 합니다. 이러한 제한에 도달하거나 초과하는 경우 할당량 응답의 부족을 받게 될 가능성이 있습니다.

| 계층 | 문자 제한 |
|------|-----------------|
| F0 | 시간당 2백만 자 |
| S1 | 시간당 4천만 자 |
| S2 | 시간당 4천만 자 |
| S3 | 시간당 1억 2천만 자 |
| S4 | 시간당 2억 자 |

이러한 제한은 Microsoft의 일반 시스템으로 제한됩니다. Microsoft의 Translator Hub를 사용하는 사용자 지정 번역 시스템은 초당 1,800자로 제한됩니다.

## <a name="latency"></a>대기 시간

Translator Text에는 13초의 최대 대기 시간이 있습니다. 이 시점에서 결과 또는 시간 초과 응답을 받습니다. 일반적으로 응답은 150밀리초~300밀리초 단위로 반환됩니다. 응답 시간은 크기 또는 요청 및 언어 쌍에 따라 달라집니다.

## <a name="sentence-length-limits"></a>문장 길이 제한

[BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 함수를 사용하는 경우 문장 길이는 275자로 제한됩니다. 이러한 언어에 대한 예외가 있습니다.

| 언어 | 코드 | 문자 제한 |
|----------|------|-----------------|
| 중국어 | zh | 132 |
| 독일어 | de | 290 |
| 이탈리아어 | it | 280 |
| 일본어 | ja | 150 |
| 포르투갈어 | pt | 290 |
| 스페인어 | es | 280 |
| 이탈리아어 | it | 280 |
| 태국어 | th | 258 |

> [!NOTE]
> 이 제한은 번역에 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [가격](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [국가별 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

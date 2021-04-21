---
title: 요청 제한 - Translator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Translator에 대한 요청 제한을 나열합니다. 비용은 요청당 5,000자의 제한으로 요청 빈도가 아닌 문자 수에 따라 청구됩니다. 문자 제한은 시간당 2백만 자의 F0 제한으로 구독 기반입니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2bc2c1361c7d2f73ff8a67e906a6db725f669d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895410"
---
# <a name="request-limits-for-translator"></a>Translator에 대한 요청 제한

이 문서에서는 Translator에 대한 제한 한도를 제공합니다. 서비스는 번역, 음차, 문장 길이 감지, 언어 감지 및 대체 번역을 포함합니다.

## <a name="character-and-array-limits-per-request"></a>요청당 문자 및 배열 제한

각 번역 요청은 번역하는 모든 대상 언어 전체에서 10,000자로 제한됩니다. 예를 들어 3,000자를 3개 언어로 번역하는 번역 요청을 보내면 요청 크기가 3,000x3 = 9,000자가 되어 요청 제한을 충족합니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 짧은 요청을 보내는 것이 좋습니다.

다음 표에는 Translator의 각 작업에 대한 배열 요소 및 문자 제한이 나열되어 있습니다.

| 작업 | 배열 요소 최대 크기 |    배열 요소 최대 개수 |    최대 요청 크기(문자) |
|:----|:----|:----|:----|
| Translate | 10000    | 100   | 10000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 50,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| 사전 조회| 100 |  10  | 1,000 |
| 사전 예제 | 텍스트 100 및 번역 100(총 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>시간당 문자 제한

시간당 문자 제한은 Translator 구독 계층을 기반으로 합니다. 

시간별 할당량은 해당 시간 동안 균등하게 사용해야 합니다. 예를 들어 시간당 200만 자 F0 계층 제한에서 문자는 분당 슬라이딩 윈도우(200만 자를 60분으로 나눈 값)인 약 33,300자보다 빠르게 사용될 수 없습니다.

이러한 제한에 도달 또는 초과하거나 단시간에 할당량의 일부를 너무 많이 보내면 할당량 초과 응답을 받을 가능성이 높습니다. 동시 요청에는 제한이 없습니다.

| 계층 | 문자 제한 |
|------|-----------------|
| F0 | 시간당 2백만 자 |
| S1 | 시간당 4천만 자 |
| S2/C2 | 시간당 4천만 자 |
| S3/C3 | 시간당 1억 2천만 자 |
| S4/C4 | 시간당 2억 자 |

[다중 서비스 구독](./reference/v3-0-reference.md#authentication)의 한도는 S1 계층과 동일합니다.

이러한 한도는 Microsoft의 표준 번역 모델로 제한됩니다. Custom Translator를 사용하는 사용자 지정 번역 모델은 초당 1,800자로 제한됩니다.

## <a name="latency"></a>대기 시간

Translator는 최대 대기 시간이 표준 모델을 사용하는 경우 15초이고 사용자 지정 모델을 사용하는 경우 120초입니다. 일반적으로 100자 이내의 텍스트에 대한 응답은 150~300밀리초에 반환됩니다. Custom Translator 모델은 지속적인 요청 속도에서 비슷한 대기 시간 특성을 가지며, 요청 속도가 일시적이면 대기 시간이 길어질 수 있습니다. 응답 시간은 요청 크기 또는 언어 쌍에 따라 달라집니다. 해당 시간 범위 내에 번역 또는 [오류 응답](./reference/v3-0-reference.md#errors)을 받지 못하는 경우에는 코드, 네트워크 연결을 확인하고 다시 시도하세요. 

## <a name="sentence-length-limits"></a>문장 길이 제한

[BreakSentence](./reference/v3-0-break-sentence.md) 함수를 사용하는 경우 문장 길이는 275자로 제한됩니다. 이러한 언어에 대한 예외가 있습니다.

| 언어 | 코드 | 문자 제한 |
|----------|------|-----------------|
| 중국어 | zh | 166 |
| 독일어 | de | 800 |
| 이탈리아어 | it | 800 |
| 일본어 | ja | 166 |
| 포르투갈어 | pt | 800 |
| 스페인어 | es | 800 |
| 태국어 | th | 180 |

> [!NOTE]
> 이 제한은 번역에 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [가격](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [국가별 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator 참조](./reference/v3-0-reference.md)
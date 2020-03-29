---
title: 요청 제한 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 이 문서는 Translator Text API에 대한 요청 제한을 나열합니다. 비용은 요청당 5,000자의 제한으로 요청 빈도가 아닌 문자 수에 따라 청구됩니다. 문자 제한은 시간당 2백만 자의 F0 제한으로 구독 기반입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498941"
---
# <a name="request-limits-for-translator-text"></a>Translator Text에 대한 요청 제한

이 문서는 Translator Text API에 대한 제한 한도를 제공합니다. 서비스는 번역, 음차, 문장 길이 감지, 언어 감지 및 대체 번역을 포함합니다.

## <a name="character-and-array-limits-per-request"></a>요청당 문자 및 배열 제한

각 번역 요청은 번역하는 모든 대상 언어에서 5,000자로 제한됩니다. 예를 들어 1,500자로 번역 요청을 3개 언어로 번역하면 요청 크기가 1,500x3 = 4,500자로 요청 제한을 조정합니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 더 짧은 요청을 보내는 것이 좋습니다.

다음 표에는 번역기 텍스트 API의 각 작업에 대한 배열 요소 및 문자 제한이 나열됩니다.

| 작업(Operation) | 배열 요소의 최대 크기 |   배열 요소의 최대 수 |  최대 요청 크기(문자) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10000 | 100 |   50,000 |
| BreakSentence | 10000    | 100 | 50,000 |
| 사전 조회| 100 |  10  | 1,000 |
| 사전 예제 | 텍스트의 경우 100개, 번역용 100개(총 200개)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>시간당 문자 제한

시간당 문자 제한은 Translator Text 구독 계층을 기반으로 합니다. 

시간당 할당량은 시간 당 균등 하 게 사용 되어야 합니다. 예를 들어 시간당 200만 자로 구성된 F0 계층 제한에서는 캐릭터를 분당 약 33,300자(200만 자+60분으로 나눈 값)보다 빠르게 사용해야 합니다.

이러한 한도에 도달하거나 초과하거나 단기간에 할당량의 일부를 너무 많이 보내면 할당량 이외 응답을 받을 가능성이 큽입니다. 동시 요청에는 제한이 없습니다.

| 계층 | 문자 제한 |
|------|-----------------|
| F0 | 시간당 2백만 자 |
| S1 | 시간당 4천만 자 |
| S2 / C2 | 시간당 4천만 자 |
| S3 / C3 | 시간당 1억 2천만 자 |
| S4 / C4 | 시간당 2억 자 |

[다중 서비스 구독에](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) 대한 제한은 S1 계층과 동일합니다.

이러한 제한은 Microsoft의 표준 번역 모델로 제한됩니다. 사용자 지정 번역기를 사용하는 사용자 지정 번역 모델은 초당 1,800자로 제한됩니다.

## <a name="latency"></a>대기 시간

번역기 텍스트 API는 표준 모델을 사용하는 최대 대기 시간이 15초이고 사용자 지정 모델을 사용할 때는 120초입니다. 일반적으로 *100자 이내의 텍스트에 대한* 응답은 150밀리초에서 300밀리초로 반환됩니다. 사용자 지정 번역기 모델은 지속적인 요청 속도에 대해 비슷한 대기 시간 특성을 가지며 요청 속도가 간헐적으로 발생할 때 대기 시간이 더 많을 수 있습니다. 응답 시간은 요청 및 언어 쌍의 크기에 따라 달라집니다. 해당 기간 내에 번역 또는 [오류 응답이](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) 수신되지 않으면 코드, 네트워크 연결 및 다시 시도하십시오. 

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

* [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [국가별 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

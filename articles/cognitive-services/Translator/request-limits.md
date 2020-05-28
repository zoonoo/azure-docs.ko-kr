---
title: 요청 제한-번역기
titleSuffix: Azure Cognitive Services
description: 이 문서에는 번역기에 대 한 요청 한도가 나열 됩니다. 비용은 요청당 5,000자의 제한으로 요청 빈도가 아닌 문자 수에 따라 청구됩니다. 문자 제한은 시간당 2백만 자의 F0 제한으로 구독 기반입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 100084a244944bcaf0d566e347d56dd4a03ecfae
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995788"
---
# <a name="request-limits-for-translator"></a>번역기에 대 한 요청 제한

이 문서에서는 번역기에 대 한 제한 제한을 제공 합니다. 서비스는 번역, 음차, 문장 길이 감지, 언어 감지 및 대체 번역을 포함합니다.

## <a name="character-and-array-limits-per-request"></a>요청당 문자 및 배열 제한

각 번역 요청은 번역 하는 모든 대상 언어에서 5000 자로 제한 됩니다. 예를 들어 3 개의 다른 언어로 변환 하기 위해 1500 자의 변환 요청을 보내는 경우 요청 크기가 1, 500x3 = 4500 자인 요청 한도가 충족 됩니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 짧은 요청을 보내는 것이 좋습니다.

다음 표에서는 변환기의 각 작업에 대 한 배열 요소 및 문자 제한을 나열 합니다.

| 작업(Operation) | 배열 요소의 최대 크기 |    최대 배열 요소 수 |    최대 요청 크기 (문자) |
|:----|:----|:----|:----|
| Translate | 5,000    | 100    | 5,000 |
| Transliterate | 5,000    | 10    | 5,000 |
| Detect | 10000 |    100 |    50,000 |
| BreakSentence | 10000    | 100 |    50,000 |
| 사전 조회| 100 |    10    | 1,000 |
| 사전 예제 | 100 for text 및 100 (200 total)| 10|    2,000 |

## <a name="character-limits-per-hour"></a>시간당 문자 제한

시간당 문자 제한은 번역기 구독 계층을 기반으로 합니다. 

매시간 할당량은 전체 시간 동안 균등 하 게 사용 해야 합니다. 예를 들어 시간당 200만 자 F0 계층 제한에서 문자는 분당 슬라이딩 윈도우 (200만 문자를 60 분으로 나눈 값) 33300 보다 더 빨리 사용 되어야 합니다.

이러한 한도에 도달 하거나 초과할 짧은 시간 동안 할당량의 일부를 너무 많이 보내면 할당량 초과 응답이 수신 될 가능성이 높습니다. 동시 요청에는 제한이 없습니다.

| 계층 | 문자 제한 |
|------|-----------------|
| F0 | 시간당 2백만 자 |
| S1 | 시간당 4천만 자 |
| S2/C2 | 시간당 4천만 자 |
| S3/C3 | 시간당 1억 2천만 자 |
| S4/C4 | 시간당 2억 자 |

[다중 서비스 구독](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) 에 대 한 제한은 S1 계층과 동일 합니다.

이러한 제한은 Microsoft의 표준 변환 모델로 제한 됩니다. 사용자 지정 변환기를 사용 하는 사용자 지정 변환 모델은 초당 1800 자로 제한 됩니다.

## <a name="latency"></a>대기 시간

사용자 지정 모델을 사용 하는 경우 변환기는 표준 모델 및 120 초를 사용 하 여 최대 대기 시간이 15 초입니다. 일반적으로 *100 자 내의 텍스트에 대 한* 응답은 150 밀리초에서 300 밀리초로 반환 됩니다. 사용자 지정 번역기 모델은 지속적으로 요청 률과 비슷한 대기 시간 특성을 가지 며, 요청 속도가 간헐적 이면 대기 시간이 길어질 수 있습니다. 응답 시간은 요청 및 언어 쌍의 크기에 따라 달라 집니다. 해당 기간 내에 번역이 나 [오류 응답](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) 을 받지 못한 경우에는 해당 코드, 네트워크 연결을 확인 하 고 다시 시도 하세요. 

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
* [v3 Translator 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

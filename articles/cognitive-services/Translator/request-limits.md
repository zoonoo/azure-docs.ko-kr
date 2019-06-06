---
title: 요청 제한 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 이 문서는 Translator Text API에 대한 요청 제한을 나열합니다. 비용은 요청당 5,000자의 제한으로 요청 빈도가 아닌 문자 수에 따라 청구됩니다. 문자 제한은 시간당 2백만 자의 F0 제한으로 구독 기반입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514649"
---
# <a name="request-limits-for-translator-text"></a>Translator Text에 대한 요청 제한

이 문서는 Translator Text API에 대한 제한 한도를 제공합니다. 서비스는 번역, 음차, 문장 길이 감지, 언어 감지 및 대체 번역을 포함합니다.

## <a name="character-and-array-limits-per-request"></a>요청에 따라 문자 및 배열 제한

각 변환 요청 5,000 자로 제한 됩니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 이 더 짧은 요청을 보내는 것이 좋습니다.

다음 테이블 목록 배열 요소와 문자 제한 Translator Text API의 각 작업에 대 한 합니다.

| 작업(Operation) | 배열 요소의 최대 크기 |   배열 요소의 최대 수 |  최대 요청 크기 (자) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10000 | 100 |   50,000 |
| BreakSentence | 10000    | 100 | 5,0000 |
| 사전 조회| 100 |  10  | 1,000 |
| 사전 예제 | 텍스트 및 변환 (총 200)에 대 한 100 100| 10|   2,000 |

## <a name="character-limits-per-hour"></a>시간당 문자 제한

시간당 문자 제한은 Translator Text 구독 계층을 기반으로 합니다. 시간별 할당량 시간 전체에서 균등 하 게 사용 해야 합니다. 도달 이러한 제한을 초과할 하거나 짧은 기간에 너무 큰 할당량의 일부를 보낼 경우 할당량 응답의 부족 가능성이 받게 됩니다.

| 계층 | 문자 제한 |
|------|-----------------|
| F0 | 시간당 2백만 자 |
| S1 | 시간당 4천만 자 |
| S2 / C2 | 시간당 4천만 자 |
| S3 / C3 | 시간당 1억 2천만 자 |
| S4 / C4 | 시간당 2억 자 |

에 대 한 제한 [다중 서비스 구독](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) S1 계층으로 동일 합니다.

이러한 제한은 Microsoft의 표준 변환 모델을 제한 합니다. 사용자 지정 변환기를 사용 하는 사용자 지정 변환 모델은 초당 1,800 문자로 제한 됩니다.

## <a name="latency"></a>대기 시간

Translator Text API 15 초 표준 모델을 사용 하 여 최대 대기 시간을 있습니다. 변환 사용자 지정 모델을 사용 하 여 최대 25 초 대기 시간을 있습니다. 이 시점에서 결과 또는 시간 초과 응답을 받습니다. 일반적으로 응답은 150밀리초~300밀리초 단위로 반환됩니다. 응답 시간이 요청 및 언어 쌍의 크기에 따라 달라 집니다. 번역을 수신할 수 없는 경우 또는 [오류 응답](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) 해당 시간대 내에서 네트워크 연결을 확인 및 다시 시도 합니다.

## <a name="sentence-length-limits"></a>문장 길이 제한

[BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 함수를 사용하는 경우 문장 길이는 275자로 제한됩니다. 이러한 언어에 대한 예외가 있습니다.

| 언어 | 코드 | 문자 제한 |
|----------|------|-----------------|
| 중국어 | zh | 132 |
| 독일어 | de | 290 |
| 이탈리아어 | it | 280 |
| 일본어 | ja | 150 |
| 포르투갈어 | (태평양 표준시) | 290 |
| 스페인어 | es | 280 |
| 이탈리아어 | it | 280 |
| 태국어 | 번째 | 258 |

> [!NOTE]
> 이 제한은 번역에 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [가격](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [국가별 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

---
title: Azure CDN에 대 한 표준 규칙 엔진 참조 | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN)에 대 한 표준 규칙 엔진의 일치 조건 및 작업에 대 한 참조 설명서입니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242211"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 참조

Azure Content Delivery Network (Azure CDN)에 대 한 [표준 규칙 엔진](cdn-standard-rules-engine.md) 에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure CDN에 대 한 표준 규칙 엔진에서 사용할 수 있는 일치 조건 및 기능에 대해 자세히 설명 합니다.

규칙 엔진은 표준 Azure CDN에서 특정 유형의 요청을 처리 하는 방법에 대 한 최종 권한으로 설계 되었습니다.

**규칙의 일반적인 용도** 는 다음과 같습니다.

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 요청을 리디렉션합니다.
- HTTP 요청 및 응답 헤더를 수정 합니다.

## <a name="terminology"></a>용어

규칙 엔진에서 규칙을 정의 하려면 [일치 조건](cdn-standard-rules-engine-match-conditions.md) 및 [작업](cdn-standard-rules-engine-actions.md)을 설정 합니다.

 ![Azure CDN 규칙 구조](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

각 규칙에는 최대 10 개의 일치 조건 및 5 개의 작업이 있을 수 있습니다. 각 Azure CDN 끝점에는 최대 25 개의 규칙이 있을 수 있습니다. 

이 제한에는 기본 *전역 규칙이* 포함 됩니다. 전역 규칙에 일치 하는 조건이 없습니다. 전역 규칙에 정의 된 작업은 항상 트리거됩니다.

   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 규칙에 지정된 작업은 후속 규칙으로 덮어쓸 수 있습니다.

## <a name="limits-and-pricing"></a>제한 및 가격 책정 

각 Azure CDN 끝점에는 최대 25 개의 규칙이 있을 수 있습니다. 각 규칙에는 최대 10 개의 일치 조건 및 5 개의 작업이 있을 수 있습니다. 규칙 엔진의 가격은 다음 차원을 따릅니다. 
- 규칙: 월별 규칙 $1 
- 처리 된 요청 수: 백만 개 요청당 $0.60
- 처음 5 개의 규칙은 무료로 유지 됩니다.

## <a name="syntax"></a>구문

규칙에서 특수 문자를 처리 하는 방법은 서로 다른 일치 조건 및 작업에서 텍스트 값을 처리 하는 방법에 따라 달라 집니다. 일치 조건 또는 작업은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

- [리터럴 값](#literal-values)
- [와일드카드 값](#wildcard-values)


### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석 되는 텍스트는 규칙에서 일치 해야 하는 값의 일부로 *% 기호를 제외한* 모든 특수 문자를 처리 합니다. 예를 들어,로 설정 된 리터럴 일치 조건은 `'*'` 정확한 값을 찾은 경우에만 충족 됩니다 `'*'` .

백분율 기호는 URL 인코딩을 나타내는 데 사용 됩니다 (예: `%20` ).

### <a name="wildcard-values"></a>와일드카드 값

현재 표준 규칙 엔진의 **Urlpath 일치 조건** 에 와일드 카드 문자를 지원 합니다. \*문자는 하나 이상의 문자를 나타내는 와일드 카드입니다. 

## <a name="next-steps"></a>다음 단계

- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)

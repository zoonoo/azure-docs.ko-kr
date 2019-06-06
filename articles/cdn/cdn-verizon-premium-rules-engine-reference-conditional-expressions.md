---
title: Verizon 프리미엄의 azure CDN 규칙 엔진 조건식 | Microsoft Docs
description: Verizon 프리미엄의 Azure CDN에 대 한 참조 설명서 규칙 엔진 일치 조건 및 기능입니다.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481536"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Verizon 프리미엄의 규칙 엔진 조건식에서 azure CDN

이 문서에 대 한 Azure 콘텐츠 배달 네트워크 (CDN)는 조건식의 자세한 설명에 나와 [규칙 엔진](cdn-verizon-premium-rules-engine.md)합니다.

규칙의 첫 번째 부분은 조건식입니다.

조건식 | 설명
-----------------------|-------------
IF | IF 식은 항상 규칙의 첫 번째 문의 일부입니다. 다른 모든 조건식과 마찬가지로 이 IF 문은 일치와 관련이 있어야 합니다. 없는 추가 조건식을 정의한 경우이 일치 기능 집합이 요청에 적용 될 수 전에 충족 해야 하는 조건을 결정 합니다.
AND IF | AND IF 식은 IF, AND IF 형식의 조건식 뒤에만 추가할 수 있습니다. 초기 IF 문에 대해 충족되어야 하는 다른 조건이 있음을 나타냅니다.
ELSE IF| ELSE IF 식은 이 ELSE IF 문과 관련된 기능 집합이 수행되기 전에 충족되어야 하는 대체 조건을 지정합니다. ELSE IF 문이 있으면 이전 문의 끝을 나타냅니다. ELSE IF 문 다음에 올 수 있는 유일한 조건식은 다른 ELSE IF 문입니다. 즉, ELSE IF 문은 충족해야 하는 단일 추가 조건을 지정하는 데에만 사용할 수 있습니다.

**예제**: ![CDN 일치 조건](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다.
   > 예제: Catch-all 규칙은 토큰 기반 인증을 통해 모든 요청을 보호합니다. 특정 요청 형식에 대한 예외를 만들기 위해 다른 규칙을 바로 아래에 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)
- [규칙 엔진 일치 조건](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)
- [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-verizon-premium-rules-engine.md)
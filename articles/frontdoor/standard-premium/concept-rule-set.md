---
title: 'Azure Front 도어: 규칙 집합'
description: 이 문서에서는 Azure Front 도어 표준/프리미엄 규칙 집합 기능에 대 한 개요를 제공 합니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099970"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)에 대 한 규칙 집합은 무엇 인가요?

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

규칙 집합은 여러 경로에 연결할 수 있는 단일 집합으로 규칙 조합을 그룹화 하는 사용자 지정 규칙 엔진입니다. 규칙 집합을 사용 하 여에 지에서 요청을 처리 하는 방법과 Azure Front 도어가 해당 요청을 처리 하는 방법을 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="common-supported-scenarios"></a>일반적으로 지원 되는 시나리오

* 보안 헤더를 구현 하 여 HTTP Strict (Strict Transport-Security), X-XSS 보호, 콘텐츠-보안 정책, X 프레임 옵션 및 CORS (원본 간 리소스 공유) 시나리오에 대 한 액세스 제어-원본 헤더와 같은 브라우저 기반 취약성을 방지 합니다. 쿠키를 사용하여 보안 기반 특성을 정의할 수도 있습니다.

* 클라이언트 장치 유형에 따라 모바일 또는 데스크톱 버전의 응용 프로그램으로 요청을 라우팅합니다.

* 리디렉션 기능을 사용 하 여 301, 302, 307 및 308을 클라이언트에 리디렉션하여 새 호스트 이름, 경로, 쿼리 문자열 또는 프로토콜로 보냅니다.

* 들어오는 요청에 따라 경로의 캐싱 구성을 동적으로 수정합니다.

* 요청 URL 경로를 다시 작성 하 고 요청을 구성 된 원본 그룹의 적절 한 원본으로 전달 합니다.

* 요청/응답 헤더를 추가, 수정 또는 제거 하 여 중요 한 정보를 숨기 거 나 헤더를 통해 중요 한 정보를 캡처합니다.

* 서버 변수를 지원 하 여 요청/응답 헤더 또는 URL 재작성 경로/쿼리 문자열 (예: 새 페이지 로드 또는 양식이 게시 된 경우)을 동적으로 변경 합니다. 서버 변수는 현재 **[규칙 집합 작업](concept-rule-set-actions.md)** 에서만 지원 됩니다.

## <a name="architecture"></a>Architecture

규칙 집합은에 지에서 요청을 처리 합니다. 요청이 Azure Front 도어 표준/프리미엄 끝점에 도착 하면 WAF가 먼저 실행 되 고 그 다음에 경로에 구성 된 설정이 적용 됩니다. 이러한 설정에는 경로와 연결 된 규칙 집합이 포함 됩니다. 규칙 집합은 경로의 위쪽에서 아래쪽으로 처리 됩니다. 규칙 집합 내의 규칙에도 동일 하 게 적용 됩니다. 각 규칙의 모든 작업이 실행되도록 하려면 규칙 내의 모든 일치 조건이 충족되어야 합니다. 요청이 규칙 집합 구성의 조건과 일치 하지 않으면 경로의 구성만 실행 됩니다.

**남은 규칙 평가 중지** 가 선택 된 경우 해당 경로와 연결 된 나머지 모든 규칙 집합이 실행 되지 않습니다.  

### <a name="example"></a>예제

다음 다이어그램에서는 WAF 정책이 먼저 실행 됩니다. 규칙 집합을 구성 하 여 응답 헤더를 추가 합니다. 그런 다음 헤더는 일치 조건이 충족 되는 경우 캐시 컨트롤의 최대 기간을 변경 합니다.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="규칙 집합의 아키텍처를 보여 주는 다이어그램입니다." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>용어

Azure Front 도어 규칙 집합을 사용 하 여 각 규칙 집합으로 구성 된 규칙 집합 구성의 조합을 만들 수 있습니다. 다음 줄에는 규칙 집합을 구성할 때 제공 되는 몇 가지 유용한 용어 있습니다.

할당량 한도를 제한 하려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.

* *규칙 집합*: 하나 또는 여러 [경로](concept-route.md)에 연결 되는 규칙 집합입니다. 각 구성은 25개의 규칙으로 제한됩니다. 10개까지 구성을 만들 수 있습니다.

* 규칙 *집합 규칙*: 최대 10 개의 일치 조건 및 5 개의 작업으로 구성 된 규칙입니다. 규칙은 규칙 집합에 대해 로컬 이며 규칙 집합에서 사용할 수 있도록 내보낼 수 없습니다. 사용자는 여러 규칙 집합에서 동일한 규칙을 만들 수 있습니다.

* *일치 조건*: 들어오는 요청을 구문 분석하는 데 활용할 수 있는 많은 일치 조건이 있습니다. 규칙 하나에 최대 10개의 일치 조건을 포함할 수 있습니다. 일치 조건은 **AND** 연산자를 사용하여 평가됩니다. *정규식은 조건에서 지원 됩니다*. 일치 조건의 전체 목록을 [규칙 집합 조건](concept-rule-set-match-conditions.md)에서 찾을 수 있습니다.

* *Action*: 작업은 일치 조건에 따라 AFD에서 들어오는 요청을 처리 하는 방법을 결정 합니다. 캐싱 동작을 수정 하 고 요청 헤더/응답 헤더를 수정할 수 있으며 URL 재작성 및 URL 리디렉션을 수행할 수 있습니다. *서버 변수는 동작에서 지원 됩니다*. 규칙 하나에 최대 10개의 일치 조건을 포함할 수 있습니다. [규칙 집합 작업](concept-rule-set-actions.md)의 전체 목록을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [전면 도어 표준/프리미엄을 만드는](create-front-door-portal.md)방법에 대해 알아봅니다.
* 첫 번째 [규칙 집합](how-to-configure-rule-set.md)을 구성 하는 방법에 대해 알아봅니다.
 

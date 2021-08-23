---
title: 'Azure Front Door: 규칙 집합'
description: 이 문서는 Azure Front Door 표준/프리미엄 규칙 집합의 개요를 제공합니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 47f69c72fdd7b3890d22d56de3a530135cf6fcc3
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437633"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)에 대한 규칙 집합은 무엇인가요?

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

규칙 집합은 규칙의 조합을 단일 집합으로 그룹화하는 사용자 지정 규칙 엔진입니다. 규칙 집합을 여러 경로에 연결할 수 있습니다. 규칙 집합을 사용하여 에지에서 요청을 처리하는 방법과 Azure Front Door가 해당 요청을 처리하는 방법을 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="common-supported-scenarios"></a>일반적으로 지원되는 시나리오

* CORS(원본 간 리소스 공유) 시나리오에 대한 브라우저 기반 취약성(예: HSTS(HTTP Strict-Transport-Security), X-XSS-Protection, Content-Security-Policy, X-Frame-Options 및 Access-Control-Allow-Origin 헤더)을 방지하기 위해 보안 헤더를 구현합니다. 쿠키를 사용하여 보안 기반 특성을 정의할 수도 있습니다.

* 클라이언트 장치 유형을 기반으로 하여 요청을 응용 프로그램의 모바일 또는 데스크톱 버전으로 라우팅합니다.

* 새 호스트 이름, 경로, 쿼리 문자열, 프로토콜로 보내기 위해 리디렉션 기능을 사용하여 301, 302, 307, 308 리디렉션을 클라이언트에 반환합니다.

* 들어오는 요청에 따라 경로의 캐싱 구성을 동적으로 수정합니다.

* 요청 URL 경로를 다시 작성하고 구성된 원점 그룹의 적절한 원점에 요청을 전달합니다.

* 요청/응답 헤더를 추가, 수정, 제거하여 중요한 정보를 숨기거나 헤더를 통해 중요한 정보를 캡처합니다.

* 서버 변수를 지원하여 요청/응답 헤더 또는 URL 다시 쓰기 경로/쿼리 문자열을 동적으로 변경할 수 있습니다(예: 새 페이지를 로드하거나 양식을 게시하는 경우). 서버 변수는 현재 **[규칙 집합 작업](concept-rule-set-actions.md)** 에서만 지원됩니다.

## <a name="architecture"></a>Architecture

규칙 집합은 에지에서 요청을 처리합니다. 요청이 Azure Front Door 표준/프리미엄 엔드포인트에 도착하면 WAF가 먼저 실행되고 그 다음에 경로에 구성된 설정이 적용됩니다. 이러한 설정에는 경로와 연결된 규칙 집합이 포함됩니다. 규칙 집합은 경로의 위쪽에서 아래쪽으로 처리됩니다. 규칙 집합 내의 규칙에도 동일하게 적용됩니다. 각 규칙의 모든 작업이 실행되도록 하려면 규칙 내의 모든 일치 조건이 충족되어야 합니다. 요청이 규칙 집합 구성의 조건과 일치하지 않으면 경로의 구성만 실행됩니다.

**남은 규칙 평가 중지** 가 선택된 경우 해당 경로와 연결된 나머지 모든 규칙 집합이 실행되지 않습니다.  

### <a name="example"></a>예제

다음 다이어그램에서는 WAF 정책이 먼저 실행됩니다. 규칙 집합을 구성하여 응답 헤더를 추가합니다. 일치 조건이 충족되면 헤더에서 캐시 제어의 최대 기간을 변경합니다.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="규칙 집합의 아키텍처를 보여주는 다이어그램입니다." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>용어

Azure Front Door 규칙 집합을 사용하여 각 규칙 집합으로 구성된 규칙 집합 구성의 조합을 생성할 수 있습니다. 다음은 규칙 집합을 구성할 때 제공되는 몇 가지 유용한 용어를 보여줍니다.

할당량 한도에 대한 자세한 내용은 [Azure 구독 및 서비스 한도, 할당량, 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

* *규칙 집합*: 하나 또는 여러 [경로](concept-route.md)에 연결되는 규칙 집합입니다.

* *규칙 집합 규칙*: 최대 10개의 일치 조건과 5개의 작업으로 구성된 규칙입니다. 규칙은 규칙 집합에 대해 로컬이며 규칙 집합 간에 사용할 수 있도록 내보낼 수 없습니다. 사용자는 여러 규칙 집합에서 동일한 규칙을 생성할 수 있습니다.

* *일치 조건*: 들어오는 요청을 구문 분석하는 데 활용할 수 있는 많은 일치 조건이 있습니다. 규칙 하나에 최대 10개의 일치 조건을 포함할 수 있습니다. 일치 조건은 **AND** 연산자를 사용하여 평가됩니다. *정규식은 조건에서 지원됩니다*. 일치 조건의 전체 목록을 [규칙 집합 일치 조건](concept-rule-set-match-conditions.md)에서 찾을 수 있습니다.

* *작업*: 작업은 일치 조건에 따라 AFD에서 들어오는 요청을 처리하는 방법을 결정합니다. 캐싱 동작을 수정하고 요청 헤더/응답 헤더를 수정할 수 있으며 URL 재작성 및 URL 리디렉션을 수행할 수 있습니다. *서버 변수는 작업에서 지원됩니다*. 규칙 하나에 최대 10개의 일치 조건을 포함할 수 있습니다. 작업 전체 목록은 [규칙 집합 규칙](concept-rule-set-actions.md)에서 찾을 수 있습니다.

## <a name="arm-template-support"></a>ARM 템플릿 지원

Azure Resource Manager 템플릿을 사용하여 규칙 집합을 구성할 수 있습니다. [예시 템플릿을 참조하세요](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set). [일치 조건](concept-rule-set-match-conditions.md) 및 [작업](concept-rule-set-actions.md)에 대한 설명서 예시에 포함된 JSON 또는 Bicep 코드 조각을 사용하여 동작을 사용자 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Front Door 표준/프리미엄을 만드는](create-front-door-portal.md) 방법을 알아봅니다.
* 첫 번째 [규칙 집합](how-to-configure-rule-set.md)을 구성하는 방법을 알아봅니다.

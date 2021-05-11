---
title: Azure Front Door 표준/프리미엄(미리 보기) 규칙 집합을 사용하여 보안 헤더 구성
description: 이 문서에서는 규칙 집합을 사용하여 보안 헤더를 구성하는 방법에 대한 지침을 제공합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099900"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Azure Front Door 표준/프리미엄(미리 보기) 규칙 집합을 사용하여 보안 헤더 구성

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 보안 헤더를 구현하여 HSTS(HTTP Strict-Transport-Security), X-XSS 보호, 콘텐츠 보안 정책 또는 X-Frame-Options와 같은 브라우저 기반 취약성을 방지하는 방법을 보여줍니다. 쿠키를 사용하여 보안 기반 특성을 정의할 수도 있습니다.

다음 예제에서는 라우팅 내 경로와 일치하는 모든 들어오는 요청에 Content-Security-Policy 헤더를 추가하는 방법을 보여 줍니다. 여기서는 신뢰할 수 있는 사이트, **https://apiphany.portal.azure-api.net** 의 스크립트만 허용하여 애플리케이션에서 실행합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 보안 헤더를 구성하려면 먼저 Front Door를 만들어야 합니다. 자세한 내용은 [빠른 시작: Front Door 만들기](create-front-door-portal.md)를 참조하세요.
* 이전에 규칙 집합 기능을 사용한 적이 없는 경우 [규칙 집합 설정](how-to-configure-rule-set.md) 방법을 검토하세요.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Azure Portal에서 콘텐츠 보안 정책 헤더 추가

1. Azure Front Door 표준/프리미엄 프로필로 이동하고 **설정** 아래에서 **규칙 집합** 을 선택합니다.

1. **추가** 를 선택하여 새 규칙 집합을 추가합니다. 규칙 집합에 **이름** 을 지정하고 규칙에 **이름** 을 제공합니다. **작업 추가** 를 선택한 다음 **응답 헤더** 를 선택합니다.

1. 이 헤더를 이 라우팅의 들어오는 모든 요청에 대한 응답으로 추가하려면 연산자를 **추가** 로 설정합니다.

1. 헤더 이름: **콘텐츠 보안 정책** 을 추가하고 이 헤더에 허용되는 값을 정의합니다. 이 시나리오에서는 *"script-src 'self' https://apiphany.portal.azure-api.net"* 을 선택합니다.

1. 구성에 원하는 규칙을 모두 추가한 후에는 규칙 집합을 라우팅과 연결해야 합니다. 규칙 집합이 작업을 수행하도록 허용하려면 이 단계가 필수적입니다. 

> [!NOTE]
> 이 시나리오에서는 [일치 조건](concept-rule-set-match-conditions.md)을 규칙에 추가하지 않았습니다. 연결된 라우팅에 정의된 경로와 일치하는 모든 들어오는 요청에 이 규칙이 적용됩니다. 이러한 요청의 하위 집합에만 적용하려면 특정 **일치 조건** 을 이 규칙에 추가해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

### <a name="deleting-a-rule"></a>규칙 삭제

이전 단계에서는 규칙 집합을 사용하여 Content-Security-Policy 헤더를 구성했습니다. 더 이상 규칙이 필요하지 않은 경우 규칙 집합 이름을 선택한 다음 규칙 삭제를 선택할 수 있습니다. 

### <a name="deleting-a-rule-set"></a>규칙 집합 삭제

규칙 집합을 삭제하려면 삭제 전에 모든 라우팅에서 연결을 해제해야 합니다. 규칙 집합을 삭제하는 방법에 대한 자세한 지침은 [규칙 집합 구성](how-to-configure-rule-set.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Front Door용 Web Application Firewall을 구성하는 방법에 대한 자세한 내용은 [Web Application Firewall 및 Front Door](../../web-application-firewall/afds/afds-overview.md)를 참조하세요.

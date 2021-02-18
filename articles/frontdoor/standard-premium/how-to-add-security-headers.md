---
title: Azure Front 도어 표준/프리미엄 (미리 보기) 규칙 집합을 사용 하 여 보안 헤더 구성
description: 이 문서에서는 규칙 집합을 사용 하 여 보안 헤더를 구성 하는 방법에 대 한 지침을 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099900"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 규칙 집합을 사용 하 여 보안 헤더 구성

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 보안 헤더를 구현 하 여 HSTS (HTTP Strict-Transport-보안), X-XSS 보호, 콘텐츠 보안 정책 또는 X 프레임 옵션과 같은 브라우저 기반 취약성을 방지 하는 방법을 보여 줍니다. 쿠키를 사용하여 보안 기반 특성을 정의할 수도 있습니다.

다음 예제에서는 경로에서 경로와 일치 하는 모든 들어오는 요청에 Content-type 헤더를 추가 하는 방법을 보여 줍니다. 여기서는 신뢰할 수 있는 사이트, **https://apiphany.portal.azure-api.net** 의 스크립트만 허용하여 애플리케이션에서 실행합니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 보안 헤더 구성을 구성 하려면 먼저 먼저 Front 도어를 만들어야 합니다. 자세한 내용은 [빠른 시작: Front Door 만들기](create-front-door-portal.md)를 참조하세요.
* 이전에 규칙 집합 기능을 사용 하지 않은 경우 [규칙 집합을 설정](how-to-configure-rule-set.md) 하는 방법을 검토 합니다.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Azure Portal에서 콘텐츠 보안 정책 헤더 추가

1. Azure Front 도어 표준/프리미엄 프로필로 이동 하 고 설정 아래에서 **규칙 집합** 을 선택 **합니다.**

1. **추가** 를 선택 하 여 새 규칙 집합을 추가 합니다. 규칙에 **이름을** 지정 하 고 규칙의 **이름을** 제공 합니다. **작업 추가** 를 선택한 다음 **응답 헤더** 를 선택 합니다.

1. 연산자를 **Append** 로 설정 하 여이 경로에 대 한 들어오는 모든 요청에 대 한 응답으로이 헤더를 추가 합니다.

1. 헤더 이름: **콘텐츠 보안 정책** 을 추가하고 이 헤더에 허용되는 값을 정의합니다. 이 시나리오에서는 *"스크립트-src ' self ' https://apiphany.portal.azure-api.net "* 를 선택 합니다.

1. 구성에 원하는 규칙을 모두 추가한 후에는 규칙 집합을 경로와 연결 해야 합니다. 이 단계는 규칙 집합이 동작을 수행 하도록 허용 하는 *데 필요* 합니다. 

> [!NOTE]
> 이 시나리오에서는 [일치 조건](concept-rule-set-match-conditions.md)을 규칙에 추가하지 않았습니다. 연결 된 경로에 정의 된 경로와 일치 하는 들어오는 모든 요청에는이 규칙이 적용 됩니다. 이러한 요청의 하위 집합에만 적용하려면 특정 **일치 조건** 을 이 규칙에 추가해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

### <a name="deleting-a-rule"></a>규칙 삭제

이전 단계에서 규칙 집합을 사용 하 여 콘텐츠나 정책 헤더를 구성 했습니다. 규칙을 더 이상 원하지 않는 경우 규칙 집합 이름을 선택한 다음 규칙 삭제를 선택할 수 있습니다. 

### <a name="deleting-a-rule-set"></a>규칙 집합 삭제

규칙 집합을 삭제 하려면 삭제 하기 전에 모든 경로에서 규칙 집합의 연관을 해제 해야 합니다. 규칙 집합을 삭제 하는 방법에 대 한 자세한 지침은 [규칙 집합 구성](how-to-configure-rule-set.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

프런트 도어 용 웹 응용 프로그램 방화벽을 구성 하는 방법에 대 한 자세한 내용은 [웹 응용 프로그램 방화벽 및 Front 도어](../../web-application-firewall/afds/afds-overview.md)를 참조 하세요.

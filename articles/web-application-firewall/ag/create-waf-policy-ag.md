---
title: Application Gateway에 대 한 WAF (웹 응용 프로그램 방화벽) 정책 만들기
description: Application Gateway에 대 한 웹 응용 프로그램 방화벽 정책을 만드는 방법에 대해 알아봅니다.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886928"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Application Gateway에 대 한 웹 응용 프로그램 방화벽 정책 만들기

WAF 정책을 수신기와 연결 하면 단일 WAF 뒤에 여러 사이트가 다른 정책에 의해 보호 될 수 있습니다. 예를 들어 WAF 뒤에 5 개의 사이트가 있는 경우 다른 4 개의 주는 없이 한 사이트의 제외, 사용자 지정 규칙 및 관리 되는 규칙 집합을 사용자 지정 하기 위해 5 개의 별도의 WAF 정책 (각 수신기에 대해 하나)을 사용할 수 있습니다. 단일 정책을 모든 사이트에 적용 하려면 개별 수신기 대신 정책을 Application Gateway와 연결 하 여 전역적으로 적용 되도록 할 수 있습니다. 경로 기반 라우팅 규칙에도 정책을 적용할 수 있습니다. 

원하는 수 만큼 정책을 만들 수 있습니다. 정책을 만든 후에는 적용 되도록 Application Gateway 연결 되어야 하지만 응용 프로그램 게이트웨이 및 수신기의 조합에 연결할 수 있습니다. 

Application Gateway에 정책이 적용 된 경우 해당 Application Gateway의 수신기에 다른 정책을 적용 하면 수신기의 정책이 적용 되지만, 수신기의 정책이 할당 된 수신기에 대해서만 적용 됩니다. Application Gateway 정책은 특정 정책이 할당 되지 않은 다른 모든 수신기에도 적용 됩니다. 

   > [!NOTE]
   > 사이트별 및 URI별 WAF 정책은 공개 미리 보기로 제공됩니다. 즉, 이 기능은 Microsoft의 추가 사용 약관을 따릅니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
   > [!NOTE]
   > 방화벽 정책이 WAF에 연결 되 면 항상 해당 WAF에 연결 된 정책이 있어야 합니다. 해당 정책을 덮어쓸 수도 있지만 WAF에서 정책의 연결이 완전히 지원 되지 않습니다. 

모든 새 웹 응용 프로그램 방화벽의 WAF 설정 (사용자 지정 규칙, 관리 되는 rulset 구성, 제외 등)은 WAF 정책 내에 살고 있습니다. 기존 WAF가 있는 경우 이러한 설정은 WAF 구성에 여전히 있을 수 있습니다. 새 WAF 정책으로 이동 하는 방법에 대 한 단계는이 문서의 뒷부분에 있는 waf [구성으로 Waf 구성 마이그레이션](#migrate) 을 참조 하세요. 

## <a name="create-a-policy"></a>정책 만들기

먼저 Azure Portal를 사용 하 여 관리 되는 기본 규칙 집합 (DRS)으로 기본 WAF 정책을 만듭니다.

1. 포털의 왼쪽 위에서 **리소스 만들기**를 선택 합니다. **Waf**를 검색 하 고, **웹 응용 프로그램 방화벽**을 선택 하 고, **만들기**를 선택 합니다.
2. **WAF 정책 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력 하거나 선택 하 고 나머지 설정에 대 한 기본값을 적용 한 다음 **검토 + 만들기**를 선택 합니다.

   |설정  |값  |
   |---------|---------|
   |정책     |지역 WAF (Application Gateway)|
   |Subscription     |구독 이름 선택|
   |Resource group     |리소스 그룹 선택|
   |정책 이름     |WAF 정책의 고유한 이름을 입력 합니다.|
3. **연결** 탭에서 다음 설정 중 하나를 입력 한 다음 **추가**를 선택 합니다.

   |설정  |값  |
   |---------|---------|
   |Application Gateway 연결     |Application Gateway 프로필 이름을 선택 합니다.|
   |수신기 연결     |Application Gateway 수신기의 이름을 선택한 다음 **추가**를 선택 합니다.|

   > [!NOTE]
   > 이미 정책을 보유 하 고 있는 Application Gateway (또는 수신기)에 정책을 할당 하는 경우 원래 정책을 덮어쓰고 새 정책으로 대체 합니다.
4. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

   ![WAF 정책 기본 사항](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF 규칙 구성 (선택 사항)

WAF 정책을 만들 때 기본적으로 *검색* 모드에 있습니다. 검색 모드에서 WAF는 모든 요청을 차단 하지 않습니다. 대신 일치 WAF 규칙이 WAF 로그에 로깅됩니다. 작동 중인 WAF를 보려면 모드 설정을 *방지*로 변경할 수 있습니다. 방지 모드에서는 선택한 CRS 규칙 집합에 정의 된 일치 규칙이 차단 및/또는 WAF 로그에 기록 됩니다.

## <a name="managed-rules"></a>관리 규칙

Azure 관리 되는 OWASP 규칙은 기본적으로 사용 하도록 설정 되어 있습니다. 규칙 그룹 내에서 개별 규칙을 사용 하지 않도록 설정 하려면 해당 규칙 그룹 내의 규칙을 확장 하 고, 규칙 번호 앞의 확인란을 선택 하 고, 위의 탭에서 **사용 안 함** 을 선택 합니다.

[![관리 규칙](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>사용자 지정 규칙

사용자 지정 규칙을 만들려면 **사용자** 지정 규칙 탭에서 **사용자 지정 규칙 추가** 를 선택 합니다. 그러면 사용자 지정 규칙 구성 페이지가 열립니다. 다음 스크린샷에서는 쿼리 문자열에 *blockme*텍스트가 포함 된 경우 요청을 차단 하도록 구성 된 예제 사용자 지정 규칙을 보여 줍니다.

[![사용자 지정 규칙](../media/create-waf-policy-ag/edit-custom-rule.png) 편집](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Waf 구성을 WAF 정책으로 마이그레이션

기존 WAF가 있는 경우 포털에서 몇 가지 변경 사항을 발견할 수 있습니다. 먼저 WAF에서 사용 하도록 설정 된 정책의 종류를 확인 해야 합니다. 다음과 같은 세 가지 잠재적 상태가 있습니다.

- WAF 정책 없음
- 사용자 지정 규칙만 정책
- WAF 정책

포털에서이를 살펴보면 WAF가 있는 상태를 확인할 수 있습니다. WAF 설정이 표시 되 고 Application Gateway 보기에서 변경할 수 있는 경우 WAF는 상태 1입니다.

[![Waf 구성](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

**웹 응용 프로그램 방화벽** 을 선택 하 고 연결 된 정책을 표시 하는 경우 waf는 상태 2 또는 상태 3입니다. 정책으로 이동한 후 사용자 지정 규칙 및 연결 된 응용 프로그램 게이트웨이 **만** 표시 되 면 사용자 지정 규칙만 정책입니다.

![WAF 사용자 지정 규칙](../media/create-waf-policy-ag/waf-custom-rules.png)

정책 설정 및 관리 규칙도 표시 되는 경우 전체 웹 응용 프로그램 방화벽 정책입니다. 

![WAF 정책 설정](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>WAF 정책으로 마이그레이션

사용자 지정 규칙만 WAF 정책만 있으면 새 WAF 정책으로 이동 하는 것이 좋습니다. 앞으로 방화벽 정책은 WAF 정책 설정, 관리 되는 규칙 집합, 제외 및 사용 하지 않는 규칙 그룹을 지원 합니다. 기본적으로 Application Gateway 내에서 이전에 수행 된 모든 WAF 구성은 이제 WAF 정책을 통해 수행 됩니다. 

사용자 지정 규칙만 WAF 정책에 대 한 편집은 사용 하지 않도록 설정 됩니다. 규칙 사용 안 함, 제외 추가 등 WAF 설정을 편집 하려면 새 최상위 방화벽 정책 리소스로 마이그레이션해야 합니다.

이렇게 하려면 *웹 응용 프로그램 방화벽 정책을* 만들고 선택한 Application Gateway 및 수신기에 연결 합니다. 이 새 정책은 현재 WAF 구성의 경우와 동일 **해야** 합니다. 즉, 모든 사용자 지정 규칙, 제외, 사용 하지 않는 규칙 등은 사용자가 만드는 새 정책에 복사 해야 합니다. Application Gateway 연결 된 정책이 있으면 WAF 규칙 및 설정을 계속 해 서 변경할 수 있습니다. Azure PowerShell를 사용 하 여이 작업을 수행할 수도 있습니다. 자세한 내용은 [WAF 정책을 기존 Application Gateway에 연결](associate-waf-policy-existing-gateway.md)을 참조 하세요.

필요에 따라 마이그레이션 스크립트를 사용 하 여 WAF 정책으로 마이그레이션할 수 있습니다. 자세한 내용은 [Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 정책 마이그레이션](migrate-policy.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](application-gateway-crs-rulegroups-rules.md)에 대해 자세히 알아보세요.

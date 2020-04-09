---
title: 응용 프로그램 게이트웨이용 웹 응용 프로그램 방화벽(WAF) 정책 만들기
description: 응용 프로그램 게이트웨이용 웹 응용 프로그램 방화벽 정책을 만드는 방법을 알아봅니다.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886928"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>응용 프로그램 게이트웨이용 웹 응용 프로그램 방화벽 정책 만들기

WAF 정책을 수신기와 연결하면 단일 WAF 뒤에 있는 여러 사이트가 서로 다른 정책에 의해 보호될 수 있습니다. 예를 들어 WAF 뒤에 5개의 사이트가 있는 경우 5개의 별도의 WAF 정책(각 수신기에 대해 하나씩)을 사용하여 다른 4개 사이트에 대해 제외, 사용자 지정 규칙 및 관리되는 규칙 집합을 사용자 지정할 수 있습니다. 단일 정책이 모든 사이트에 적용되도록 하려면 정책을 개별 리스너가 아닌 응용 프로그램 게이트웨이와 연결하여 전역으로 적용할 수 있습니다. 정책은 경로 기반 라우팅 규칙에 적용할 수도 있습니다. 

원하는 만큼 정책을 만들 수 있습니다. 정책을 만든 후에는 응용 프로그램 게이트웨이에 연결해야 적용되지만 응용 프로그램 게이트웨이와 리스너 조합에 연결할 수 있습니다. 

응용 프로그램 게이트웨이에 정책이 적용된 다음 해당 응용 프로그램 게이트웨이의 수신기에 다른 정책을 적용하는 경우 수신기의 정책은 적용되지만 해당 정책이 할당된 수신기에대해만 적용됩니다. 응용 프로그램 게이트웨이 정책은 여전히 할당된 특정 정책이 없는 다른 모든 수신기에 적용됩니다. 

   > [!NOTE]
   > 사이트별 및 URI별 WAF 정책은 공개 미리 보기로 제공됩니다. 즉, 이 기능은 Microsoft의 추가 사용 약관을 따릅니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
   > [!NOTE]
   > 방화벽 정책이 WAF에 연결되면 해당 WAF에 연결된 정책이 항상 있어야 합니다. 해당 정책을 덮어쓸 수 있지만 WAF에서 정책을 완전히 분리하는 것은 지원되지 않습니다. 

모든 새로운 웹 응용 프로그램 방화벽의 WAF 설정(사용자 지정 규칙, 관리되는 룰셋 구성, 제외 등)은 WAF 정책 내에 있습니다. 기존 WAF가 있는 경우 이러한 설정은 여전히 WAF 구성에 존재할 수 있습니다. 새 WAF 정책으로 이동하는 방법에 대한 단계는 이 문서의 후반부에서 [WAF 구성을 WAF 정책으로 마이그레이션을 참조하세요.](#migrate) 

## <a name="create-a-policy"></a>정책 만들기

먼저 Azure 포털을 사용하여 관리되는 기본 규칙 집합(DRS)을 사용하여 기본 WAF 정책을 만듭니다.

1. 포털의 왼쪽 상단에서 리소스 **만들기를 선택합니다.** **WAF**를 검색하고 **웹 응용 프로그램 방화벽을**선택한 다음 **을**선택합니다.
2. **WAF 정책** 만들기 페이지에서 **기본 탭을** 입력하거나 선택하고 나머지 설정의 기본값을 수락한 다음 **검토 + 만들기를**선택합니다.

   |설정  |값  |
   |---------|---------|
   |에 대 한 정책     |지역 WAF(애플리케이션 게이트웨이)|
   |Subscription     |구독 이름 선택|
   |Resource group     |리소스 그룹 선택|
   |정책 이름     |WAF 정책에 고유한 이름을 입력합니다.|
3. **연결** 탭에서 다음 설정 중 하나를 입력한 다음 **추가를**선택합니다.

   |설정  |값  |
   |---------|---------|
   |애플리케이션 게이트웨이 연결     |응용 프로그램 게이트웨이 프로필 이름을 선택합니다.|
   |어소시에이트 리스너     |응용 프로그램 게이트웨이 수신기의 이름을 선택한 다음 **추가 를**선택합니다.|

   > [!NOTE]
   > 이미 정책이 있는 응용 프로그램 게이트웨이(또는 리스너)에 정책을 할당하면 원래 정책이 덮어쓰여지고 새 정책으로 대체됩니다.
4. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

   ![WAF 정책 기본 사항](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF 규칙 구성(선택 사항)

WAF 정책을 만들 때 기본적으로 *검색* 모드에 있습니다. 검색 모드에서 WAF는 요청을 차단하지 않습니다. 대신 일치하는 WAF 규칙이 WAF 로그에 기록됩니다. WAF가 작동하는 것을 보려면 모드 설정을 *방지*로 변경할 수 있습니다. 방지 모드에서 선택한 CRS 규칙 집합에 정의된 일치 규칙이 WAF 로그에 차단 및/또는 기록됩니다.

## <a name="managed-rules"></a>관리 규칙

Azure 관리 OWASP 규칙은 기본적으로 활성화됩니다. 규칙 그룹 내에서 개별 규칙을 사용하지 않도록 설정하려면 해당 규칙 그룹 내의 규칙을 확장하고 규칙 번호 앞에 있는 확인란을 선택하고 위의 탭에서 **사용 안 함을** 선택합니다.

[![관리되는](../media/create-waf-policy-ag/managed-rules.png) 규칙](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>사용자 지정 규칙

사용자 지정 규칙을 만들려면 사용자 지정 **규칙** 탭에서 사용자 지정 **규칙 추가를** 선택합니다. 그러면 사용자 지정 규칙 구성 페이지가 열립니다. 다음 스크린샷은 쿼리 문자열에 텍스트 *blockme가*포함되어 있는 경우 요청을 차단하도록 구성된 사용자 지정 규칙 예제를 보여 주었습니다.

[![사용자 지정](../media/create-waf-policy-ag/edit-custom-rule.png) 규칙 편집](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>WAF 구성을 WAF 정책으로 마이그레이션

기존 WAF가 있는 경우 포털에서 일부 변경 사항이 있을 수 있습니다. 먼저 WAF에서 어떤 종류의 정책을 활성화했는지 확인해야 합니다. 세 가지 잠재적인 상태가 있습니다.

- WAF 정책 없음
- 사용자 지정 규칙 전용 정책
- WAF 정책

포털에서 WAF를 확인하여 WAF의 상태를 확인할 수 있습니다. WAF 설정이 표시되고 응용 프로그램 게이트웨이 보기 내에서 변경할 수 있는 경우 WAF가 상태 1에 있습니다.

[![WAF](../media/create-waf-policy-ag/waf-configure.png) 구성](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

**웹 응용 프로그램 방화벽을** 선택하고 관련 정책을 표시하면 WAF가 상태 2 또는 상태 3에 있습니다. 정책을 탐색한 후 사용자 지정 규칙 및 연결된 응용 프로그램 **게이트웨이만** 표시되면 사용자 지정 규칙 전용 정책입니다.

![WAF 사용자 지정 규칙](../media/create-waf-policy-ag/waf-custom-rules.png)

정책 설정 및 관리되는 규칙도 표시되면 전체 웹 응용 프로그램 방화벽 정책입니다. 

![WAF 정책 설정](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>WAF 정책으로 마이그레이션

사용자 지정 규칙만 WAF 정책이 있는 경우 새 WAF 정책으로 이동할 수 있습니다. 앞으로 방화벽 정책은 WAF 정책 설정, 관리되는 규칙 집합, 제외 및 비활성화된 규칙 그룹을 지원합니다. 기본적으로 응용 프로그램 게이트웨이 내에서 이전에 수행되었던 모든 WAF 구성은 이제 WAF 정책을 통해 수행됩니다. 

사용자 지정 규칙에 대한 편집은 WAF 정책만 사용할 수 없습니다. 규칙 사용 설정 비활성화, 제외 추가 등과 같은 WAF 설정을 편집하려면 새 최상위 방화벽 정책 리소스로 마이그레이션해야 합니다.

이렇게 하려면 웹 *응용 프로그램 방화벽 정책을* 만들고 응용 프로그램 게이트웨이 및 수신기에 연결합니다. 이 새 정책은 현재 WAF 구성과 정확히 **같아야 하며,** 이는 모든 사용자 지정 규칙, 제외, 사용 안 함 규칙 등을 만드는 새 정책에 복사해야 함을 의미합니다. 응용 프로그램 게이트웨이와 연결된 정책이 있으면 WAF 규칙 및 설정을 계속 변경할 수 있습니다. Azure PowerShell을 사용하면 이 작업을 수행할 수도 있습니다. 자세한 내용은 [WAF 정책을 기존 응용 프로그램 게이트웨이와 연결](associate-waf-policy-existing-gateway.md)참조합니다.

선택적으로 마이그레이션 스크립트를 사용하여 WAF 정책으로 마이그레이션할 수 있습니다. 자세한 내용은 Azure PowerShell 을 [사용하여 웹 응용 프로그램 방화벽 정책 마이그레이션을](migrate-policy.md)참조하세요.

## <a name="next-steps"></a>다음 단계

[웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙에](application-gateway-crs-rulegroups-rules.md)대해 자세히 알아봅니다.

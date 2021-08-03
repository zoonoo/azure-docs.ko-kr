---
title: Application Gateway용 WAF(Web Application Firewall) 정책 만들기
description: Application Gateway용 Web Application Firewall 정책을 만드는 방법에 대해 알아봅니다.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 478c2cbe35c43ffb0fdc507cc1b00585623013ea
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668545"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Application Gateway용 Web Application Firewall 정책 만들기

WAF 정책을 수신기와 연결하면 단일 WAF 뒤에 여러 사이트가 다른 정책에 의해 보호될 수 있습니다. 예를 들어 WAF 뒤에 5개의 사이트가 있는 경우 5개의 개별 WAF 정책(각 수신기에 대해 하나)을 사용하여 다른 4개에 영향을 주지 않고 하나의 사이트에 대한 제외, 사용자 지정 규칙, 관리 규칙 집합을 사용자 지정할 수 있습니다. 단일 정책을 모든 사이트에 적용하려면 개별 수신기 대신 정책을 Application Gateway와 연결하여 전역적으로 적용되도록 할 수 있습니다. 경로 기반 라우팅 규칙에도 정책을 적용할 수 있습니다. 

원하는 만큼 정책을 만들 수 있습니다. 정책을 만든 후에는 적용되도록 Application Gateway에 연결되어야 하지만 Application Gateway 및 수신기의 조합에 연결할 수 있습니다. 

Application Gateway에 정책이 적용된 경우 해당 Application Gateway의 수신기에 다른 정책을 적용하면 수신기의 정책이 적용되지만, 수신기의 정책이 할당된 수신기에 대해서만 적용됩니다. Application Gateway 정책은 특정 정책이 할당되지 않은 다른 모든 수신기에 계속 적용됩니다. 

   > [!NOTE]
   > 방화벽 정책이 WAF에 연결되면 항상 해당 WAF에 연결된 정책이 있어야 합니다. 해당 정책을 덮어쓸 수도 있지만 WAF에서 정책을 완전히 연결 해제하는 것은 지원되지 않습니다. 

모든 새 Web Application Firewall의 WAF 설정(사용자 지정 규칙, 관리형 규칙 집합 구성, 제외 등)이 WAF 정책 내에 존재합니다. 기존 WAF가 있는 경우 이러한 설정은 WAF 구성에 여전히 있을 수 있습니다. 새 WAF 정책으로 이동하는 방법에 대한 단계는 이 문서의 뒷부분에 있는 [WAF 정책으로 WAF 구성 마이그레이션](#migrate)을 참조하세요. 

## <a name="create-a-policy"></a>정책 만들기

먼저 Azure Portal을 사용하여 관리형 DRS(기본 규칙 집합)로 기본 WAF 정책을 만듭니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** 를 선택합니다. **WAF** 를 검색하여 **Web Application Firewall** 을 선택한 다음, **만들기** 를 선택합니다.
2. **WAF 정책 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택하고 나머지 설정에 대한 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.

   |설정  |값  |
   |---------|---------|
   |정책     |국가 WAF(Application Gateway)|
   |Subscription     |구독 이름 선택|
   |Resource group     |리소스 그룹 선택|
   |정책 이름     |WAF 정책의 고유한 이름을 입력합니다.|
3. **연결** 탭에서 다음 설정 중 하나를 입력한 다음, **추가** 를 선택합니다.

   |설정  |값  |
   |---------|---------|
   |Application Gateway 연결     |Application Gateway 프로필 이름을 선택합니다.|
   |수신기 연결     |Application Gateway 수신기의 이름을 선택한 다음, **추가** 를 선택합니다.|

   > [!NOTE]
   > 이미 정책이 설치되어 있는 Application Gateway(또는 수신기)에 정책을 할당하면 원래 정책을 덮어쓰고 새 정책으로 대체합니다.
4. **검토 + 생성** 를 선택한 다음, **생성** 를 선택합니다.

   ![WAF 정책 기본 사항](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF 규칙 구성(선택 사항)

WAF 정책을 만드는 경우 기본적으로 *검색* 모드입니다. 검색 모드에서는 WAF가 요청을 차단하지 않습니다  . 대신 일치 WAF 규칙이 WAF 로그에 로깅됩니다. 동작 중인 WAF를 확인하기 위해 모드 설정을 *방지* 로 변경할 수 있습니다. 방지 모드에서는 선택한 CRS 규칙 집합에 정의된 일치 규칙이 차단 및/또는 WAF 로그에 기록됩니다.

## <a name="managed-rules"></a>관리 규칙

Azure 관리 OWASP 규칙은 기본적으로 사용하도록 설정되어 있습니다. 규칙 그룹 내에 있는 개별 규칙을 사용하지 않도록 설정하려면 해당 규칙 그룹 내에서 규칙을 확장하고 규칙 번호 앞의 확인란을 선택한 다음, 위의 탭에서 **사용 안 함** 을 선택합니다.

[ ![관리 규칙](../media/create-waf-policy-ag/managed-rules.png) ](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>사용자 지정 규칙

사용자 지정 규칙을 만들려면 **사용자 지정 규칙** 탭에서 **사용자 지정 규칙 추가** 를 선택합니다. 그러면 사용자 지정 규칙 구성 페이지가 열립니다. 다음 스크린샷에서는 쿼리 문자열에 *blockme* 텍스트가 포함된 경우 요청을 차단하도록 구성된 예제 사용자 지정 규칙을 보여 줍니다.

[ ![사용자 지정 규칙 편집](../media/create-waf-policy-ag/edit-custom-rule.png) ](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>WAF 정책으로 WAF 구성 마이그레이션

기존 WAF가 있는 경우 포털에서 몇 가지 변경 사항을 발견할 수 있습니다. 먼저 WAF에서 사용하도록 설정된 정책의 종류를 확인해야 합니다. 다음과 같은 세 가지 잠재적 상태가 있습니다.

- WAF 정책 없음
- 사용자 지정 규칙 전용 정책
- WAF 정책

포털에서 이를 살펴보면 WAF의 상태를 확인할 수 있습니다. WAF 설정이 표시되고 Application Gateway 보기에서 변경할 수 있는 경우 WAF는 상태 1입니다.

[ ![WAF 구성](../media/create-waf-policy-ag/waf-configure.png) ](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

**Web Application Firewall** 을 선택하고 연결된 정책을 표시하는 경우 WAF는 상태 2 또는 상태 3입니다. 정책으로 이동한 후 사용자 지정 규칙 및 연결된 Application Gateway **만** 표시되면 사용자 지정 규칙 전용 정책입니다.

![WAF 사용자 지정 규칙](../media/create-waf-policy-ag/waf-custom-rules.png)

정책 설정 및 관리 규칙도 표시되는 경우 전체 Web Application Firewall 정책입니다. 

![WAF 정책 설정](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>WAF 정책으로 마이그레이션

사용자 지정 규칙 전용 WAF 정책이 있으면 새 WAF 정책으로 이동하는 것이 좋습니다. 앞으로 방화벽 정책은 WAF 정책 설정, 관리 규칙 집합, 제외 및 사용하지 않는 규칙 그룹을 지원합니다. 기본적으로 Application Gateway 내에서 이전에 수행된 모든 WAF 구성은 이제 WAF 정책을 통해 수행됩니다. 

사용자 지정 규칙 전용 WAF 정책에 대한 편집은 사용하지 않도록 설정됩니다. 규칙 사용 안 함, 제외 추가 등의 WAF 설정을 편집하려면 새 최상위 방화벽 정책 리소스로 마이그레이션해야 합니다.

이렇게 하려면 *Web Application Firewall 정책* 을 만들고 선택한 Application Gateway 및 수신기에 연결합니다. 이 새 정책은 현재 WAF 구성의 경우와 동일해야 합니다. 즉, 모든 사용자 지정 규칙, 제외, 사용하지 않는 규칙 등은 사용자가 만드는 새 정책에 복사해야 합니다. Application Gateway에 연결된 정책이 있으면 WAF 규칙 및 설정을 계속해서 변경할 수 있습니다. Azure PowerShell을 사용하여 이 작업을 수행할 수도 있습니다. 자세한 내용은 [기존 Application Gateway에 WAF 정책 연결](associate-waf-policy-existing-gateway.md)을 참조하세요.

필요에 따라 마이그레이션 스크립트를 사용하여 WAF 정책으로 마이그레이션할 수 있습니다. 자세한 내용은 [Azure PowerShell을 사용하여 Web Application Firewall 정책 마이그레이션](migrate-policy.md)을 참조하세요.

## <a name="force-mode"></a>강제 모드

현재 구성에 정확히 일치하는 정책으로 모든 항목을 복사하지 않으려는 경우 WAF를 "강제" 모드로 설정할 수 있습니다. 다음 Azure PowerShell 코드를 실행하면 WAF가 강제 모드로 전환됩니다. 그런 다음, 구성에 정확히 동일한 설정이 없더라도 WAF 정책을 WAF에 연결할 수 있습니다. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

그런 다음, 애플리케이션 게이트웨이에 WAF 정책을 연결하는 단계를 수행합니다. 자세한 내용은 [기존 Application Gateway에 WAF 정책 연결](associate-waf-policy-existing-gateway.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Web Application Firewall CRS 규칙 그룹 및 규칙](application-gateway-crs-rulegroups-rules.md)에 대해 자세히 알아보세요.

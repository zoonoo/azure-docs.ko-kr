---
title: Azure Front 도어를 사용 하 여 WAF (웹 응용 프로그램 방화벽)에 대 한 사용자 지정 응답 구성
description: WAF가 요청을 차단할 때 사용자 지정 응답 코드와 메시지를 구성 하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: a995460793686d8293d77965e74e2cbf916925a0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005602"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Azure WAF (웹 응용 프로그램 방화벽)에 대 한 사용자 지정 응답 구성

기본적으로 WAF는 일치 하는 규칙으로 인해 요청을 차단할 때 **요청이 차단** 됨 메시지를 포함 하는 403 상태 코드를 반환 합니다. 기본 메시지는 또한 요청에 대 한 [로그 항목](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor) 에 연결 하는 데 사용할 수 있는 추적 참조 문자열을 포함 합니다.  사용 사례에 대 한 참조 문자열을 사용 하 여 사용자 지정 응답 상태 코드와 사용자 지정 메시지를 구성할 수 있습니다. 이 문서에서는 WAF에 의해 요청이 차단 될 때 사용자 지정 응답 페이지를 구성 하는 방법을 설명 합니다.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>사용자 지정 응답 상태 코드 및 메시지 사용 포털 구성

WAF 포털의 "정책 설정" 아래에서 사용자 지정 응답 상태 코드 및 본문을 구성할 수 있습니다.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF 정책 설정":::

위의 예제에서 응답 코드를 403으로 유지 하 고 아래 이미지에 나와 있는 것 처럼 짧은 "문의처" 메시지를 구성 했습니다.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="사용자 지정 응답 예제":::

"{{azure-ref}}"는 응답 본문에 고유한 참조 문자열을 삽입 합니다. 값은 및 로그의 TrackingReference 필드와 일치 합니다 `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` .

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>사용자 지정 응답 상태 코드 및 메시지 사용 PowerShell 구성

### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인용 대화형 대화 상자를 사용하여 Azure에 연결

```
Connect-AzAccount
Install-Module -Name Az

```
현재 버전의 PowerShellGet이 설치되어 있는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 여기서는 [AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)를 사용 하 여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>사용자 지정 응답을 사용 하 여 새 WAF 정책 만들기 

다음은 사용자 지정 응답 상태 코드를 405로 설정 하 여 새 WAF 정책을 만들고 사용자에 게 메시지를 표시 하는 예입니다. [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 를 사용 **합니다.**

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

[AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)를 사용 하 여 기존 waf 정책의 사용자 지정 응답 코드 또는 응답 본문 설정을 수정 합니다.

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>다음 단계
- [Azure Front 도어를 사용 하 여 웹 응용 프로그램 방화벽](../afds/afds-overview.md) 에 대 한 자세한 정보
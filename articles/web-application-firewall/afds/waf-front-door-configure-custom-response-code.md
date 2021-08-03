---
title: Azure Front Door의 WAF(Web Application Firewall)에 대한 사용자 지정 응답 구성
description: WAF가 요청을 차단할 때 사용자 지정 응답 코드 및 메시지를 구성하는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ec105a5545a207ebee343ea6af5fb26995b25b5
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671809"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>WAF(Azure Web Application Firewall)에 대한 사용자 지정 응답 구성

기본적으로 WAF는 일치하는 규칙으로 인해 요청을 차단할 경우, **요청이 차단됨** 이라는 메시지와 함께 403 상태 코드를 반환합니다. 기본 메시지에는 요청에 대한 [로그 항목](./waf-front-door-monitor.md)에 연결하는 데 사용할 수 있는 추적 참조 문자열도 포함됩니다.  사용 사례에 대한 참조 문자열을 사용하여 사용자 지정 응답 상태 코드 및 사용자 지정 메시지를 구성할 수 있습니다. 이 문서에서는 WAF에 의해 요청이 차단될 때 사용자 지정 응답 페이지를 구성하는 방법을 설명합니다.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>사용자 지정 응답 상태 코드 및 메시지 사용 포털 구성

WAF 포털의 “정책 설정”에서 사용자 지정 응답 상태 코드 및 본문을 구성할 수 있습니다.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF 정책 설정":::

위의 예제에서는 응답 코드를 403으로 유지하고 아래 이미지와 같이 짧은 “Please contact us” 메시지를 구성했습니다.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="사용자 지정 응답 예제":::

“{{azure-ref}}”는 응답 본문에 고유한 참조 문자열을 삽입합니다. 값은 `FrontdoorAccessLog` 및 `FrontdoorWebApplicationFirewallLog` 로그의 TrackingReference 필드와 일치합니다.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>PowerShell을 사용하여 사용자 지정 응답 상태 코드 및 메시지 구성

### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](/powershell/azure/) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

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

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 여기서는 [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>사용자 지정 응답을 사용하여 새 WAF 정책 만들기 

다음은 사용자 지정 응답 상태 코드가 405로 설정된 새 WAF 정책을 만들고 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 **You are blocked.** 라는 메시지를 만드는 예제입니다.

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

[Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)를 사용하여 기존 WAF 정책의 사용자 지정 응답 코드 또는 응답 본문 설정을 수정합니다.

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
- [Azure Front Door의 WAF(Web Application Firewall)](../afds/afds-overview.md)에 대해 자세히 알아보기

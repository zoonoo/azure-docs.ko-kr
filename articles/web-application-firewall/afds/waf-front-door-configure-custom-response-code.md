---
title: Azure 정문으로 WAF에 대한 사용자 지정 응답 구성
description: WAF(웹 응용 프로그램 방화벽)가 요청을 차단할 때 사용자 지정 응답 코드 및 메시지를 구성하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185337"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽에 대한 사용자 지정 응답 구성

기본적으로 Azure Front Door를 사용하는 WAF(Azure Web Application Firewall)가 일치하는 규칙으로 인해 요청을 차단하면 **요청이 차단된** 메시지와 함께 403 상태 코드를 반환합니다. 이 문서에서는 WAF에서 요청을 차단할 때 사용자 지정 응답 상태 코드 및 응답 메시지를 구성하는 방법을 설명합니다.

## <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

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

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 이 예제에서는 [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>사용자 지정 응답으로 새 WAF 정책 만들기 

다음은 사용자 지정 응답 상태 코드가 405로 설정되어 있고 사용자에게 메시지가 차단된 새 WAF 정책을 만드는 **예제입니다.** [새 AzFrontDoorWaf 정책을](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)사용 하 여 .

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

[Update-AzFrontDoorFireWallPolicy를](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)사용하여 기존 WAF 정책의 사용자 지정 응답 코드 또는 응답 본문 설정을 수정합니다.

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
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>다음 단계
- [Azure 정문이 있는 웹 응용 프로그램 방화벽에](../afds/afds-overview.md) 대해 자세히 알아보기
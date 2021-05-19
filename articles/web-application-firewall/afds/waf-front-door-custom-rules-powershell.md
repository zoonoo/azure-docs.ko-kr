---
title: Azure Front Door에 대한 WAF 사용자 지정 규칙 및 기본 규칙 집합 구성
description: 기존 Front Door 엔드포인트에 대한 사용자 지정 규칙과 관리되는 규칙으로 구성된 WAF 정책을 구성하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563412"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell을 사용하여 Web Application Firewall 정책 구성

Azure WAF(Web Application Firewall) 정책은 요청이 Front Door에 도착할 때 필요한 검사를 정의합니다.
이 문서에서는 Azure 관리 기본 규칙 집합이 사용하도록 설정된 일부 사용자 지정 규칙으로 구성된 WAF 정책을 구성하는 방법을 보여 줍니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

속도 제한 정책을 설정하기 전에 PowerShell 환경을 설정하고 Front Door 프로필을 만듭니다.

### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](/powershell/azure/) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

#### <a name="sign-in-to-azure"></a>Azure에 로그인

```
Connect-AzAccount

```
Front Door 모듈을 설치하기 전에 현재 버전의 PowerShellGet을 설치했는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기

[빠른 시작: Front Door 프로필 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다.

## <a name="custom-rule-based-on-http-parameters"></a>http 매개 변수를 기반으로 하는 사용자 지정 규칙

다음 예제에서는 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용하여 두 개의 일치 조건으로 사용자 지정 규칙을 구성하는 방법을 보여 줍니다. 요청은 참조 페이지에 정의된 대로 지정된 사이트에서 온 것이며 쿼리 문자열에는 "password"가 포함되지 않습니다. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>http 요청 메서드를 기반으로 하는 사용자 지정 규칙

다음과 같이 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)를 사용하여 "PUT" 메서드를 차단하는 규칙을 만듭니다.

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>크기 제약 조건을 기반으로 사용자 지정 규칙 만들기

다음 예제에서는 Azure PowerShell을 사용하여 URL이 100자를 초과하는 규칙 차단 요청을 만듭니다.
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>관리되는 기본 규칙 집합 추가

다음 예에서는 Azure PowerShell을 사용하여 관리되는 기본 규칙 집합을 만듭니다.
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, Front Door 프로필이 포함된 지정된 리소스 그룹에서 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 이전 단계에서 만들어진 규칙으로 보안 정책을 구성합니다.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Front Door 프런트 엔드 호스트에 정책 연결

기존 Front Door 프런트 엔드 호스트에 보안 정책 개체를 연결하고 Front Door 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용하여 Front Door 개체를 검색합니다.
그런 다음, [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)를 사용하여 이전 단계에서 만든 "$myWAFPolicy$"의 *resourceId* 로 프런트 엔드 *WebApplicationFirewallPolicyLink* 속성을 설정합니다. 

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1* 을 사용합니다. [빠른 시작: Front Door 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다. 또한 아래 예제에서 $frontDoorName을 Front Door 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 보안 정책을 Front Door 프런트 엔드에 연결하려면 *WebApplicationFirewallPolicyLink* 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door](../../frontdoor/front-door-overview.md)에 대한 자세한 정보 
- [Front Door를 사용하는 WAF](afds-overview.md)에 대해 자세히 알아봅니다.
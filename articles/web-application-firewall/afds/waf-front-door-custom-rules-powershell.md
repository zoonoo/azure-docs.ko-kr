---
title: Azure 정문& 기본 규칙 집합을 & WAF 사용자 지정 규칙 구성
description: WAF 정책을 구성하는 방법은 기존 프런트 도어 엔드포인트에 대한 사용자 지정 및 관리 규칙으로 구성됩니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186643"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell을 사용하여 웹 응용 프로그램 방화벽 정책 구성

Azure 웹 응용 프로그램 방화벽(WAF) 정책은 요청이 정문에 도착할 때 필요한 검사를 정의합니다.
이 문서에서는 일부 사용자 지정 규칙으로 구성되고 Azure 관리 기본 규칙 집합을 사용하도록 설정한 WAF 정책을 구성하는 방법을 보여 주십습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

속도 제한 정책을 설정하기 전에 PowerShell 환경을 설정하고 전면 도어 프로필을 만듭니다.

### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

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

퀵스타트에 설명된 지침에 따라 [정문 프로파일 만들기: 전면 도어 프로파일 만들기](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>http 매개 변수를 기반으로 하는 사용자 지정 규칙

다음 예제에서는 [New-AzFrontDoorWafMatchConditionObject를](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)사용하여 두 개의 일치 조건으로 사용자 지정 규칙을 구성하는 방법을 보여 주십습니다. 요청은 참조자가 정의한 지정된 사이트에서 온 것이며 쿼리 문자열에는 "암호"가 포함되어 있지 않습니다. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>http 요청 메서드를 기반으로 하는 사용자 지정 규칙

다음과 같이 [New-AzFrontDoorWaf CustomRuleObject를](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 사용하여 "PUT" 메서드를 차단하는 규칙을 만듭니다.

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>크기 제약 조건에 따라 사용자 지정 규칙 만들기

다음 예제는 Azure PowerShell을 사용하여 100자 보다 긴 Url을 사용하여 규칙을 차단하는 요청을 만듭니다.
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>관리되는 기본 규칙 집합 추가

다음 예제는 Azure PowerShell을 사용하여 관리되는 기본 규칙 집합을 만듭니다.
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음 정문 프로필이 포함된 지정된 리소스 그룹에 [New-AzFrontDoorWafPolicy를](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 사용하여 이전 단계에서 생성된 규칙으로 보안 정책을 구성합니다.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>프런트 도어 프런트 엔드 호스트에 정책 연결

보안 정책 개체를 기존 프런트 도어 프런트 엔드 호스트에 연결하고 정문 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor를](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)사용하여 정문 개체를 검색합니다.
다음으로, 프런트 엔드 *WebApplicationFirewallPolicy* 속성을 [Set-AzFrontDoor를](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)사용하여 이전 단계에서 만든 "$myWAFPolicy$"의 *resourceId로* 설정합니다. 

아래 예제에서는 [빠른 시작: 정문](../../frontdoor/quickstart-create-front-door.md) 문서 만들기에 제공된 지침을 사용하여 정문 프로파일을 만들었다는 가정 하에 리소스 그룹 이름 *myResourceGroupFD1을* 사용합니다. 또한 아래 예제에서는 $frontDoorName 정문 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 보안 정책을 프런트 도어 프런트 엔드에 연결하려면 *WebApplicationFirewallPolicy* 속성을 한 번만 설정하면 됩니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [정문에](../../frontdoor/front-door-overview.md) 대해 자세히 알아보기 
- [현관문이 있는 WAF에](afds-overview.md) 대해 자세히 알아보기

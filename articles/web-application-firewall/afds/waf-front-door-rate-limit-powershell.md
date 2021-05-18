---
title: Front Door에 대한 WAF 속도 제한 규칙 구성 - Azure PowerShell
description: 기존 Front Door 엔드포인트에 대한 속도 제한 규칙을 구성하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 23b893bad591af5f1e923b68e8d30453f859792b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563480"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Azure PowerShell을 사용하여 웹 애플리케이션 방화벽 속도 제한 규칙 구성
Azure Front Door의 Azure WAF(Web Application Firewall)는 1분 동안 클라이언트에서 허용되는 요청 수를 제어합니다.
이 문서에서는 Azure PowerShell을 사용하여 클라이언트에서 URL에 */promo* 가 포함된 웹 애플리케이션으로 허용된 요청 수를 제어하는 WAF 속도 제한 규칙을 구성하는 방법을 보여 줍니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> 각 클라이언트 IP 주소에 대해 속도 제한이 적용됩니다. 서로 다른 IP 주소에서 Front Door에 액세스하는 여러 클라이언트가 있는 경우 자체의 속도 제한이 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항
속도 제한 정책을 설정하기 전에 PowerShell 환경을 설정하고 Front Door 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](/powershell/azure/) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인용 대화형 대화 상자를 사용하여 Azure에 연결
```
Connect-AzAccount

```
Front Door 모듈을 설치하기 전에 현재 버전의 PowerShellGet을 설치했는지 확인합니다. 다음 명령을 실행하고 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기
[빠른 시작: Front Door 프로필 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다.

## <a name="define-url-match-conditions"></a>URL 일치 조건 정의
[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용하여 URL 일치 조건(URL에 /promo가 포함됨)을 정의합니다.
다음 예제에서는 */promo* 를 *RequestUri* 변수의 값으로 일치시킵니다.

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>사용자 지정 속도 제한 규칙 만들기
[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)를 사용하여 속도 제한을 설정합니다. 다음 예제에서는 제한이 1000으로 설정됩니다. 클라이언트에서 1분 동안 1000을 초과하는 프로모션 페이지로의 요청은 다음 분이 시작될 때까지 차단됩니다.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzureRmResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, Front Door 프로필이 포함된 지정된 리소스 그룹에서 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 사용자 지정 속도 제한 규칙으로 보안 정책을 구성합니다.

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1* 을 사용합니다. [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 [빠른 시작: Front Door 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Front Door 프런트 엔드 호스트에 정책 연결
기존 Front Door 프런트 엔드 호스트에 보안 정책 개체를 연결하고 Front Door 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 명령을 사용하여 Front Door 개체를 검색합니다.
그런 다음, [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용하여 이전 단계에서 만든 "$ratePolicy"의 *resourceId* 로 프런트 엔드 *WebApplicationFirewallPolicyLink* 속성을 설정합니다. 

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1* 을 사용합니다. [빠른 시작: Front Door 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다. 또한 아래 예제에서 $frontDoorName을 Front Door 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 보안 정책을 Front Door 프런트 엔드에 연결하려면 *WebApplicationFirewallPolicyLink* 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door](../../frontdoor/front-door-overview.md)에 대해 자세히 알아봅니다.
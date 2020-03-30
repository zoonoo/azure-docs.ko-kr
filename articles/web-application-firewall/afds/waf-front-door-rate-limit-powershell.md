---
title: 전면 도어에 대한 WAF 속도 제한 규칙 구성 - Azure PowerShell
description: 기존 정문 끝점에 대한 속도 제한 규칙을 구성하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649367"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Azure PowerShell을 사용하여 웹 응용 프로그램 방화벽 속도 제한 규칙 구성
Azure Front Door에 대한 WAF(Azure 웹 응용 프로그램 방화벽) 속도 제한 규칙은 1분 동안 클라이언트에서 허용되는 요청 수를 제어합니다.
이 문서에서는 Azure PowerShell을 사용하여 URL에 */promo를* 포함하는 웹 응용 프로그램에 클라이언트에서 허용되는 요청 수를 제어하는 WAF 속도 제한 규칙을 구성하는 방법을 보여 주십습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
속도 제한 정책을 설정하기 전에 PowerShell 환경을 설정하고 전면 도어 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인을 위한 대화형 대화 상자가 있는 Azure에 연결
```
Connect-AzAccount

```
전면 도어 모듈을 설치하기 전에 PowerShellGet의 현재 버전이 설치되어 있는지 확인하십시오. 다음 명령을 실행하고 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기
퀵스타트에 설명된 지침에 따라 [정문 프로파일 만들기: 전면 도어 프로파일 만들기](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>URL 일치 조건 정의
[New-AzFrontDoorWafMatchConditionObject를](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)사용하여 URL 일치 조건(URL 포함 /promo)을 정의합니다.
다음 예제는 *RequestUri* 변수의 값으로 */promo와* 일치합니다.

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>사용자 지정 속도 제한 규칙 만들기
[새 AzFrontDoorWaf 사용자 정의 규칙 개체를](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)사용하여 속도 제한을 설정합니다. 다음 예제에서는 제한이 1000으로 설정됩니다. 1분 동안 1,000개를 초과하는 모든 클라이언트에서 다음 분이 시작될 때까지 요청이 차단됩니다.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzureRmResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음 정문 프로필이 포함된 지정된 리소스 그룹에 [New-AzFrontDoorWafPolicy를](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 사용하여 사용자 지정 속도 제한 규칙으로 보안 정책을 구성합니다.

아래 예제에서는 [빠른 시작: 정문](../../frontdoor/quickstart-create-front-door.md) 문서 만들기에 제공된 지침을 사용하여 정문 프로파일을 만들었다는 가정 하에 리소스 그룹 이름 *myResourceGroupFD1을* 사용합니다.

 [새 AzFrontDoorWaf 정책을](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)사용 하 여 .

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>프런트 도어 프런트 엔드 호스트에 정책 연결
보안 정책 개체를 기존 프런트 도어 프런트 엔드 호스트에 연결하고 정문 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 명령을 사용하여 정문 오브젝트를 검색합니다.
다음으로, [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용하여 이전 단계에서 만든 "$ratePolicy"의 *resourceId에* 프런트 엔드 *WebApplicationFirewallPolicy* 속성을 설정합니다. 

아래 예제에서는 [빠른 시작: 정문](../../frontdoor/quickstart-create-front-door.md) 문서 만들기에 제공된 지침을 사용하여 정문 프로파일을 만들었다는 가정 하에 리소스 그룹 이름 *myResourceGroupFD1을* 사용합니다. 또한 아래 예제에서는 $frontDoorName 정문 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 보안 정책을 프런트 도어 프런트 엔드에 연결하려면 *WebApplicationFirewallPolicy* 속성을 한 번만 설정하면 됩니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [정문에](../../frontdoor/front-door-overview.md)대해 자세히 알아보십시오. 



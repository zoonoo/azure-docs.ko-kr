---
title: Azure Front 도어 서비스에 대 한 지역 필터링 웹 응용 프로그램 방화벽 정책 구성
description: 이 자습서에서는 지역 필터링 정책을 만들고 정책을 기존 Front 도어가 프런트 엔드 호스트와 연결 하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79135873"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>프런트 도어의 지역 필터링 WAF 정책 설정

이 자습서에서는 Azure PowerShell을 사용하여 간단한 지역 필터링 정책을 만들고 기존 Front Door 프런트 엔드 호스트에 연결하는 방법을 알아봅니다. 이 샘플 지역 필터링 정책은 미국을 제외한 모든 국가/지역으로부터의 요청을 차단합니다.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

## <a name="prerequisites"></a>전제 조건

지역 필터링 정책을 설정하기 전에 PowerShell 환경을 설정하고 Front Door 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인을 위해 대화형 대화 상자를 사용 하 여 Azure에 연결

```
Install-Module -Name Az
Connect-AzAccount
```
현재 버전의 PowerShellGet이 설치되어 있는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기

[빠른 시작: Front 도어 프로필 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명 된 지침에 따라 front 도어 프로필을 만듭니다.

## <a name="define-geo-filtering-match-condition"></a>지역 필터링 일치 조건 정의

일치 조건을 만들 때 매개 변수에 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용하여 "US"에서 발생하지 않는 요청을 선택하는 샘플 일치 조건을 만듭니다. 두 자로 된 국가에 [대 한 국가 매핑은 Azure Front 도어의 도메인에서 지역 필터링 이란?](waf-front-door-geo-filtering.md)에 제공 됩니다.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>작업 및 우선 순위를 통해 지역 필터링 일치 조건을 규칙에 추가

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)를 사용하여 일치 조건, 작업, 우선 순위에 따라 CustomRule 개체(`nonUSBlockRule`)를 만듭니다.  CustomRule은 여러 MatchCondition을 가질 수 있습니다.  이 예제에서는 작업이 가장 우선 순위가 높은 차단 및 우선 순위 1로 설정됩니다.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>정책에 규칙 추가

`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, Front Door 프로필을 포함하는 지정된 리소스 그룹에 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 `nonUSBlockRule`이 포함된 `geoPolicy` 정책 개체를 만듭니다. 지역 정책에 고유한 이름을 제공해야 합니다. 

다음 예제에서는 [빠른 시작: 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공 된 지침을 사용 하 여 front 도어 프로필을 만들었다고 가정 하 고 리소스 그룹 이름 *myResourceGroupFD1* 를 사용 합니다. 아래 예제에서 정책 이름 *geoPolicyAllowUSOnly*를 고유한 정책 이름으로 바꿉니다.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Front Door 호스트에 WAF 정책 연결

기존 Front Door 프런트 엔드 호스트에 WAF 정책 개체를 연결하고 Front Door 속성을 업데이트합니다. 

이렇게 하려면 먼저 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor)를 사용하여 Front Door 개체를 검색합니다. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

다음으로, [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)를 사용하여 프런트 엔드 WebApplicationFirewallPolicyLink 속성을 `geoPolicy`의 resourceId로 설정합니다.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> WAF 정책을 Front Door 프런트 엔드 호스트에 연결하려면 WebApplicationFirewallPolicyLink 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드 호스트에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](../overview.md)에 대해 알아봅니다.
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.

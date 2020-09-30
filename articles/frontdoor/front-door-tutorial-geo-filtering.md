---
title: 자습서 - 지역 필터링 WAF 정책 구성 - Azure Front Door
description: 이 자습서에서는 지역 필터링 정책을 만들고 기존 Front Door 프런트 엔드 호스트에 연결하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 20aa038e15b1ae5734ad6f463c6f450368617119
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090036"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>자습서: Front Door에 대한 지역 필터링 WAF 정책을 설정하는 방법
이 자습서에서는 Azure PowerShell을 사용하여 간단한 지역 필터링 정책을 만들고 기존 Front Door 프런트 엔드 호스트에 연결하는 방법을 알아봅니다. 이 샘플 지역 필터링 정책은 미국을 제외한 모든 국가/지역으로부터의 요청을 차단합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 지역 필터링 일치 조건을 정의합니다.
> - 규칙에 지역 필터링 일치 조건을 추가합니다.
> - 정책에 규칙을 추가합니다.
> - WAF 정책을 FrontDoor 프런트 엔드 호스트에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항
* 지역 필터링 정책을 설정하기 전에 PowerShell 환경을 설정하고 Front Door 프로필을 만듭니다.
* [빠른 시작: Front Door 프로필 만들기](quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다.

## <a name="define-geo-filtering-match-condition"></a>지역 필터링 일치 조건 정의

일치 조건을 만들 때 매개 변수에 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용하여 "US"에서 발생하지 않는 요청을 선택하는 샘플 일치 조건을 만듭니다. 국가/지역 매핑에 대한 두 문자로 된 국가/지역 코드는 [여기](front-door-geo-filtering.md)에 제공됩니다.

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
`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, Front Door 프로필을 포함하는 지정된 리소스 그룹에 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 `nonUSBlockRule`이 포함된 `geoPolicy` 정책 개체를 만듭니다. 지역 필터링 정책에 고유한 이름을 제공해야 합니다. 

아래 예제에서는 리소스 그룹 이름으로 *FrontDoorQS_rg0*을 사용합니다. [빠른 시작: Front Door 만들기](quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다. 아래 예제에서 정책 이름 *geoPolicyAllowUSOnly*를 고유한 정책 이름으로 바꿉니다.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Front Door 호스트에 WAF 정책 연결
기존 Front Door 프런트 엔드 호스트에 WAF 정책 개체를 연결하고 Front Door 속성을 업데이트합니다. 

이렇게 하려면 먼저 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor)를 사용하여 Front Door 개체를 검색합니다. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
다음으로, [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)를 사용하여 프런트 엔드 WebApplicationFirewallPolicyLink 속성을 `geoPolicy`의 resourceId로 설정합니다.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> WAF 정책을 Front Door 프런트 엔드 호스트에 연결하려면 WebApplicationFirewallPolicyLink 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드 호스트에 자동으로 적용됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 WAF 정책과 연결된 지역 필터링 규칙을 구성했습니다. 그런 다음, 정책을 Front Door의 프런트 엔드 호스트에 연결했습니다. 지역 필터링 규칙 또는 WAF 정책이 더 이상 필요하지 않은 경우 WAF 정책을 삭제하기 전에 먼저 프런트 엔드 호스트에서 정책 연결을 해제해야 합니다.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="WAF 정책 연결 해제":::

## <a name="next-steps"></a>다음 단계

Front Door용 Web Application Firewall을 구성하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [웹 애플리케이션 방화벽 및 Front Door](front-door-waf.md)

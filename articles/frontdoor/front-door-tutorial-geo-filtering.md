---
title: 자습서 - Azure Front Door Service용 도메인에지역 필터링 HTTPS 구성 | Microsoft Docs
description: 이 자습서에서는 간단한 지역 필터링 정책을 만들고 기존 Front Door 프런트 엔드 호스트에 연결하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: c4032f7c33cec7b7a7864ccff07a05b87c945949
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988604"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Front Door에 대한 지역 필터링 정책 설정 방법
이 자습서에서는 Azure PowerShell을 사용하여 간단한 지역 필터링 정책을 만들고 기존 Front Door 프런트 엔드 호스트에 연결하는 방법을 알아봅니다. 이 샘플 지역 필터링 정책은 미국을 제외한 모든 국가로부터의 요청을 차단합니다.

## <a name="1-set-up-your-powershell-environment"></a>1. PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 AzureRM을 설치합니다.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 지원이 곧 제공됩니다.

Front Door 모듈을 설치하기 전에 현재 버전의 PowerShellGet을 설치했는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

AzureRM.FrontDoor 모듈을 설치합니다. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. 지역 필터링 일치 조건 정의
먼저 “미국”에서 오지 않는 요청을 선택하는 샘플 일치 조건을 만듭니다. 일치 조건을 만들 때 매개 변수는 PowerShell [가이드](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject)를 참조하세요. 국가와 2자 국가 코드 매핑은 [여기](/Protection/GeoFiltering)에 있습니다.

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. 작업 및 우선 순위를 통해 지역 필터링 일치 조건을 규칙에 추가

그런 다음, 일치 조건, 작업, 우선 순위에 따라 CustomRule 개체(`nonUSBlockRule`)를 만듭니다.  CustomRule은 여러 MatchCondition을 가질 수 있습니다.  이 예제에서는 작업이 가장 우선 순위가 높은 차단 및 우선 순위 1로 설정됩니다.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

CustomRuleObject를 만들 때 매개 변수는 PowerShell [가이드](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject)를 참조하세요.

## <a name="4-add-rules-to-a-policy"></a>4. 정책에 규칙 추가
이 단계에서는 지정된 리소스 그룹에서 이전 단계의 `nonUSBlockRule`을 포함하는 `geoPolicy` 정책 개체를 만듭니다. `Get-AzureRmResourceGroup`을 사용하여 ResourceGroupName $resourceGroup을 찾습니다.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

정책을 만들 때 매개 변수는 PowerShell [가이드](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy)를 참조하세요.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. 정책을 첫 번째 Front Door 호스트에 연결
마지막 단계는 보호 정책 개체를 기존 Front Door 프런트엔드 호스트에 연결하고 Front Door 속성을 업데이트하는 것입니다. 먼저 [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor)를 사용하여 Front Door 개체를 검색한 다음, 프런트 엔드 WebApplicationFirewallPolicyLink 속성을 `geoPolicy`의 resourceId로 설정합니다.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

다음 [명령](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor)을 사용하여 Front Door 개체를 업데이트합니다.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 보호 정책을 Front Door 프런트엔드 호스트에 연결하려면 WebApplicationFirewallPolicyLink 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드 호스트에 자동으로 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door와 응용 프로그램 계층 보안](front-door-application-security.md)에 대해 알아 보세요.
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.

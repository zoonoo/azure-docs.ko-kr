---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는 Azure PowerShell을 사용하여 Azure 응용 프로그램 게이트웨이에서 HTTP 헤더를 다시 작성하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947197"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure 응용 프로그램 게이트웨이 - Azure PowerShell을 사용 하 고 HTTP 요청 및 응답 헤더 다시 작성

이 문서에서는 Azure PowerShell을 사용하여 요청 및 응답에서 HTTP 헤더를 다시 작성하도록 [응용 프로그램 게이트웨이 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 인스턴스를 구성하는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

- 이 문서의 단계를 완료하려면 Azure PowerShell을 로컬에서 실행해야 합니다. 또한 Az 모듈 버전 1.0.0 이상에 설치해야 합니다. 실행한 `Import-Module Az` `Get-Module Az` 다음 설치한 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.
- 응용 프로그램 게이트웨이 v2 SKU 인스턴스가 있어야 합니다. v1 SKU에서는 헤더 를 다시 작성할 수 없습니다. v2 SKU가 없는 경우 시작하기 전에 [응용 프로그램 게이트웨이 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 재작성을 구성하려면 다음 단계를 완료해야 합니다.

1. HTTP 헤더 다시 작성하는 데 필요한 개체를 만듭니다.

   - **요청헤더구성**: 다시 작성할 요청 헤더 필드와 헤더에 대한 새 값을 지정하는 데 사용됩니다.

   - **ResponseHeaderConfiguration**: 다시 작성하려는 응답 헤더 필드와 헤더에 대한 새 값을 지정하는 데 사용됩니다.

   - **작업 집합**: 이전에 지정한 요청 및 응답 헤더의 구성을 포함합니다.

   - **조건**: 선택적 구성입니다. 다시 작성 조건은 HTTP(S) 요청 및 응답의 내용을 평가합니다. HTTP(S) 요청 또는 응답이 재작성 조건과 일치하는 경우 다시 쓰기 작업이 수행됩니다.

     둘 이상의 조건을 작업과 연결하는 경우 모든 조건이 충족될 때만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

   - **다시 쓰기규칙**: 여러 재쓰기 작업 / 다시 쓰기 조건 조합을 포함합니다.

   - **RuleSequence**: 다시 작성 규칙이 실행되는 순서를 결정하는 데 도움이 되는 선택적 구성입니다. 이 구성은 다시 쓰기 집합에 여러 번 다시 쓰기 규칙이 있는 경우에 유용합니다. 규칙 시퀀스 값이 낮은 다시 쓰기 규칙이 먼저 실행됩니다. 동일한 규칙 시퀀스 값을 두 개의 다시 쓰기 규칙에 할당하는 경우 실행 순서는 결정적이지 않습니다.

     RuleSequence를 명시적으로 지정하지 않으면 기본값 100이 설정됩니다.

   - **다시 쓰기규칙 집합**: 요청 라우팅 규칙에 연결될 여러 재작성 규칙을 포함합니다.

2. 라우팅 규칙에 다시 쓰기규칙 집합을 연결합니다. 재작성 구성은 라우팅 규칙을 통해 소스 수신기에 연결됩니다. 기본 라우팅 규칙을 사용하는 경우 헤더 재작성 구성은 원본 수신기와 연결되며 전역 헤더 다시 작성입니다. 경로 기반 라우팅 규칙을 사용하는 경우 헤더 재작성 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다.

여러 HTTP 헤더 다시 쓰기 집합을 만들고 각 재쓰기 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 하나의 재쓰기 집합만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP 헤더 재작성 규칙 구성 지정

이 예제에서는 위치 헤더에 azurewebsites.net 대한 참조가 있을 때마다 HTTP 응답에서 위치 헤더를 다시 작성하여 리디렉션 URL을 수정합니다. 이렇게 하려면 응답의 위치 헤더에 azurewebsites.net 포함되어 있는지 여부를 평가하는 조건을 추가합니다. 우리는 패턴을 `(https?):\/\/.*azurewebsites\.net(.*)$`사용합니다. 그리고 우리는 헤더 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 값으로 사용합니다. 이 값은 *azurewebsites.net* 위치 헤더의 *contoso.com* 대체합니다.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>애플리케이션 게이트웨이의 구성 검색

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>요청 라우팅 규칙의 구성 검색

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP 헤더를 다시 작성하기 위한 구성으로 응용 프로그램 게이트웨이 업데이트

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>다시 쓰기 규칙 삭제

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정하는 방법에 대한 자세한 내용은 [일반적인 헤더 다시 쓰기 시나리오를](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)참조하십시오.
---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는를 사용 하 여 Azure 애플리케이션 게이트웨이에서 HTTP 헤더를 다시 작성 하는 방법에 대 한 정보를 제공 Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 6938ad55915286af397fee6d72a333e3bb39a1e6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397918"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure 애플리케이션 게이트웨이를 사용 하 여 HTTP 요청 및 응답 헤더 재작성-Azure PowerShell

이 문서에서는 Azure PowerShell를 사용 하 여 요청 및 응답에서 HTTP 헤더를 다시 작성 하도록 [Application Gateway V2 SKU](./application-gateway-autoscaling-zone-redundant.md) 인스턴스를 구성 하는 방법을 설명 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

- 이 문서의 단계를 완료 하려면 Azure PowerShell를 로컬로 실행 해야 합니다. 또한 Az module version 1.0.0 이상을 설치 해야 합니다. 을 실행 `Import-Module Az` `Get-Module Az` 하 고 설치한 버전을 확인 합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.
- Application Gateway v2 SKU 인스턴스가 있어야 합니다. 헤더 재작성은 v1 SKU에서 지원 되지 않습니다. V2 SKU가 없는 경우 시작 하기 전에 [Application Gateway V2 sku](./tutorial-autoscale-ps.md) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 재작성을 구성 하려면 다음 단계를 완료 해야 합니다.

1. HTTP 헤더 재작성에 필요한 개체를 만듭니다.

   - **RequestHeaderConfiguration** : 재작성 하려는 요청 헤더 필드와 헤더의 새 값을 지정 하는 데 사용 됩니다.

   - **Responseheaderconfiguration** : 재작성 하려는 응답 헤더 필드와 헤더의 새 값을 지정 하는 데 사용 됩니다.

   - **Actionset** : 이전에 지정 된 요청 및 응답 헤더의 구성을 포함 합니다.

   - **Condition** : 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가 합니다. HTTP (S) 요청 또는 응답이 재작성 조건과 일치 하면 재작성 작업이 수행 됩니다.

     작업에 둘 이상의 조건을 연결 하는 경우 모든 조건이 충족 되는 경우에만 작업이 수행 됩니다. 즉, 작업은 논리적 AND 연산입니다.

   - **RewriteRule** : 여러 재작성 작업/재작성 조건 조합을 포함 합니다.

   - **Rulesequence** : 재작성 규칙이 실행 되는 순서를 결정 하는 데 도움이 되는 선택적 구성입니다. 이 구성은 재작성 집합에 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 규칙 시퀀스 값이 낮은 재작성 규칙이 먼저 실행 됩니다. 두 재작성 규칙에 동일한 규칙 시퀀스 값을 할당 하는 경우 실행 순서는 비결 정적입니다.

     RuleSequence를 명시적으로 지정 하지 않으면 기본값인 100이 설정 됩니다.

   - **RewriteRuleSet** : 요청 라우팅 규칙에 연결 되는 재작성 규칙을 여러 개 포함 합니다.

2. RewriteRuleSet을 라우팅 규칙에 연결 합니다. 재작성 구성은 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 원본 수신기와 연결 되며 전역 헤더 재작성입니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.

여러 HTTP 헤더 재작성 집합을 만들고 각 재작성 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 재작성 집합을 하나만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP 헤더 재작성 규칙 구성을 지정 합니다.

이 예제에서는 위치 헤더에 azurewebsites.net에 대 한 참조가 포함 될 때마다 HTTP 응답의 위치 헤더를 다시 작성 하 여 리디렉션 URL을 수정 합니다. 이렇게 하려면 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하는 조건을 추가 합니다. 패턴을 사용 `(https?):\/\/.*azurewebsites\.net(.*)$` 합니다. `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`헤더 값으로를 사용 합니다. 이 값은 location 헤더에서 *azurewebsites.net* 을 *contoso.com* 로 바꿉니다.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Application gateway의 구성 검색

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>요청 라우팅 규칙의 구성 검색

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP 헤더 재작성 구성으로 응용 프로그램 게이트웨이 업데이트

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

몇 가지 일반적인 사용 사례를 설정 하는 방법에 대 한 자세한 내용은 [common header 재작성 시나리오](./rewrite-http-headers.md)를 참조 하십시오.
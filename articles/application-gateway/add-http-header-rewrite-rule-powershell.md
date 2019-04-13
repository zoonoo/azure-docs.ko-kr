---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는 설명 Azure Application Gateway의 HTTP 헤더를 다시 작성 하는 방법은 Azure PowerShell을 사용 하 여
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548322"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure Application Gateway-Azure PowerShell을 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성

이 문서에서는 Azure PowerShell을 사용 하 여 구성 하는 방법을 보여 줍니다.는 [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 요청 및 응답의 HTTP 헤더를 다시 작성 합니다.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- 이 자습서에서는 Azure PowerShell을 로컬로 실행해야 합니다. Az 모듈 버전 1.0.0 이상을 설치해야 합니다. `Import-Module Az`를 실행한 후 `Get-Module Az`를 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.
- Application Gateway v2는 v1 SKU에 대 한 헤더 재작성 기능 이므로 SKU를 사용할 수 없습니다를 해야 합니다. V2 SKU가 없는 경우 만듭니다는 [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) 시작 하기 전에 합니다.

## <a name="what-is-required-to-rewrite-a-header"></a>헤더를 다시 작성 하는 데 필요한 항목은

HTTP 헤더 다시 쓰기를 구성 해야 합니다.

1. http 헤더를 다시 쓰는 데 필요한 새 개체를 만듭니다.

   - **RequestHeaderConfiguration**:이 개체는 다시 쓰려는 요청 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.

   - **ResponseHeaderConfiguration**:이 개체는 다시 쓰려는 응답 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.

   - **ActionSet**: 이 개체는 위에 지정된 요청 및 응답 헤더의 구성을 포함합니다.

   - **조건**: 선택적 구성 이며 재작성 조건에 추가 하는 경우 HTTP (S) 요청 및 응답의 콘텐츠는 계산 합니다. 재작성 조건과 사용 하 여 연결을 다시 작성 작업을 실행 하는 결정 재작성 조건을 사용 하 여 HTTP (S) 요청 또는 응답와 일치 하는지 여부에 따라 달라 집니다. 

     둘 이상의 조건을 사용 하 여 연결 된 경우 작업을 다음 작업을 실행할지 즉, 모든 조건이 충족 되는 경우에, 논리적 AND 연산을 수행 됩니다.

   - **RewriteRule**: 다시 쓰기 작업이 여러-재작성 조건 조합을 포함 합니다.

   - **RuleSequence**: 선택적 구성입니다. 다양 한 재작성 규칙 실행 순서를 결정할 수 있습니다. 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있을 때 유용 합니다. 낮은 규칙 순서 값을 사용 하 여 다시 쓰기 규칙을 먼저 실행 됩니다. 두 다시 쓰기 규칙을 규칙 순서를 제공 하는 경우 실행 순서 비결 수 됩니다.

     RuleSequence를 명시적으로 지정 하지 않으면, 기본값은 100 설정이 적용 됩니다.

   - **RewriteRuleSet**:이 개체는 요청 라우팅 규칙에 연결 될 여러 다시 쓰기 규칙을 포함 합니다.

2. 라우팅 규칙을 사용 하 여 rewriteRuleSet 연결 해야 합니다. 즉, 재작성 구성이 라우팅 규칙을 통해 원본 수신기에 연결 되었습니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다.

여러 http 헤더 재작성 집합을 만들 수 있습니다 하 고 각 다시 쓰기 집합 리스너가 여러 개 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Http 헤더 다시 쓰기 규칙 구성 지정**

이 예제에서는 "azurewebsites.net"에 대 한 참조를 포함 하는 location 헤더 때마다 http 응답의 location 헤더를 다시 작성 하 여 리디렉션 URL을 수정 하겠습니다. 이 위해 위치 헤더를 응답 패턴을 사용 하 여 azurewebsites.net 포함 되는지 여부를 평가 하는 조건이 추가 됩니다 `(https?):\/\/.*azurewebsites\.net(.*)$`합니다. 사용 하 여 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로. 바뀝니다 *azurewebsites.net* 사용 하 여 *contoso.com* location 헤더에 있습니다.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>기존 Application Gateway의 구성 검색

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>기존 요청 라우팅 규칙의 구성 검색

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Http 헤더를 다시 쓰기 위한 구성으로 Application Gateway를 업데이트합니다.

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

일반적인 몇 가지를 수행 하는 데 필요한 구성에 대 한 더 많은 사용 사례에 알아보려면 [공용 헤더 시나리오를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)합니다.
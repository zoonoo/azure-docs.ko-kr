---
title: 기존 Azure Application Gateway에서 HTTP 헤더 다시 쓰기
description: 이 문서에서는 Azure PowerShell을 사용하여 기존 Azure Application Gateway에 HTTP 헤더를 다시 쓰는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: f3bda2cf680fa1ad17b5cbb3b9ba5911c78a1e4e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316509"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>기존 Application Gateway에서 HTTP 헤더 다시 쓰기

Azure PowerShell을 사용하여 기존 [자동 크기 조정 및 영역 중복 Application Gateway SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)에서 [HTTP 요청 및 응답 헤더를 다시 쓰도록 규칙](rewrite-http-headers.md)을 구성할 수 있습니다.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 기존 Application Gateway 구성 검색
> * Http 헤더 다시 쓰기 규칙 구성 지정
> * Http 헤더를 다시 쓰기 위한 위 구성으로 Application Gateway를 업데이트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 Azure PowerShell을 로컬로 실행해야 합니다. Az 모듈 버전 1.0.0 이상을 설치해야 합니다. `Import-Module Az`를 실행한 후 `Get-Module Az`를 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Http 헤더 다시 쓰기 규칙 구성 지정**

Http 헤더를 다시 쓰는 데 필요한 새 개체를 구성합니다.

- **RequestHeaderConfiguration**:이 개체는 다시 쓰려는 요청 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
- **ResponseHeaderConfiguration**:이 개체는 다시 쓰려는 응답 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
- **ActionSet**: 이 개체는 위에 지정된 요청 및 응답 헤더의 구성을 포함합니다.
- **RewriteRule**: 이 개체는 위에 지정된 모든 *actionSets*를 포함합니다.
- **RewriteRuleSet**: 이 개체는 모든 *rewriteRules*를 포함하며 요청 라우팅 규칙인 기본 또는 경로 기반에 연결해야 합니다.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>기존 Application Gateway의 구성 검색

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>기존 요청 라우팅 규칙의 구성 검색

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Http 헤더를 다시 쓰기 위한 구성으로 Application Gateway를 업데이트합니다.

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>다시 쓰기 규칙 삭제

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [URL 경로 기반 회람 규칙을 사용하여 애플리케이션 게이트웨이 만들기](./tutorial-url-route-powershell.md)

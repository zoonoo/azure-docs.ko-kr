---
title: Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 v2 사용자 지정 규칙 구성
description: Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 v2 사용자 지정 규칙을 구성 하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263735"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 v2 사용자 지정 규칙 구성

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

사용자 지정 규칙을 사용 하면 WAF (웹 응용 프로그램 방화벽)를 통과 하는 각 요청에 대해 평가 되는 사용자 고유의 규칙을 만들 수 있습니다. 이러한 규칙은 관리 되는 규칙 집합의 나머지 규칙 보다 높은 우선 순위를 보유 합니다. 전체 사용자 지정을 허용 하려면 사용자 지정 규칙에 작업 (허용 또는 차단), 일치 조건 및 연산자가 있습니다.

이 문서에서는 사용자 지정 규칙을 사용 하는 Azure 애플리케이션 게이트웨이 WAF v2를 만듭니다. 요청 헤더에 사용자 에이전트 *evilbot*이 포함되어 있으면 사용자 지정 규칙이 트래픽을 차단합니다.

더 많은 사용자 지정 규칙 예제를 보려면 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)을 참조 하세요.

복사, 붙여넣기 및 실행할 수 있는 하나의 연속 스크립트에서이 문서의 Azure PowerShell 코드를 실행 하려면 [Azure 애플리케이션 Gateway PowerShell 샘플](powershell-samples.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Azure PowerShell 모듈이 필요 합니다. Azure PowerShell를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 스크립트를 실행 하려면 Azure PowerShell 모듈 버전 2.1.0 이상이 필요 합니다. 다음을 수행합니다.

  1. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.
  2. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

## <a name="example-script"></a>예제 스크립트

### <a name="set-up-variables"></a>변수 설정

다음 코드를 실행합니다.

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

다음 코드를 실행합니다.

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

다음 코드를 실행합니다.

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>정적 공용 VIP 만들기

다음 코드를 실행합니다.

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>풀 및 프런트 엔드 포트 만들기

다음 코드를 실행합니다.

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>수신기, HTTP 설정, 규칙 및 자동 크기 조정 만들기

다음 코드를 실행합니다.

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>사용자 지정 규칙을 만들어 WAF 정책에 적용

다음 코드를 실행합니다.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

다음 코드를 실행합니다.

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>다음 단계

[웹 응용 프로그램 방화벽에 대 한 자세한 정보](waf-overview.md)

---
title: URL 경로 기반 리디렉션으로 애플리케이션 게이트웨이 만들기 - Azure PowerShell
description: Azure PowerShell을 사용하여 URL 경로 기반 리디렉션된 트래픽으로 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/3/2019
ms.author: victorh
ms.openlocfilehash: e4222ac9269220f7155351889a4d1c8167988414
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729525"
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-azure-powershell"></a>Azure PowerShell을 사용하여 URL 경로 기반 리디렉션으로 애플리케이션 게이트웨이 만들기

Azure PowerShell을 사용하여 [애플리케이션 게이트웨이](application-gateway-introduction.md)를 만들 때 [URL 기반 회람 규칙](application-gateway-url-route-overview.md)을 구성할 수 있습니다. 이 자습서에서는 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하여 백 엔드 풀을 만듭니다. 그런 다음, 웹 트래픽이 적절한 백 엔드 풀로 리디렉션되도록 하는 URL 라우팅 규칙을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 애플리케이션 게이트웨이 만들기
> * 수신기 및 라우팅 규칙 추가
> * 백 엔드 풀에 대한 가상 머신 확장 집합 만들기

다음 예제에서는 두 포트 8080 및 8081에서 들어오고 동일한 백 엔드 풀로 리디렉션되는 사이트 트래픽을 보여 줍니다.

![URL 라우팅 예제](./media/tutorial-url-redirect-powershell/scenario.png)

원하는 경우 [Azure CLI](tutorial-url-redirect-cli.md)를 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬에 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)를 사용하여 *myBackendSubnet* 및 *myAGSubnet*에 대한 서브넷 구성을 만듭니다. 서브넷 구성으로 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 *myVNet*이라는 가상 네트워크를 만듭니다. 마지막으로 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 *myAGPublicIPAddress*라는 공용 IP 주소를 만듭니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

이 섹션에서는 애플리케이션 게이트웨이를 지원하는 리소스를 만든 다음, 최종적으로 애플리케이션 게이트웨이를 만듭니다. 다음과 같은 리소스를 만듭니다.

- *IP 구성 및 프런트 엔드 포트* - 앞에서 만든 서브넷을 애플리케이션 게이트웨이에 연결하고 액세스 시 사용할 포트를 할당합니다.
- *기본 풀* - 모든 애플리케이션 게이트웨이에 서버 백 엔드 풀이 하나 이상 있어야 합니다.
- *기본 수신기 및 규칙* - 기본 수신기는 할당된 포트에서 트래픽을 수신 대기하고 기본 규칙은 기본 풀로 트래픽을 보냅니다.

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

[New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)을 사용하여 이전에 애플리케이션 게이트웨이에 만든 *myAGSubnet*을 연결합니다. [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)를 사용하여 *myAGPublicIPAddress*를 애플리케이션 게이트웨이에 할당합니다. 그런 후 [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)를 사용하여 HTTP 포트를 만들 수 있습니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>기본 풀 및 설정 만들기

[New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)을 사용하여 애플리케이션 게이트웨이에 대해 *appGatewayBackendPool*이라는 기본 백 엔드 풀을 만듭니다. [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)를 사용하여 백 엔드 풀에 대한 설정을 구성합니다.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>기본 수신기 및 규칙 만들기

애플리케이션 게이트웨이에서 트래픽을 백 엔드 풀로 적절하게 라우팅할 수 있으려면 수신기가 필요합니다. 이 자습서에서는 여러 개의 수신기를 만듭니다. 첫 번째 기본 수신기는 루트 URL에서 트래픽을 예상합니다. 다른 수신기는 특정 URL(예: `http://52.168.55.24:8080/images/` 또는 `http://52.168.55.24:8081/video/`)에서 트래픽을 예상합니다.

이전에 만든 프런트 엔드 구성 및 프런트 엔드 포트가 포함된 [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener)를 사용하여 *defaultListener*라는 수신기를 만듭니다. 수신기에서 들어오는 트래픽에 사용할 백 엔드 풀을 인식할 수 있는 규칙이 필요합니다. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)을 사용하여 *rule1*이라는 기본 규칙을 만듭니다.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

필요한 지원 리소스를 만들었으므로 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)를 사용하여 *myAppGateway*라는 애플리케이션 게이트웨이에 대한 매개 변수를 지정한 다음, [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)를 사용하여 만듭니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-backend-pools-and-ports"></a>백 엔드 풀과 포트 추가

[Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool)을 사용하여 애플리케이션 게이트웨이에 백 엔드 풀을 추가할 수 있습니다. 이 예제에서는 *imagesBackendPool* 및 *videoBackendPool*이 생성됩니다. [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport)를 사용하여 풀에 대한 프런트 엔드 포트를 추가합니다. [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway)를 사용하여 애플리케이션 게이트웨이에 변경 내용을 제출합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport `
  -Port 8081

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="add-listeners-and-rules"></a>수신기 및 규칙 추가

### <a name="add-listeners"></a>수신기 추가

[Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)를 사용하여 트래픽을 라우팅하는 데 필요한 *backendListener* 및 *redirectedListener*라는 수신기를 추가합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$redirectPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $redirectPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-default-url-path-map"></a>기본 URL 경로 맵 추가

URL 경로 맵은 특정 URL을 특정 백 엔드 풀로 라우팅하도록 합니다. [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) 및 [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig)를 사용하여 *imagePathRule* 및 *videoPathRule*이라는 URL 경로 맵을 만들 수 있습니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
  -Paths "/video/*" `
  -BackendAddressPool $videoPool `
  -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-redirection-configuration"></a>리디렉션 구성 추가

[Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)을 사용하여 수신기에 대해 리디렉션을 구성할 수 있습니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendListener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectConfig = Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig `
  -RedirectType Found `
  -TargetListener $backendListener `
  -IncludePath $true `
  -IncludeQueryString $true

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-url-path-map"></a>리디렉션 URL 경로 맵 추가

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig

$redirectPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name redirectPathRule `
  -Paths "/images/*" `
  -RedirectConfiguration $redirectConfig

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap `
  -PathRules $redirectPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rules"></a>라우팅 규칙 추가

라우팅 규칙은 URL 맵을 생성된 수신기에 연결합니다. [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)을 사용하여 *defaultRule* 및 *redirectedRule*이라는 규칙을 추가할 수 있습니다.


```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

$redirectPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name defaultRule `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name redirectedRule `
  -RuleType PathBasedRouting `
  -HttpListener $redirectlistener `
  -UrlPathMap $redirectPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>가상 머신 확장 집합 만들기

이 예제에서는 사용자가 만든 세 개의 백 엔드 풀을 지원하는 세 개의 가상 머신 확장 집합을 만듭니다. 사용자가 만든 확장 집합의 이름은 *myvmss1*, *myvmss2* 및 *myvmss3*입니다. 각 확장 집합에는 IIS를 설치하는 두 개의 가상 머신 인스턴스가 포함됩니다. IP 설정을 구성할 때 확장 집합을 백 엔드 풀에 할당합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>IIS 설치

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i

  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져올 수 있습니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 예: `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm`, `http://52.168.55.24:8080/video/test.htm`, 또는 `http://52.168.55.24:8081/images/test.htm`

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![애플리케이션 게이트웨이의 기준 URL 테스트](./media/tutorial-url-redirect-powershell/application-gateway-iistest.png)

&lt;ip-address&gt;를 사용자의 IP 주소로 대체하여 URL을 http://&lt;ip-address&gt;:8080/images/test.htm으로 변경하면 다음 예제와 같은 내용이 표시됩니다.

![애플리케이션 게이트웨이의 이미지 URL 테스트](./media/tutorial-url-redirect-powershell/application-gateway-iistest-images.png)

URL을 http://&lt;ip-address&gt;:8080/video/test.htm으로 변경하고 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하면 다음 예제와 같은 내용이 표시됩니다.

![애플리케이션 게이트웨이의 비디오 URL 테스트](./media/tutorial-url-redirect-powershell/application-gateway-iistest-video.png)

이제 URL을 http://&lt;ip-address&gt;:8081/images/test.htm으로 변경하고 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하면 트래픽이 http://&lt;ip-address&gt;:8080/images에 있는 이미지 백 엔드 풀로 다시 리디렉션되었다고 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [애플리케이션 게이트웨이 통해 수행할 수 있는 작업에 대한 자세한 정보](application-gateway-introduction.md)

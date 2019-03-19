---
title: 가상 머신 확장 집합이 있는 애플리케이션 게이트웨이 만들기 - Azure PowerShell
description: Azure PowerShell을 사용하여 가상 머신 확장 집합이 있는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/26/2019
ms.author: victorh
ms.openlocfilehash: ea34270114aab5f62d713a945e0959539b0b84a5
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309165"
---
# <a name="create-an-application-gateway-and-virtual-machine-scale-set-using-azure-powershell"></a>Azure PowerShell을 사용하여 애플리케이션 게이트웨이 및 가상 머신 확장 집합 만들기

Azure PowerShell을 사용하여 백 엔드 서버에 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하는 [애플리케이션 게이트웨이](application-gateway-introduction.md)를 만들 수 있습니다. 이 예제에서 확장 집합은 애플리케이션 게이트웨이의 기본 백 엔드 풀에 추가되는 두 개의 가상 머신 인스턴스를 포함합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 애플리케이션 게이트웨이 만들기
> * 기본 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 설치 하 고 PowerShell을 로컬로 사용 하려는 경우 이상. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 사용 하 여 Azure 리소스 그룹을 만듭니다 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

이라는 서브넷을 구성 *myBackendSubnet* 하 고 *myAGSubnet* 사용 하 여 [새로 만들기-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)합니다. 가상 네트워크를 만듭니다 *myVNet* 사용 하 여 [새로 만들기-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 서브넷 구성으로 합니다. 마지막으로, 명명 된 공용 IP 주소를 만듭니다 *myAGPublicIPAddress* 사용 하 여 [새로 만들기-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)합니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

연결할 *myAGSubnet* 사용 하 여 응용 프로그램 게이트웨이를 이전에 만든 [새로 만들기-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)합니다. 할당할 *myAGPublicIPAddress* 사용 하 여 응용 프로그램 게이트웨이 [새로 만들기-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
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

### <a name="create-the-backend-pool-and-settings"></a>백 엔드 풀 및 설정 만들기

이라는 백 엔드 풀을 만듭니다 *appGatewayBackendPool* 사용 하 여 응용 프로그램 게이트웨이 [새로 만들기-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)합니다. 사용 하 여 백 엔드 주소 풀에 대 한 설정을 구성 [새로 만들기-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings)합니다.

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

애플리케이션 게이트웨이에서 트래픽을 백 엔드 풀로 적절히 라우팅할 수 있는 수신기가 필요합니다. 이 예제에서는 루트 URL에서 트래픽을 수신 대기하는 기본 수신기를 만듭니다. 

라는 수신기를 만듭니다 *mydefaultListener* 사용 하 여 [새로 만들기-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 프런트 엔드 구성 및 이전에 만든 프런트 엔드 포트를 사용 하 여 합니다. 수신기에서 들어오는 트래픽에 사용할 백 엔드 풀을 인식할 수 있는 규칙이 필요합니다. 이라는 기본 규칙을 만듭니다 *rule1* 사용 하 여 [새로 만들기-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)합니다.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
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

만들었으므로 필요한 지원 리소스를 사용 하 여 응용 프로그램 게이트웨이에 대 한 매개 변수를 지정 [새로 만들기-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)을 사용 하 여 만든 [새로 만들기-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)합니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
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

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

이 예제에서는 애플리케이션 게이트웨이에서 백 엔드 풀에 대한 서버를 제공하도록 가상 머신 확장 집합을 만듭니다. IP 설정을 구성할 때 확장 집합을 백 엔드 풀에 할당합니다.

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
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>IIS 설치

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

사용할 수 있습니다 [Get AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 응용 프로그램 게이트웨이의 공용 IP 주소를 가져오려고 합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![애플리케이션 게이트웨이의 기준 URL 테스트](./media/tutorial-create-vmss-powershell/tutorial-iistest.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 애플리케이션 게이트웨이 만들기
> * 기본 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기

애플리케이션 게이트웨이 및 관련 리소스에 대해 자세히 알아보려면 사용법 문서를 참조하세요.

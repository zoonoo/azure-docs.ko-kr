---
title: 애플리케이션 게이트웨이 만들기 - Azure PowerShell | Microsoft Docs
description: Azure PowerShell을 사용하여 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 412ede9895e6684d039dcad4df4189c8b3774d76
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313823"
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Azure PowerShell을 사용하여 애플리케이션 게이트웨이 만들기

Azure PowerShell을 사용하여 명령줄 또는 스크립트에서 애플리케이션 게이트웨이를 만들거나 관리할 수 있습니다. 이 빠른 시작에서는 네트워크 리소스, 백 엔드 서버 및 애플리케이션 게이트웨이를 만드는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 설치 하 고 PowerShell을 로컬로 사용 하려는 경우 이상. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

사용 하 여 Azure 리소스 그룹을 만듭니다 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

사용 하 여 서브넷 구성을 만듭니다 [새로 만들기-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)합니다. 사용 하 여 가상 네트워크 만들기 [새로 만들기-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 서브넷 구성으로 합니다. 마지막으로 사용 하 여 공용 IP 주소를 만듭니다 [새로 만들기-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)합니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
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
## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 애플리케이션 게이트웨이에 대한 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 또한 가상 머신에 IIS를 설치하여 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인합니다.

### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

사용 하 여 네트워크 인터페이스 만들기 [새로 만들기-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)합니다. 사용 하 여 가상 머신 구성을 만듭니다 [새로 만들기-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)합니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. 사용자 이름으로 *azureuser*를 입력하고, 암호로 *Azure123456!* 를 암호 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 가상 머신을 만듭니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

사용 하 여 [새로 만들기-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) application gateway를 사용 하 여 이전에 만든 서브넷에 연결 하는 구성을 만듭니다. 사용 하 여 [새로 만들기-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) application gateway에도 이전에 만든 공용 IP 주소를 할당 하는 구성을 만듭니다. 사용 하 여 [새로 만들기-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) application gateway에 액세스 하는 데 사용할 80 포트를 할당 합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>백 엔드 풀 만들기

사용 하 여 [새로 만들기-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) application gateway에 대 한 백 엔드 풀을 만듭니다. 사용 하 여 백 엔드 풀에 대 한 설정을 구성 [새로 만들기-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings)합니다.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>수신기를 만들고 규칙을 추가

애플리케이션 게이트웨이에서 트래픽을 백 엔드 풀로 적절히 라우팅할 수 있는 수신기가 필요합니다. 사용 하 여 수신기를 만듭니다 [새로 만들기-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 프런트 엔드 구성 및 이전에 만든 프런트 엔드 포트를 사용 하 여 합니다. 수신기에서 들어오는 트래픽에 사용할 백 엔드 풀을 인식할 수 있는 규칙이 필요합니다. 사용 하 여 [새로 만들기-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) 라는 규칙을 만들려면 *rule1*합니다.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

이제 필요한 지원 리소스를 만들 사용 하 여 [새로 만들기-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) application gateway에 대 한 매개 변수를 지정 하 고 다음 사용할 [새로 만들기-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) 를 만드시겠습니까.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

사용 하 여 [Get AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 응용 프로그램 게이트웨이의 공용 IP 주소를 가져오려고 합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![애플리케이션 게이트웨이 테스트](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요한 경우 사용할 수 없습니다 합니다 [제거 AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 리소스 그룹, 응용 프로그램 게이트웨이 제거 하는 명령 및 모든 관련 리소스입니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 그룹, 네트워크 리소스 및 백 엔드 서버를 만들었습니다. 그런 다음, 이러한 리소스를 사용하여 애플리케이션 게이트웨이를 만들었습니다. 애플리케이션 게이트웨이 및 관련 리소스에 대해 자세히 알아보려면 사용법 문서를 참조하세요.


---
title: '빠른 시작: PowerShell을 사용하여 웹 트래픽 보내기'
titleSuffix: Azure Application Gateway
description: Azure PowerShell을 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: abb38dfc342c8ff692ed1a3a05376b5dcefe8a3d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399563"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure Application Gateway를 통해 웹 트래픽 보내기

이 빠른 시작에서는 Azure PowerShell을 사용하여 애플리케이션 게이트웨이를 만듭니다. 그런 다음, 테스트하여 제대로 작동하는지 확인합니다. 

애플리케이션 게이트웨이는 애플리케이션 웹 트래픽을 백 엔드 풀의 특정 리소스로 보냅니다. 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가합니다. 간단히 나타내기 위해 이 문서에서는 공용 프런트 엔드 IP 1개, 애플리케이션 게이트웨이에 단일 사이트를 호스트하는 기본 수신기 1개, 기본 요청 라우팅 규칙 1개 및 백 엔드 풀의 가상 머신 2개를 이용한 간단한 설정을 사용합니다.

[Azure CLI](quick-create-cli.md) 또는 [Azure Portal](quick-create-portal.md)을 사용하여 이 빠른 시작을 완료할 수도 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell 버전 1.0.0 이상](/powershell/azure/install-az-ps)(로컬로 Azure PowerShell을 실행하는 경우)

## <a name="connect-to-azure"></a>Azure에 연결

Azure에 연결하려면 `Connect-AzAccount`를 실행합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

새 리소스 그룹을 만들려면 `New-AzResourceGroup` cmdlet을 사용합니다. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>네트워크 리소스 만들기

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다.  애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.  Application Gateway에 대한 새 서브넷을 만들거나 기존 서브넷을 사용할 수 있습니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 사용 사례에 따라 Application Gateway의 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP를 선택합니다.

1. `New-AzVirtualNetworkSubnetConfig`를 사용하여 서브넷 구성을 만듭니다.
2. `New-AzVirtualNetwork`를 사용하여 서브넷 구성이 포함된 가상 네트워크를 만듭니다. 
3. `New-AzPublicIpAddress`를 사용하여 공용 IP 주소를 만듭니다. 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

1. `New-AzApplicationGatewayIPConfiguration`을 사용하여 이전에 만든 서브넷과 애플리케이션 게이트웨이를 연결하는 구성을 만듭니다. 
2. `New-AzApplicationGatewayFrontendIPConfig`를 사용하여 이전에 만든 공용 IP 주소를 애플리케이션 게이트웨이에 할당하는 구성을 만듭니다. 
3. `New-AzApplicationGatewayFrontendPort`를 사용하여 애플리케이션 게이트웨이에 액세스할 포트 80을 할당합니다.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

1. `New-AzApplicationGatewayBackendAddressPool`을 사용하여 애플리케이션 게이트웨이에 대한 백 엔드 풀을 만듭니다. 현재 백 엔드 풀은 비어 있으며 다음 섹션에서 백 엔드 서버 NIC를 만드는 동안 백 엔드 풀에 추가합니다.
2. `New-AzApplicationGatewayBackendHttpSetting`을 사용하여 백 엔드 풀에 대한 설정을 구성합니다.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>수신기를 만들고 규칙을 추가

애플리케이션 게이트웨이가 트래픽을 백 엔드 풀로 적절히 라우팅하려면 Azure에 수신기가 필요합니다. 또한 수신기가 들어오는 트래픽에 어떤 백 엔드 풀을 사용해야 하는지 알 수 있도록 규칙이 필요합니다. 

1. 이전에 만든 프론트 엔드 구성 및 프론트 엔드 포트가 포함된 `New-AzApplicationGatewayHttpListener`를 사용하여 수신기를 만듭니다. 
2. `New-AzApplicationGatewayRequestRoutingRule`을 사용하여 *rule1*이라는 규칙을 만듭니다. 

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

필요한 지원 리소스를 만들었으니, 이제 애플리케이션 게이트웨이를 만들겠습니다.

1. `New-AzApplicationGatewaySku`를 사용하여 애플리케이션 게이트웨이에 대한 매개 변수를 지정합니다.
2. `New-AzApplicationGateway`를 사용하여 애플리케이션 게이트웨이를 만듭니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
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

### <a name="backend-servers"></a>백 엔드 서버

Application Gateway를 만들었으므로 웹 사이트를 호스트할 백 엔드 가상 머신을 만듭니다. 백 엔드는 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성될 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 또한 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인할 수 있도록 가상 머신에 IIS를 설치합니다.

#### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

1. `Get-AzApplicationGatewayBackendAddressPool`을 사용하여 최근에 만든 Application Gateway 백 엔드 풀 구성을 가져옵니다.
2. `New-AzNetworkInterface`를 사용하여 네트워크 인터페이스를 만듭니다.
3. `New-AzVMConfig`를 사용하여 가상 머신 구성을 만듭니다.
4. `New-AzVM`을 사용하여 가상 머신을 만듭니다.

가상 머신을 만드는 다음 코드 샘플을 실행하면 Azure가 자격 증명을 요청합니다. 사용자 이름으로 *azureuser*를 입력하고, 암호를 입력합니다.
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
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

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

애플리케이션 게이트웨이를 만들려면 반드시 IIS가 필요한 것은 아니지만, 이 빠른 시작에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인하기 위해 설치했습니다. IIS를 사용하여 애플리케이션 게이트웨이 테스트:

1. `Get-AzPublicIPAddress`를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. 
2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 브라우저를 새로 고치면 가상 머신의 이름이 표시됩니다. 응답이 유효하면 애플리케이션 게이트웨이가 성공적으로 만들어졌으며 백 엔드에 성공적으로 연결할 수 있다는 의미입니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![애플리케이션 게이트웨이 테스트](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 애플리케이션 게이트웨이 및 모든 관련 리소스도 함께 삭제됩니다. 

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure PowerShell을 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-powershell.md)


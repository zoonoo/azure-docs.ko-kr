---
title: VM에 대한 라우팅 기본 설정 구성 - Azure PowerShell
description: Azure PowerShell을 통해 라우팅 기본 설정을 선택하여 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 8325d63881c72a795e3b9e9a6d1d8498c84972ad
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829327"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정 구성

이 문서에서는 가상 머신의 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정 옵션으로 **인터넷**을 선택하면 VM의 인터넷 바인딩 트래픽이 ISP 네트워크를 통해 라우팅됩니다. 기본 라우팅은 Microsoft 글로벌 네트워크를 통해 전달됩니다.

이 문서에서는 Azure PowerShell을 사용하여 ISP 네트워크를 통해 트래픽을 라우팅하도록 설정된 공용 IP로 가상 머신을 만드는 방법을 보여줍니다.

> [!IMPORTANT]
> 라우팅 기본 설정은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-feature-for-your-subscription"></a>구독에 대한 기능 등록
라우팅 기본 설정 기능은 현재 미리 보기로 제공됩니다. 다음과 같이 구독에 대한 기능을 등록합니다.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
1. Cloud Shell을 사용하는 경우 2단계로 건너뜁니다. 명령 세션을 열고 `Connect-AzAccount`를 사용하여 Azure에 로그인합니다.
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 미국 동부 Azure 지역의 리소스 그룹을 만듭니다.

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

인터넷에서 가상 머신에 액세스하려면 공용 IP 주소가 필요합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *미국 동부* 지역의 *MyResourceGroup* 리소스 그룹에 *MyPublicIP* 라우팅 기본 설정 유형 *인터넷*이라는 IPv4 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

VM을 배포하기 전에 지원되는 네트워크 리소스(네트워크 보안 그룹, 가상 네트워크 및 가상 NIC)를 만들어야 합니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNSG*라는 NSG를 만듭니다.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *mySubNet*을 사용하여 *myVnet*이라는 가상 네트워크를 만듭니다.

### <a name="create-a-subnet"></a>서브넷 만들기

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>NIC 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 가상 NIC를 만듭니다. 다음 예제에서는 가상 NIC를 만듭니다.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>VM에 대한 네트워크 트래픽 허용

인터넷에서 공용 IP 주소에 연결하려면 먼저 네트워크 인터페이스, 네트워크 인터페이스가 있는 서브넷 또는 둘 다에 연결된 네트워크 보안 그룹에서 필요한 포트가 열려 있는지 확인합니다. [포털](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) 또는 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)을 사용하여 네트워크 인터페이스 및 서브넷에 대한 유효한 보안 규칙을 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

* [공용 IP 주소의 라우팅 기본 설정](routing-preference-overview.md)에 대해 자세히 알아봅니다.
* Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대해 자세히 알아봅니다.
* [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.

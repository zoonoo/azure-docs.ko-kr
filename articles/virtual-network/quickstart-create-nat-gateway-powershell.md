---
title: NAT 게이트웨이 만들기 - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 NAT 게이트웨이를 만드는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: de4e32d79cf4dfb3a5f54544c65544297a2c0232
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88054156"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 NAT 게이트웨이 만들기

이 자습서에서는 Azure Virtual Network NAT 서비스를 사용하는 방법을 보여줍니다. Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell을 사용하여 이 자습서를 완료하거나 명령을 로컬로 실행할 수 있습니다.  Azure Cloud Shell을 사용하지 않은 경우 [지금 로그인](https://shell.azure.com)합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest)을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT**라는 리소스 그룹을 **eastus2** 위치에 만듭니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>NAT 게이트웨이 만들기

NAT 게이트웨이에 대한 공용 IP 옵션은 다음과 같습니다.

* **공용 IP 주소**
* **공용 IP 접두사**

둘 다 NAT 게이트웨이에서 사용할 수 있습니다.

공용 IP 주소 및 공용 IP 접두사를 이 시나리오에 추가하여 보여 줍니다.

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest)를 사용하여 **myPublicIP**라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$publicIP** 변수에 저장됩니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

[New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest)를 사용하여 **myPublicIPprefix**라는 공용 IP 접두사를 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$publicIPPrefix**라는 변수에 저장됩니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

[New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)를 사용하여 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령의 결과는 **myPublicIP** 공용 IP 주소와 **myPublicIPprefix** 공용 IP 접두사를 사용하는 **myNATgateway**라는 게이트웨이 리소스를 만듭니다. 유휴 시간 제한은 10분으로 설정됩니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$natGateway**라는 변수에 저장됩니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

이 시점에서 NAT 게이트웨이가 작동하며, 이제 가상 네트워크의 서브넷을 사용해야 하는 서브넷만 구성하면 됩니다.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

가상 네트워크를 만들고 서브넷을 게이트웨이에 연결합니다.

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)를 사용하여 **myResourceGroup**에서 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest)를 사용하여 **mySubnet**이라는 서브넷이 있는 **myVnet**이라는 가상 네트워크를 만듭니다. 가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$subnet** 및 **$vnet** 변수에 저장됩니다.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 서비스를 사용합니다.  UDR을 구성할 필요가 없습니다.

## <a name="create-a-vm-to-use-the-nat-service"></a>NAT 서비스를 사용하는 VM 만들기

이제 NAT 서비스를 사용할 VM을 만듭니다.  이 VM에는 VM에 액세스할 수 있도록 인스턴스 수준 공용 IP로 사용할 공용 IP가 있습니다.  NAT 서비스는 흐름 방향을 인식하며 서브넷의 기본 인터넷 대상을 대체합니다. VM의 공용 IP 주소는 아웃바운드 연결에 사용되지 않습니다.

### <a name="create-public-ip-for-source-vm"></a>원본 VM에 대한 공용 IP 만들기

VM에 액세스하는 데 사용할 공용 IP를 만듭니다.  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest)를 사용하여 **myPublicIPVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$publicIpVM**이라는 변수에 저장됩니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG 만들기 및 VM에 대한 SSH 엔드포인트 공개

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh에 대한 인바운드 액세스를 허용하는 NSG를 만들어야 합니다. [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest)을 사용하여 **myNSG**라는 NSG 리소스를 만듭니다. [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)를 사용하여 **ssh**라는 SSH 액세스에 대한 NSG 규칙을 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$nsg**라는 변수에 저장됩니다.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>VM에 대한 NIC 만들기

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0)를 사용하여 **myNic**라는 네트워크 인터페이스를 만듭니다. 이 명령은 공용 IP 주소와 네트워크 보안 그룹을 연결합니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$nic**라는 변수에 저장됩니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>VM 만들기

#### <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. SSH 키 쌍이 이미 있는 경우 이 단계를 건너뜁니다.

ssh-keygen을 사용하여 SSH 키 쌍을 만듭니다.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
PuTTy 사용을 포함하여 SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [Windows에 SSH 키를 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

Cloud Shell을 사용하여 SSH 키 쌍을 만드는 경우 키 쌍이 컨테이너 이미지에 저장됩니다. 이 [스토리지 계정이 자동으로 만들어집니다](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). 키를 검색하기 전에는 스토리지 계정 또는 파일 공유를 삭제하지 않습니다.

#### <a name="create-vm-configuration"></a>VM 구성 만들기

PowerShell에서 VM을 만들려면 사용할 이미지, 크기 및 인증 옵션에 대한 설정이 포함된 구성을 만듭니다. 이 구성은 VM을 빌드하는 데 사용됩니다.

SSH 자격 증명, OS 정보 및 VM 크기를 정의합니다. 다음 예제에서는 SSH 키가 ~/.ssh/id_rsa.pub에 저장됩니다.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
구성 정의를 결합하여 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0)을 통해 **myVM**이라는 VM을 **myResourceGroupNAT**에 만듭니다.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

VM에서 배포가 준비될 때까지 기다린 후 나머지 단계를 계속 진행합니다.

## <a name="discover-the-ip-address-of-the-vm"></a>VM의 IP 주소 검색

먼저 만든 VM의 IP 주소를 검색해야 합니다. VM의 공용 IP 주소를 가져오려면 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)를 사용합니다. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이를 사용하여 VM에 액세스할 수 있도록 메모장에 붙여넣습니다.

### <a name="sign-in-to-vm"></a>VM에 로그인

SSH 자격 증명은 이전 작업의 Cloud Shell에 저장해야 합니다.  브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다.

```bash
ssh azureuser@<ip-address-destination>
```

이제 NAT 서비스를 사용할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 NAT 게이트웨이 및 이를 사용하는 VM을 만들었습니다. 

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 해결할 수 있습니다.


- [Azure Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.
> [!div class="nextstepaction"]



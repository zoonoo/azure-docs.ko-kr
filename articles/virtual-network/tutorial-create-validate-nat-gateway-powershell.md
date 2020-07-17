---
title: '자습서: NAT 게이트웨이 만들기 및 테스트 - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: 이 자습서에서는 Azure PowerShell을 사용하여 NAT 게이트웨이를 만들고 NAT 서비스를 테스트하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: 059d1f99bafe656aaaf4dbab69e437728b9afdfb
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735305"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>자습서: Azure PowerShell을 사용하여 NAT 게이트웨이 만들기 및 NAT 서비스 테스트

이 자습서에서는 Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. NAT 게이트웨이를 테스트하려면 원본 및 대상 가상 머신을 배포해야 합니다. 공용 IP 주소에 대한 아웃바운드 연결을 설정하여 NAT 게이트웨이를 테스트합니다. 이러한 연결은 원본 가상 머신에서 대상으로 제공됩니다. 이 자습서에서는 간단히 하기 위해 원본 및 대상을 동일한 리소스 그룹의 서로 다른 두 가상 네트워크에 배포합니다.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell을 사용하여 이 자습서를 완료하거나 각 명령을 로컬로 실행할 수 있습니다.  Azure Cloud Shell을 사용한 적이 없는 경우 [지금 로그인](https://shell.azure.com)해야 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT**라는 리소스 그룹을 **eastus2** 위치에 만듭니다.


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>NAT 게이트웨이 만들기

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest)를 사용하여 **myPublicIPsource**라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$publicIPsource**라는 변수에 저장됩니다.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest)를 사용하여 **myPublicIPprefixsource**라는 공용 IP 접두사를 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$publicIPPrefixsource**라는 변수에 저장됩니다.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

[New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)를 사용하여 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령의 결과는 **myPublicIPsource** 공용 IP 주소와 **myPublicIPprefixsource** 공용 IP 접두사를 사용하는 **myNATgateway**라는 게이트웨이 리소스를 만듭니다. 유휴 시간 제한은 10분으로 설정됩니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$natGateway**라는 변수에 저장됩니다.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

이 시점에서 NAT 게이트웨이가 작동하며, 이제 가상 네트워크의 서브넷을 사용해야 하는 서브넷만 구성하면 됩니다.

## <a name="prepare-the-source-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 원본 준비

전체 테스트 환경을 설정하는 과정을 안내합니다. 오픈 소스 도구를 사용하여 NAT 게이트웨이를 확인하는 테스트를 설정합니다. 먼저 이전에 만든 NAT 게이트웨이 리소스를 사용하는 원본으로 시작합니다.

### <a name="configure-virtual-network-for-source"></a>원본에 대한 가상 네트워크 구성

가상 네트워크를 만들고 서브넷을 게이트웨이에 연결합니다.

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)를 사용하여 **myResourceGroupNAT**에서 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest)를 사용하여 **mySubnetsource**라는 서브넷이 있는 **myVnetsource**라는 가상 네트워크를 만듭니다. 가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$subnetsource** 및 **$vnetsource** 변수에 저장됩니다.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 서비스를 사용합니다.  UDR을 구성할 필요가 없습니다.

NAT 게이트웨이를 테스트하려면 먼저 원본 VM을 만들어야 합니다.  외부에서 이 VM에 액세스할 수 있도록 공용 IP 주소 리소스를 인스턴스 수준 공용 IP로 할당합니다. 이 주소는 테스트를 위해 액세스하는 데만 사용됩니다.  NAT 서비스를 다른 아웃바운드 옵션보다 우선적으로 적용하는 방법을 설명합니다.

또한 공용 IP 없이 이 VM을 만들고, 연습용으로 공용 IP가 없는 점프 박스로 사용할 다른 VM을 만들 수도 있습니다.

### <a name="create-public-ip-for-source-vm"></a>원본 VM에 대한 공용 IP 만들기

VM에 액세스하는 데 사용할 공용 IP를 만듭니다.  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest)를 사용하여 **myPublicIPVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$publicIpsourceVM**이라는 변수에 저장됩니다.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG 만들기 및 VM에 대한 SSH 엔드포인트 공개

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh에 대한 인바운드 액세스를 허용하는 NSG를 만듭니다. NAT 서비스는 흐름 방향을 인식합니다. NAT 게이트웨이가 동일한 서브넷에 구성되면 이 NSG는 아웃바운드에 사용되지 않습니다. [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest)을 사용하여 **myNSGsource**라는 NSG 리소스를 만듭니다. [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)를 사용하여 **ssh**라는 SSH 액세스에 대한 NSG 규칙을 **myResourceGroupNAT**에 만듭니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$nsgsource**라는 변수에 저장됩니다.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>원본 VM에 대한 NIC 만들기

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0)를 사용하여 **myNicsource**라는 네트워크 인터페이스를 만듭니다. 이 명령은 공용 IP 주소와 네트워크 보안 그룹을 연결합니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$nicsource**라는 변수에 저장됩니다.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>원본 VM 만들기

#### <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. SSH 키 쌍이 이미 있는 경우 이 단계를 건너뜁니다.

ssh-keygen을 사용하여 SSH 키 쌍을 만듭니다.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
PuTTy 사용을 포함하여 SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [Windows에 SSH 키를 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

Cloud Shell을 사용하여 SSH 키 쌍을 만드는 경우 키 쌍이 컨테이너 이미지에 저장됩니다. 이 [스토리지 계정이 자동으로 만들어집니다](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). 키를 검색하기 전에는 스토리지 계정 또는 파일 공유를 삭제하지 않습니다.

#### <a name="create-vm-configuration"></a>VM 구성 만들기

PowerShell에서 VM을 만들려면 사용할 이미지, 크기 및 인증 옵션 등의 설정이 있는 구성을 만듭니다. 그런 다음, 이 구성을 사용하여 VM을 빌드합니다.

SSH 자격 증명, OS 정보 및 VM 크기를 정의합니다. 다음 예제에서는 SSH 키가 ~/.ssh/id_rsa.pub에 저장됩니다.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
구성 정의를 결합하여 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0)을 통해 **myVMsource**라는 VM을 **myResourceGroupNAT**에 만듭니다.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

명령이 즉시 반환되지만 VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="prepare-destination-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 대상 준비

이제 NAT 서비스에서 변환하는 아웃바운드 트래픽의 대상을 만들어 테스트할 수 있습니다.

### <a name="configure-virtual-network-for-destination"></a>대상에 대한 가상 네트워크 구성

대상 가상 머신이 있는 가상 네트워크를 만들어야 합니다.  이러한 명령은 원본 VM에 적용되는 단계와 동일합니다. 대상 엔드포인트를 공개하도록 작은 변경 내용이 추가되었습니다.

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)를 사용하여 **myResourceGroupNAT**에서 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest)를 사용하여 **mySubnetdestination**이라는 서브넷이 있는 **myVnetdestination**이라는 가상 네트워크를 만듭니다. 가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$subnetdestination** 및 **$vnetdestination** 변수에 저장됩니다.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>대상 VM에 대한 공용 IP 만들기

대상 VM에 액세스하는 데 사용할 공용 IP를 만듭니다.  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest)를 사용하여 **myPublicIPdestinationVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.  이 명령의 결과는 나중에 사용할 수 있도록 **$publicIpdestinationVM**이라는 변수에 저장됩니다.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>NSG 만들기 및 VM에 대한 SSH 및 HTTP 엔드포인트 공개

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh에 대한 인바운드 액세스를 허용하는 NSG를 만듭니다. [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest)을 사용하여 **myNSGdestination**이라는 NSG 리소스를 만듭니다. [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)를 사용하여 **ssh**라는 SSH 액세스에 대한 NSG 규칙을 만듭니다.  [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)를 사용하여 **http**라는 HTTP 액세스에 대한 NSG 규칙을 만듭니다. 두 규칙이 모두 **myResourceGroupNAT**에 만들어집니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$nsgdestination**이라는 변수에 저장됩니다.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>대상 VM에 대한 NIC 만들기

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0)를 사용하여 **myNicdestination**이라는 네트워크 인터페이스를 만듭니다. 이 명령은 공용 IP 주소와 네트워크 보안 그룹에 연결합니다. 이 명령의 결과는 나중에 사용할 수 있도록 **$nicdestination**라는 변수에 저장됩니다.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>대상 VM 만들기

#### <a name="create-vm-configuration"></a>VM 구성 만들기

PowerShell에서 VM을 만들려면 사용할 이미지, 크기 및 인증 옵션 등의 설정이 있는 구성을 만듭니다. 그런 다음, 이 구성을 사용하여 VM을 빌드합니다.

SSH 자격 증명, OS 정보 및 VM 크기를 정의합니다. 다음 예제에서는 SSH 키가 ~/.ssh/id_rsa.pub에 저장됩니다.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
구성 정의를 결합하여 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0)을 통해 **myVMdestination**이라는 VM을 **myResourceGroupNAT**에 만듭니다.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

명령이 즉시 반환되지만 VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>대상 VM에서 웹 서버 및 테스트 페이로드 준비

먼저 대상 VM의 IP 주소를 검색해야 합니다.  VM의 공용 IP 주소를 가져오려면 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)를 사용합니다. 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 대상 가상 머신임을 나타냅니다.

### <a name="sign-in-to-destination-vm"></a>대상 VM에 로그인

SSH 자격 증명은 이전 작업의 Cloud Shell에 저장해야 합니다.  브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 

```azurepowershell-interactive
ssh azureuser@$destip

```

로그인하면 다음 명령을 복사하여 붙여넣습니다.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

이러한 명령은 가상 머신을 업데이트하고, nginx를 설치하며, 100KB 파일을 만듭니다. 이 파일은 NAT 서비스를 사용하여 원본 VM에서 검색됩니다.

대상 VM을 사용하여 SSH 세션을 닫습니다.

## <a name="prepare-test-on-source-vm"></a>원본 VM에서 테스트 준비

먼저 원본 VM의 IP 주소를 검색해야 합니다.  VM의 공용 IP 주소를 가져오려면 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)를 사용합니다. 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 원본 가상 머신임을 나타냅니다.

### <a name="log-into-source-vm"></a>원본 VM에 로그인

다시 한 번 SSH 인증 정보가 Cloud Shell에 저장됩니다. 브라우저에서 [Azure Cloud Shell](https://shell.azure.com)에 대한 새 탭을 엽니다.  이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

다음 명령을 복사하고 붙여넣어 NAT 서비스 테스트를 준비합니다.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

이러한 명령은 가상 머신을 업데이트하고, go를 설치하고, GitHub에서 [hey](https://github.com/rakyll/hey)를 설치하고, 셸 환경을 업데이트합니다.

이제 NAT 서비스를 테스트할 준비가 되었습니다.

## <a name="validate-nat-service"></a>NAT 서비스 유효성 검사

원본 VM에 로그인한 상태에서 **curl** 및 **hey**를 사용하여 대상 IP 주소에 대한 요청을 생성할 수 있습니다.

curl을 사용하여 100KB 파일을 검색합니다.  아래 예제의 **\<ip-address-destination>** 를 이전에 복사한 대상 IP 주소로 바꿉니다.  **--output** 매개 변수는 검색된 파일이 삭제됨을 나타냅니다.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

또한 **hey**를 사용하여 일련의 요청을 생성할 수도 있습니다. 다시 한 번 아래 예제의 **\<ip-address-destination>** 를 이전에 복사한 대상 IP 주소로 바꿉니다.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

이 명령은 30초의 시간 제한에서 동시에 100개의 요청을 10회 생성합니다. TCP 연결은 다시 사용되지 않습니다.  각 요청에서 100KB를 검색합니다.  실행이 완료되면 **hey**에서 수행된 NAT 서비스의 작동 상태에 대한 몇 가지 통계를 보고합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 NAT 게이트웨이를 만들고, 원본 및 대상 VM을 만든 다음, NAT 게이트웨이를 테스트했습니다.

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 쉽게 해결할 수 있습니다.

- [Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대한 자세한 정보
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.

> [!div class="nextstepaction"]


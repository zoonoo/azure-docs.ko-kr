---
title: '빠른 시작: 내부 부하 분산 장치 만들기 - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 내부 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696781"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 VM 부하를 분산하는 내부 부하 분산 장치 만들기

Azure PowerShell을 사용하여 내부 부하 분산 장치와 두 개의 가상 머신을 만들어 Azure Load Balancer를 시작합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

* 이름을 **CreateIntLBQS-rg** 로 지정합니다.
* 위치: **eastus**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>가상 네트워크 및 Azure Bastion 호스트 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 다음과 같은 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 서브넷: **myBackendSubnet**
* 가상 네트워크: **10.0.0.0/16**
* 서브넷: **10.0.0.0/24**
* 서브넷 이름: **AzureBastionSubnet**.
* 서브넷 **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Azure Bastion 호스트에 대한 공용 IP 주소 만들기

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* 이름: **myPublicIPBastion**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 할당 방법: **static**.
* **표준** SKU

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Azure Bastion 호스트 만들기

[New-AzBastion](/powershell/module/az.network/new-azbastion)을 사용하여 베스천 호스트를 만듭니다.

* 이름: **myBastion**.
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 가상 네트워크: **myVNet**
* 공용 IP 주소 **myPublicIPBastion** 과 연결됩니다.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기
가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 다음과 같은 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* **HTTP 허용** 에 대한 설명
* 액세스 **허용**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 우선 순위 **2000**
* **인터넷** 원본
* **(*)** 의 원본 포트 범위
* **(*)** 의 대상 주소 접두사
* 대상 **포트 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 다음과 같은 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 이전 단계에서 생성된 보안 규칙을 변수에 저장합니다.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-frontend-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 프런트 엔드 IP를 만듭니다.

* 이름: **myFrontEnd**
* **10.0.0.4** 의 개인 IP 주소입니다.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 다음과 같은 백 엔드 주소 풀을 만듭니다. 

* 이름: **myBackEndPool**
* VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 다음과 같은 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 다음과 같은 부하 분산 장치 규칙을 만듭니다. 

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe** 를 사용합니다.
* 프로토콜: **TCP**
* **15분** 의 유휴 제한 시간.
* TCP 재설정을 활성화합니다.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>백 엔드 풀의 가상 머신은 이 구성과 아웃바운드 인터넷 연결을 사용하지 않습니다. </br> 아웃바운드 연결 제공에 대한 자세한 내용은 다음을 참조하세요. </br> **[Azure에서 아웃바운드 연결](load-balancer-outbound-connections.md)**</br> 연결 제공 옵션: </br> **[아웃바운드 전용 부하 분산 장치 구성](egress-only.md)** </br> **[Virtual Network NAT란?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 다음과 같은 내부 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* 위치: **eastus**
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>네트워크 인터페이스 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 다음과 같은 세 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm-1"></a>VM 1

* 이름: **myNicVM1**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool** 의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 이름: **myNicVM2**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool** 의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = Get-Credential
```

다음과 같은 가상 머신을 만듭니다.

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 이름: **myVM1**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨
* **영역 1**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* 이름: **myVM2**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM2** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨
* **영역 2**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>가상 네트워크 및 Azure Bastion 호스트 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 다음과 같은 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 서브넷: **myBackendSubnet**
* 가상 네트워크: **10.0.0.0/16**
* 서브넷: **10.0.0.0/24**
* 서브넷 이름: **AzureBastionSubnet**.
* 서브넷 **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Azure Bastion 호스트에 대한 공용 IP 주소 만들기

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* 이름: **myPublicIPBastion**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 할당 방법: **static**.
* **표준** SKU

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Azure Bastion 호스트 만들기

[New-AzBastion](/powershell/module/az.network/new-azbastion)을 사용하여 베스천 호스트를 만듭니다.

* 이름: **myBastion**.
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 가상 네트워크: **myVNet**
* 공용 IP 주소 **myPublicIPBastion** 과 연결됩니다.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.


### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기
가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 다음과 같은 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* **HTTP 허용** 에 대한 설명
* 액세스 **허용**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 우선 순위 **2000**
* **인터넷** 원본
* **(*)** 의 원본 포트 범위
* **(*)** 의 대상 주소 접두사
* 대상 **포트 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 다음과 같은 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 이전 단계에서 생성된 보안 규칙을 변수에 저장합니다.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-frontend-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 프런트 엔드 IP를 만듭니다.

* 이름: **myFrontEnd**
* **10.0.0.4** 의 개인 IP 주소입니다.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 다음과 같은 백 엔드 주소 풀을 만듭니다. 

* 이름: **myBackEndPool**
* VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 다음과 같은 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 다음과 같은 부하 분산 장치 규칙을 만듭니다. 

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe** 를 사용합니다.
* 프로토콜: **TCP**
* **15분** 의 유휴 제한 시간.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 다음과 같은 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* 위치: **eastus**
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>네트워크 인터페이스 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 다음과 같은 세 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm-1"></a>VM 1

* 이름: **myNicVM1**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool** 의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 이름: **myNicVM2**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool** 의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>가상 머신에 대한 가용성 집합 만들기

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm)를 사용하여 다음과 같은 가상 머신에 대한 가용성 집합을 만듭니다.

* 이름: **myAvSet**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = Get-Credential
```

다음과 같은 가상 머신을 만듭니다.

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 이름: **myVM1**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨
* 위치: **eastus**
* 가용성 집합: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* 이름: **myVM2**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM2** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨
* 위치: **eastus**
* 가용성 집합: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

세 VM을 만들고 구성하는 데 몇 분 정도 소요됩니다.

---

## <a name="install-iis"></a>IIS 설치

[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 

확장은 PowerShell Add-WindowsFeature Web-Server를 실행하여 IIS 웹 서버를 설치한 다음, Default.htm 페이지를 업데이트하여 VM의 호스트 이름을 표시합니다.

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

### <a name="create-network-interface"></a>네트워크 인터페이스 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 네트워크 인터페이스를 만듭니다.

#### <a name="mytestvm"></a>myTestVM

* 이름은 **myNicTestVM** 입니다.
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>가상 머신 만들기

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = Get-Credential
```

다음을 사용하여 가상 머신을 만듭니다.

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* 이름은 **myTestVM** 입니다.
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicTestVM** 에 연결됩니다.
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>테스트

1. Azure Portal에 [로그인](https://portal.azure.com)합니다.

1. **개요** 화면에서 부하 분산 장치의 개인 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, **myLoadBalancer** 를 선택합니다.

2. **myLoadBalancer** 의 **개요** 에서 **개인 IP 주소** 옆에 있는 주소를 적어 두거나 복사해 둡니다.

3. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **CreateIntLBQS-rg** 리소스 그룹에 있는 **myTestVM** 을 선택합니다.

4. **개요** 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

6. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

7. **myTestVM** 에서 **Internet Explorer** 를 엽니다.

8. 이전 단계의 IP 주소를 브라우저의 주소 표시줄에 입력합니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="표준 내부 부하 분산 장치 만들기" border="true":::
   
세 개의 VM 모두에서 부하 분산 장치 분산 트래픽을 확인하려면 각 VM의 IIS 웹 서버의 기본 페이지를 사용자 지정한 다음, 클라이언트 컴퓨터에서 웹 브라우저를 강제로 새로 고칠 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 나머지 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음 작업을 수행했습니다.

* 표준 또는 기본 내부 부하 분산 장치를 만들었습니다.
* 가상 머신을 연결했습니다. 
* 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성했습니다.
* 부하 분산 장치를 테스트했습니다.

Azure Load Balancer에 대해 자세히 알아보려면 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure Load Balancer란?](load-balancer-overview.md)
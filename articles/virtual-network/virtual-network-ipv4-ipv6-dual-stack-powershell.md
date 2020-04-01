---
title: IPv6 듀얼 스택 애플리케이션 배포 - 기본 로드 밸러저 - PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Powershell을 사용하여 Azure 가상 네트워크에서 IPv6 듀얼 스택 응용 프로그램을 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3cfe80962c11e37c79549a74d7e4b19cd08f4684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420914"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>기본 로드 밸런서 - PowerShell을 사용하여 IPv6 듀얼 스택 애플리케이션 배포

이 문서에서는 듀얼 스택 가상 네트워크 및 서브넷을 포함하는 Azure PowerShell, 듀얼(IPv4 + IPv6) 프런트 엔드 구성이 있는 기본 로드 밸런서, 듀얼 IP 구성이 있는 NIC가 있는 VM, 네트워크 보안 그룹 및 공용 IP를 사용하는 기본 로드 밸런서를 사용하여 이중 스택(IPv4 + IPv6) 응용 프로그램을 배포하는 방법을 보여 주십니다.

표준 로드 밸런서를 사용하여 이중 스택(IPV4 + IPv6) 응용 프로그램을 배포하려면 [Azure PowerShell을 사용하여 표준 로드 밸런서를 사용하여 IPv6 이중 스택 응용 프로그램 배포를](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)참조하십시오.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

듀얼 스택 가상 네트워크를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제는 *우리 동쪽* 위치에 *myRGDualStack라는* 리소스 그룹을 만듭니다.

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 가상 컴퓨터에 액세스하려면 로드 밸런서에 대한 IPv4 및 IPv6 공용 IP 주소가 필요합니다. [New-AzPublicIpAddress를](/powershell/module/az.network/new-azpublicipaddress)사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *dsRG1* 리소스 그룹에서 *dsPublicIP_v4* 및 *dsPublicIP_v6라는* IPv4 및 IPv6 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
RDP 연결을 사용하여 가상 시스템에 액세스하려면 [New-AzPublicIpAddress를](/powershell/module/az.network/new-azpublicipaddress)사용하여 가상 시스템에 대한 IPV4 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 부하 분산에 대한 이중 프런트 엔드 IP(IPv4 및 IPv6) 및 백 엔드 주소 풀을 구성한 다음 기본 로드 밸러블러를 만듭니다.

### <a name="create-front-end-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 다음 예제는 *dsLbFrontEnd_v4* 및 *dsLbFrontEnd_v6*라는 IPv4 및 IPv6 프런트 엔드 IP 구성을 만듭니다.

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig를](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다. 다음 예제는 *dsLbBackEndPool_v4* 및 *dsLbBackEndPool_v6* 라는 백 엔드 주소 풀을 만들어 IPV4 및 IPv6 NIC 구성이 모두 있는 VM을 포함합니다.

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>상태 프로브 만들기
[추가 AzLoadBalancerProbeConfig를](/powershell/module/az.network/add-azloadbalancerprobeconfig) 사용하여 VM의 상태를 모니터링하는 상태 프로브를 만듭니다.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 선택적으로 상태 프로브를 정의할 수 있습니다. 기본 로드 밸런서에는 IPv4 프로브를 사용하여 VM의 IPv4 및 IPv6 끝점의 상태를 평가합니다. 표준 로드 밸러블러에는 명시적으로 IPv6 상태 프로브에 대한 지원이 포함되어 있습니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 *dsLBrule_v4* 및 *dsLBrule_v6* 라는 로드 밸러버 규칙을 만들고 *TCP* 포트 *80의* 트래픽을 IPv4 및 IPv6 프런트 엔드 IP 구성으로 균형을 조정합니다.

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 기본 Load Balancer를 만듭니다. 다음 예제에서는 이전 단계에서 만든 IPv4 및 IPv6 프런트 엔드 IP 구성, 백 엔드 풀 및 로드 균형 규칙을 사용하여 *myLoadBalancer라는* 공용 기본 로드 밸런서를 만듭니다.

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
일부 VM을 배포하고 밸러블러를 테스트하려면 가용성 집합, 네트워크 보안 그룹, 가상 네트워크 및 가상 NIC와 같은 지원 네트워크 리소스를 만들어야 합니다. 
### <a name="create-an-availability-set"></a>가용성 집합 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet*이라는 가용성 집합을 만듭니다.

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNET에서 인바운드 및 아웃바운드 통신을 제어하는 규칙에 대한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>포트 3389에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통해 RDP 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기

네트워크 보안 그룹 규칙을 만들어 포트 80을 통해 인터넷 연결을 허용하는 [경우 New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*을 만듭니다.

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC 만들기

[New-AzNetworkInterface를](/powershell/module/az.network/new-aznetworkinterface)사용하여 가상 NIC를 만듭니다. 다음 예제는 IPv4 및 IPv6 구성을 모두 갖춘 두 개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩)

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IPv4 및 IPv6 끝점의 IP 주소 결정
리소스 그룹의 모든 네트워크 인터페이스 개체를 사용하여 이 배포에 사용된 `get-AzNetworkInterface`IP를 요약합니다. 또한 `get-AzpublicIpAddress`로드 밸러처의 IPv4 및 IPv6 끝점의 프런트 엔드 주소를 .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
다음 그림은 두 VM의 개인 IPv4 및 IPv6 주소와 로드 밸러터의 프런트 엔드 IPv4 및 IPv6 IP 주소를 나열하는 샘플 출력을 보여 주며,

![Azure에서 이중 스택(IPv4/IPv6) 응용 프로그램 배포의 IP 요약](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure 포털에서 IPv6 듀얼 스택 가상 네트워크 보기
다음과 같이 Azure 포털에서 IPv6 듀얼 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에서 *dsVnet*을 입력합니다.
2. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다. 그러면 *dsVnet이라는*듀얼 스택 가상 네트워크의 **개요** 페이지가 시작됩니다. 듀얼 스택 가상 네트워크는 *dsSubnet라는*듀얼 스택 서브넷에 있는 IPv4 및 IPv6 구성이 있는 두 NIC를 보여줍니다.

  ![Azure의 IPv6 듀얼 스택 가상 네트워크](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성(IPv4 및 IPv6)이 있는 기본 로드 밸러커를 만들었습니다. 또한 부하 분산기의 백 엔드 풀에 추가된 듀얼 IP 구성(IPV4 + IPv6)이 포함된 두 개의 가상 컴퓨터를 만들었습니다. Azure 가상 네트워크에서 IPv6 지원에 대해 자세히 알아보려면 [Azure 가상 네트워크에 대한 IPv6이란 무엇입니까?](ipv6-overview.md)

---
title: Azure 가상 네트워크에서 IPv6로 IPv4 응용 프로그램 업그레이드 - PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Powershell을 사용하여 Azure 가상 네트워크의 기존 응용 프로그램에 IPv6 주소를 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420773"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Azure 가상 네트워크에서 IPv6로 IPv4 응용 프로그램 업그레이드 - PowerShell

이 문서에서는 표준 로드 밸런서 및 공용 IP가 있는 Azure 가상 네트워크의 기존 IPv4 응용 프로그램에 IPv6 연결을 추가하는 방법을 보여 주십니다. 현재 업그레이드에는 다음이 포함됩니다.
- 가상 네트워크 및 서브넷을 위한 IPv6 주소 공간
- IPv4 및 IPV6 프런트 엔드 구성을 모두 갖춘 표준 부하 밸러커
- IPv4 + IPv6 구성이 모두 있는 NIC가 있는 VM
- IPv6 공용 IP이므로 로드 밸런서에 인터넷 연결 IPv6 연결이 있습니다.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [빠른 시작: 표준 로드 밸러커 만들기 - Azure PowerShell에](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)설명된 대로 표준 로드 밸러서를 배포했다고 가정합니다.

## <a name="retrieve-the-resource-group"></a>리소스 그룹 검색

듀얼 스택 가상 네트워크를 만들려면 먼저 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)을 사용하여 리소스 그룹을 검색해야 합니다.

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP 주소 만들기

표준 로드 밸런서에 대한 [New-AzPublicIpAddress를](/powershell/module/az.network/new-azpublicipaddress) 사용하여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *myResourceGroupSLB* 리소스 그룹에 *PublicIP_v6* 라는 IPv6 공용 IP 주소를 만듭니다.

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>로드 밸론터 프런트 엔드 구성

기존 로드 밸러서 구성을 검색한 다음 다음과 같이 [Add-AzLoadBalancerFrontendIpConfig를](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) 사용하여 새 IPv6 IP 주소를 추가합니다.

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>로드 밸로이터 백엔드 풀 구성

로드 밸런서 구성의 로컬 복사본에 백 엔드 풀을 만들고 다음과 같이 새 백 엔드 풀 구성으로 실행 중인 로드 밸런서를 업데이트합니다.

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>부하 분산 장치 규칙 구성
기존 로드 밸런서 프런트 엔드 및 백 엔드 풀 구성을 검색한 다음 [Add-AzLoadBalancerRuleConfig를](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)사용하여 새 로드 밸런싱 규칙을 추가합니다.

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 주소 범위 추가

다음과 같이 VM을 호스팅하는 가상 네트워크 및 서브넷에 IPv6 주소 범위를 추가합니다.

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>NIC에 IPv6 구성 추가

다음과 같이 [Add-AzNetworkInterfaceIpConfig를](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) 사용하여 IPv6 주소로 모든 VM NIC를 구성합니다.

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure 포털에서 IPv6 듀얼 스택 가상 네트워크 보기
다음과 같이 Azure 포털에서 IPv6 듀얼 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에서 *myVnet을*입력합니다.
2. **myVnet이** 검색 결과에 나타나면 선택합니다. 그러면 *myVNet이라는*듀얼 스택 가상 네트워크의 **개요** 페이지가 시작됩니다. 듀얼 스택 가상 네트워크는 *mySubnet라는*듀얼 스택 서브넷에 있는 IPv4 및 IPv6 구성을 모두 갖춘 3개의 NIC를 보여줍니다.

  ![Azure의 IPv6 듀얼 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용하여 기존 표준 로드 밸런서를 이중 스택(IPv4 및 IPv6) 구성으로 업데이트했습니다. 또한 백 엔드 풀의 VM의 NIC와 이를 호스팅하는 가상 네트워크에 IPv6 구성을 추가했습니다. Azure 가상 네트워크에서 IPv6 지원에 대해 자세히 알아보려면 [Azure 가상 네트워크에 대한 IPv6이란 무엇입니까?](ipv6-overview.md)

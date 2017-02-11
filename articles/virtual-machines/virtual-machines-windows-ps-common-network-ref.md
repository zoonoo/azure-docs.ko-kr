---
title: "VM에 대한 공통 네트워크 PowerShell 명령 | Microsoft Docs"
description: "Vm에 대한 가상 네트워크 및 연결된 리소스 만들기를 시작하게 하는 공통 PowerShell 명령."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Vm에 대한 공통 네트워크 Azure PowerShell 명령
가상 컴퓨터를 만들려는 경우, [가상 네트워크](../virtual-network/virtual-networks-overview.md) 를 만들거나 VM을 추가할 수 있는 기존 가상 네트워크에 대해 알아야 합니다. 일반적으로 VM을 만들 때 이 문서에 설명된 리소스를 만드는 것이 좋습니다.

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="create-network-resources"></a>네트워크 리소스 만들기
| 작업 | 명령 |
| --- | --- |
| 서브넷 구성 만들기 |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR><BR>일반적인 네트워크는 [인터넷 연결 부하 분산 장치](../load-balancer/load-balancer-internet-overview.md)에 대한 서브넷 및 [내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md)에 대한 별도 서브넷을 가질 수도 있습니다. |
| 가상 네트워크 만들기 |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name" -Location "location_name" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 고유한 도메인 이름에 대한 테스트 |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "domain_name" -Location "location_name"<BR><BR>Azure 관리 DNS 서버에서 공용 IP 주소에 대한 domainname.location.cloudapp.azure.com 매핑을 만드는 [공용 IP 리소스](../virtual-network/virtual-network-ip-addresses-overview-arm.md)에 대한 DNS 도메인 이름을 지정할 수 있습니다. 이 이름에는 문자, 숫자 및 하이픈만 포함할 수 있습니다. 첫 번째 및 마지막 문자는 문자 또는 숫자여야 하며 도메인 이름은 Azure 위치 내에서 고유해야 합니다. **True** 가 반환된 경우 제안한 이름이 전역적으로 고유합니다. |
| 공용 IP 주소 만들기 |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "ip_address_name" -ResourceGroupName "resource_group_name" -DomainNameLabel "domain_name" -Location "location_name" -AllocationMethod Dynamic<BR><BR>공용 IP 주소는 이전에 테스트했고 부하 분산 장치의 프런트 엔드 구성에서 사용되는 도메인 이름을 사용합니다. |
| 프런트 엔드 IP 구성 만들기 |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "frontend_ip_name" -PublicIpAddress $pip<BR><BR>프런트 엔드 구성은 들어오는 네트워크 트래픽에 대해 이전에 만든 공용 IP 주소를 포함합니다. |
| 백 엔드 주소 풀 만들기 |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "backend_pool_name"<BR><BR>네트워크 인터페이스를 통해 액세스 하는 부하 분산 장치의 백 엔드에 대한 내부 주소를 제공합니다. |
| 프로브 만들기 |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "probe_name" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>백 엔드 주소 풀의 가상 컴퓨터 인스턴스 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다. |
| 부하 분산 규칙 만들기 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 할당하는 규칙을 포함합니다. |
| 인바운드 NAT 규칙 만들기 |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "rule_name" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>백 엔드 주소 풀에서 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치에는 공용 포트 매핑 규칙을 포함합니다. |
| 부하 분산 장치 만들기 |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "resource_group_name" -Name "load_balancer_name" -Location "location_name" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 네트워크 인터페이스 만들기 |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "resource_group_name" -Name "network_interface_name" -Location "location_name" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>이전에 만든 공용 IP 주소 및 가상 네트워크 서브넷을 사용하여 네트워크 인터페이스를 만듭니다. |

## <a name="get-information-about-network-resources"></a>네트워크 리소스에 대한 정보 가져오기
| 작업 | 명령 |
| --- | --- |
| 가상 네트워크 나열 |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹의 모든 가상 네트워크를 나열합니다. |
| 가상 네트워크에 대한 정보 가져오기 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" |
| 가상 네트워크의 서브넷 나열 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" &#124; Select Subnets |
| 서브넷에 대한 정보 가져오기 |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>지정된 가상 네트워크의 서브넷에 대한 정보를 가져옵니다. $vnet 값은 Get-AzureRmVirtualNetwork에서 반환되는 개체를 나타냅니다. |
| IP 주소 나열 |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹의 공용 IP 주소를 나열합니다. |
| 부하 분산 장치 나열 |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹의 모든 부하 분산 장치를 나열합니다. |
| 네트워크 인터페이스 나열 |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹의 모든 네트워크 인터페이스를 나열합니다. |
| 네트워크 인터페이스에 대한 정보 가져오기 |Get-AzureRmNetworkInterface -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>특정 네트워크 인터페이스에 대한 정보를 가져옵니다. |
| 네트워크 인터페이스의 IP 구성 가져오기 |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "ipconfiguration_name" -NetworkInterface $nic<BR><BR>지정된 네트워크 인터페이스의 IP 구성에 대한 정보를 가져옵니다. $nic 값은 Get-AzureRmNetworkInterface에서 반환되는 개체를 나타냅니다. |

## <a name="manage-network-resources"></a>네트워크 리소스 관리
| 작업 | 명령 |
| --- | --- |
| 가상 네트워크에 서브넷 추가 |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>기존 가상 네트워크에 서브넷을 추가합니다. $vnet 값은 Get-AzureRmVirtualNetwork에서 반환되는 개체를 나타냅니다. |
| 가상 네트워크 삭제 |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹에서 지정된 가상 네트워크를 제거합니다. |
| 네트워크 인터페이스 삭제 |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹에서 지정된 네트워크 인터페이스를 제거합니다. |
| 부하 분산 장치 삭제 |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "load_balancer_name" -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹에서 지정된 부하 분산 장치를 제거합니다. |
| 공용 IP 주소 삭제 |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "ip_address_name" -ResourceGroupName "resource_group_name"<BR><BR>리소스 그룹에서 지정된 공용 IP 주소를 제거합니다. |

## <a name="next-steps"></a>다음 단계
* [VM 만들기](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 할 때 방금 만든 네트워크 인터페이스를 사용합니다.
* [여러 네트워크 인터페이스를 사용하여 VM 만들기](../virtual-network/virtual-networks-multiple-nics.md)를 할 수 있는 방법을 알아봅니다.




<!--HONumber=Dec16_HO2-->



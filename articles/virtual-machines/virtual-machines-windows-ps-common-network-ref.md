---
title: Vm에 대한 공통 네트워크 PowerShell 명령 | Microsoft Docs
description: Vm에 대한 가상 네트워크 및 연결된 리소스 만들기를 시작하게 하는 공통 PowerShell 명령.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2016
ms.author: davidmu

---
# Vm에 대한 공통 네트워크 Azure PowerShell 명령
가상 컴퓨터를 만들려는 경우, [가상 네트워크](../virtual-network/virtual-networks-overview.md)를 만들거나 VM을 추가할 수 있는 기존 가상 네트워크에 대해 알아야 합니다. 일반적으로 VM을 만들 때 이 문서에 설명된 리소스를 만드는 것이 좋습니다.

## Azure PowerShell을 사용하여 리소스 만들기
최신 버전의 Azure PowerShell을 설치하는 방법, 사용할 구독을 선택하는 방법, Azure 계정에 로그인하는 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

| 리소스 | 명령 |
| --- | --- |
| 서브넷 |$internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>일반적인 네트워크에는 [인터넷 연결 부하 분산 장치](../load-balancer/load-balancer-internet-overview.md)에 대한 서브넷과 [내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md)에 대한 별도 서브넷이 있을 수 있습니다. |
| 가상 네트워크 |가상 네트워크 만들기:<BR><BR>$rgName = "[resource-group-name](../powershell-azure-resource-manager.md)"<BR>$locName = "[location-name](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "virtual-network-name"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>가상 네트워크의 목록 가져오기:<BR><BR>[Get AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; Sort Name &#124; Select Name<BR><BR>가상 네트워크의 서브넷 목록:<BR><BR>Get AzureRmVirtualNetwork-$vnetName-ResourceGroupName $rgName & #124; 서브넷 선택<BR><BR>서브넷 목록을 보여 주는 다음과 같은 내용이 표시됩니다:<BR><BR>서브넷<BR>---<BR>{internetSubnet, internalSubnet}<BR><BR> internetSubnet에 대한 서브넷 인덱스는 0이고 internalSubnet에 대한 서브넷 인덱스는 1입니다. |
| 도메인 이름 레이블 |$domName = "domain-name"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>Azure 관리 DNS 서버에서 공용 IP 주소에 대한 domainnamelabel.location.cloudapp.azure.com 매핑을 만드는 [공용 IP 리소스](../virtual-network/virtual-network-ip-addresses-overview-arm.md)에 대한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 레이블에는 문자, 숫자 및 하이픈만 사용할 수 있습니다. 첫 번째 및 마지막 문자는 문자 또는 숫자여야 하며 도메인 이름 레이블은 Azure 위치 내에서 고유해야 합니다. 도메인 이름 레이블이 전역적으로 고유한지 항상 테스트해야 합니다. **True**가 반환된 경우 제안한 이름이 전역적으로 고유합니다. |
| 공용 IP 주소 |$ipName = "public-ip-address-name"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>공용 IP 주소는 이전에 만들었고 부하 분산 장치의 프런트 엔드 구성에서 사용되는 도메인 이름 레이블을 사용합니다. |
| 프런트 엔드 IP 구성 |$feConfigName = "frontend-ip-config-name"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>프런트 엔드 구성은 들어오는 네트워크 트래픽에 대해 이전에 만든 공용 IP 주소를 포함합니다. |
| 백 엔드 주소 풀 |$bePoolName = "back-end-pool-name"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>네트워크 인터페이스를 통해 액세스 하는 부하 분산 장치의 백 엔드에 대한 내부 주소를 제공합니다. |
| 프로브 |$probeName = "health-probe-name"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>백 엔드 주소 풀의 가상 컴퓨터 인스턴스 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다. |
| 부하 분산 규칙 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다. |
| 인바운드 NAT 규칙 |$ruleName = "NAT-rule-name"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>백 엔드 주소 풀에서 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치에는 공용 포트 매핑 규칙을 포함합니다. |
| 부하 분산 장치 |$lbName = "load-balancer-name"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 네트워크 인터페이스 |$vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "network-interface-name"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>이전에 만든 공용 IP 주소 및 가상 네트워크 서브넷을 사용하여 네트워크 인터페이스를 만듭니다. |

## 다음 단계
* [VM 만들기](virtual-machines-windows-ps-create.md)를 할 때 방금 만든 네트워크 인터페이스를 사용합니다.
* [여러 네트워크 인터페이스를 사용하여 VM 만들기](../virtual-network/virtual-networks-multiple-nics.md)를 할 수 있는 방법을 알아봅니다.

<!---HONumber=AcomDC_0629_2016-->
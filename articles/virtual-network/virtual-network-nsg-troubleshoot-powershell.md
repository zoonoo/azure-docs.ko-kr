---
title: 네트워크 보안 그룹 문제 해결 - PowerShell | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure Resource Manager 배포 모델에서 네트워크 보안 그룹 문제를 해결하는 방법에 알아봅니다.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 6beaeb4b7e5c9e393427d575f1cf8bc48599dbd5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Azure PowerShell을 사용하여 네트워크 보안 그룹 문제 해결
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

VM(가상 컴퓨터)에서 NSG(네트워크 보안 그룹)를 구성했으며 VM 연결 문제가 발생하는 경우 이 문서를 통해 문제 해결에 도움이 되는 NSG 진단 기능을 대략적으로 알 수 있습니다.

NSG에서는 VM(가상 머신)에서 들어오고 나가는 트래픽 유형을 제어할 수 있습니다. Azure VNet(Virtual Network), NIC(네트워크 인터페이스) 또는 둘 다의 서브넷에 NSG를 적용할 수 있습니다. NIC에 적용되는 유효한 규칙은 NIC 및 NIC에 연결된 서브넷에 적용되는 NSG에 존재하는 규칙을 집계한 것입니다. 때로는 이러한 NSG 간의 규칙이 서로 충돌하고 VM의 네트워크 연결에 영향을 줄 수 있습니다.  

VM의 NIC에 적용된 NSG의 모든 유효 보안 규칙을 볼 수 있습니다. 이 문서에서는 Azure Resource Manager 배포 모델에서 이러한 규칙을 사용하여 VM 연결 문제를 해결하는 방법을 보여 줍니다. VNet 및 NSG 개념에 익숙하지 않은 경우 [가상 네트워크](virtual-networks-overview.md) 및 [네트워크 보안 그룹](virtual-networks-nsg.md) 개요 문서를 읽어보세요.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>유효 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결
다음에 나오는 시나리오는 일반적인 연결 문제의 한 예입니다.

VM *VM1*은 *WestUS-VNet1*이라는 VNet 내에 있는 *Subnet1* 서브넷의 일부입니다. RDP over TCP 포트 3389 통해 VM에 연결하려고 하면 실패합니다. NSG는 NIC *VM1-NIC1*과 서브넷 *Subnet1* 둘 다에 적용됩니다. TCP 포트 3389로의 트래픽은 네트워크 인터페이스 *VM1-NIC1*에 연결된 NSG에서 허용되지만 VM1의 포트 3389에 대한 TCP ping은 실패합니다.

이 예제에서는 TCP 포트 3389를 사용하지만 다음 단계를 사용하여 임의 포트에 대한 인바운드 및 아웃바운드 연결 실패를 확인할 수 있습니다.

## <a name="detailed-troubleshooting-steps"></a>자세한 문제 해결 단계
VM에 대한 NSG 문제를 해결하려면 다음 단계를 완료합니다.

1. Azure PowerShell 세션을 시작하고 Azure에 로그인합니다. Azure PowerShell을 사용하는 데 친숙하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 문서를 읽어보세요. 사용자 계정은 네트워크 인터페이스에 대한 *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* 작업에 할당해야 합니다. 작업을 계정에 할당하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어의 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions)를 참조하세요.
2. 다음 명령을 입력하여 리소스 그룹 *RG1*의 NIC *VM1-NIC1*에 적용되는 모든 NSG 규칙을 반환합니다.
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > NIC의 이름을 모르는 경우 다음 명령을 입력하여 리소스 그룹에 있는 모든 NIC의 이름을 검색합니다. 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    다음 텍스트는 *VM1-NIC1* NIC에 대해 반환된 유효 규칙 출력의 샘플입니다.
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    출력의 다음 정보에 유의하세요.
   
   * 서브넷과 연결된 섹션(*Subnet1*)과 NIC와 연결된 섹션(*VM1-NIC1*)의 두 **NetworkSecurityGroup** 섹션이 있습니다. 이 예제에서는 NSG가 각 섹션에 적용되었습니다.
   * **Association** 에는 지정된 NSG와 연결된 리소스(서브넷 또는 NIC)가 표시됩니다. 이 명령을 실행하기 직전에 NSG 리소스가 이동/연결 해제되면 명령 출력에 변경 내용이 반영될 때까지 몇 초 정도 기다려야 할 수 있습니다. 
   * *defaultSecurityRules*로 시작하는 규칙 이름: NSG가 생성될 때 몇 가지 기본 보안 규칙이 생성됩니다. 기본 규칙은 제거할 수 없지만 더 높은 우선 순위 규칙으로 재정의할 수 있습니다.
     NSG 기본 보안 규칙에 대한 자세한 내용은 [NSG 개요](virtual-networks-nsg.md#default-rules) 문서를 읽어보세요.
   * **ExpandedAddressPrefix** 는 NSG 기본 태그의 주소 접두사를 확장합니다. 태그는 여러 주소 접두사를 나타냅니다. 특정 주소 접두사와의 VM 연결 문제를 해결할 때 태그를 확장하는 것이 유용할 수 있습니다. 예를 들어 VNET 피어링을 사용할 경우 VIRTUAL_NETWORK 태그는 이전 출력의 피어링된 VNet 접두사를 표시하도록 확장됩니다.
     
     > [!NOTE]
     > 이 명령은 NSG가 서브넷이나 NIC 또는 둘 다와 연결된 경우에만 유효 규칙을 보여 줍니다. 하나의 VM에 다른 NSG가 적용되는 여러 NIC가 있을 수 있습니다. 문제를 해결할 때 각 NIC에 대해 해당 명령을 실행합니다.
     > 
     > 
3. 많은 수의 NSG 규칙에 대해 간편하게 필터링을 수행하려면 다음 명령을 입력하여 추가적으로 문제를 해결합니다. 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    다음 그림과 같이 RDP 트래픽(TCP 포트 3389)에 대한 필터가 표 보기에 적용됩니다.
   
    ![규칙 목록](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. 표 보기에서 볼 수 있는 것처럼 RDP에 대한 허용 규칙과 거부 규칙이 있습니다. 2단계의 출력에는 *DenyRDP* 규칙이 서브넷에 적용된 NSG에 있다는 사실이 표시됩니다. 인바운드 규칙의 경우 서브넷에 적용된 NSG가 먼저 처리됩니다. 일치 항목이 발견되면 네트워크 인터페이스에 적용된 NSG는 처리되지 않습니다. 이 경우 서브넷의 *DenyRDP* 규칙이 VM(**VM1**)에 대한 RDP를 차단합니다.
   
   > [!NOTE]
   > 하나의 VM에 여러 NIC가 연결되어 있을 수 있습니다. 각각이 다른 서브넷에 연결될 수 있습니다. 이전 단계의 명령은 NIC에 대해 실행되므로 연결 오류가 발생하는 NIC를 지정하는 것이 중요합니다. 확실하지 않은 경우 항상 VM에 연결된 각 NIC에 대해 명령을 실행할 수 있습니다.
   > 
   > 
5. VM1에 대한 RDP의 경우 **Subnet1-NSG** NSG에서 *RDP 거부(3389)* 규칙을 *RDP 허용(3389)*으로 변경합니다. VM에 대한 RDP 연결을 열거나 PsPing 도구를 사용하여 TCP 포트 3389가 열려 있는지 확인합니다. [PsPing 다운로드 페이지](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    다음 명령의 출력에 포함된 정보를 사용하여 NSG에서 규칙을 제거할 수 있습니다.
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>고려 사항
연결 문제를 해결하는 경우 다음 사항을 고려합니다.

* 기본 NSG 규칙은 인터넷의 인바운드 액세스를 차단하고 VNet 인바운드 트래픽만 허용합니다. 필요에 따라 인터넷의 인바운드 액세스를 허용하는 규칙을 명시적으로 추가해야 합니다.
* VM의 네트워크 연결이 실패하도록 하는 NSG 보안 규칙이 없는 경우 다음이 문제의 원인일 수 있습니다.
  * VM의 운영 체제 내에서 방화벽 소프트웨어가 실행되고 있습니다.
  * 가상 어플라이언스 또는 온-프레미스 트래픽에 대해 경로가 구성되었습니다. 강제 터널링을 통해 인터넷 트래픽이 온-프레미스로 리디렉션될 수 있습니다. 온-프레미스 네트워크 하드웨어가 이 트래픽을 처리하는 방법에 따라 인터넷에서 VM으로의 RDP/SSH 연결이 이 설정에서 작동하지 않을 수 있습니다. VM에서 들어오고 나가는 트래픽 흐름을 방해할 수 있는 경로 문제를 진단하는 방법을 자세히 알아보려면 [경로 문제 해결](virtual-network-routes-troubleshoot-powershell.md) 문서를 읽어보세요. 
* VNet을 피어링한 경우 기본적으로, VIRTUAL_NETWORK 태그는 피러링된 VNet에 대한 접두사를 포함하도록 자동으로 확장됩니다. VNet 피어링 연결과 관련된 문제를 해결하기 위해 **ExpandedAddressPrefix** 목록에서 이러한 접두사를 볼 수 있습니다. 
* 유효 보안 규칙은 VM의 NIC 및/또는 서브넷에 연결된 NSG가 있을 때만 표시됩니다. 
* NIC 또는 서브넷과 연결된 NSG가 없고 VM에 할당된 공용 IP 주소가 있는 경우 모든 포트가 인바운드 및 아웃바운드 액세스를 위해 열립니다. VM에 공용 IP 주소가 있을 때는 NIC 또는 서브넷에 NSG를 적용하는 것이 강력하게 권장됩니다.  


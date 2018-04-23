---
title: 경로 문제 해결 - PowerShell | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure Resource Manager 배포 모델에서 경로 문제를 해결하는 방법에 알아봅니다.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 2d501419dde633f89a5760af9f82604006de6b6f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Azure PowerShell을 사용하여 경로 문제 해결
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Azure VM(Virtual Machine)과의 네트워크 연결 문제가 발생하는 경우 경로가 VM 트래픽 흐름에 영향을 미칠 수 있습니다. 이 문서에서는 추가 문제 해결을 위해 경로에 대한 진단 기능 개요를 제공합니다.

경로 테이블은 서브넷과 연결되며 해당 서브넷의 모든 NIC(네트워크 인터페이스)에서 유효합니다. 다음 유형의 경로를 각 네트워크 인터페이스에 적용할 수 있습니다.

* **시스템 경로:** 기본적으로 Azure VNet(Virtual Network)에서 생성된 모든 서브넷에는 로컬 VNet 트래픽, VPN Gateway를 통한 온-프레미스 트래픽 및 인터넷 트래픽을 허용하는 시스템 경로 테이블이 있습니다. 또한 피어링된 VNet에 대한 시스템 경로도 있습니다.
* **BGP 경로:** ExpressRoute 또는 사이트 간 VPN 연결을 통해 네트워크 인터페이스로 전파됩니다. [VPN Gateway가 있는 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 및 [ExpressRoute 개요](../expressroute/expressroute-introduction.md) 문서를 읽어 BGP 라우팅에 대해 자세히 알아봅니다.
* **UDR(사용자 정의 경로):** 네트워크 가상 어플라이언스를 사용하거나 사이트 간 VPN을 통해 온-프레미스 네트워크로 트래픽을 강제 터널링하는 경우 UDR(사용자 정의 경로)이 서브넷 경로 테이블에 연결될 수 있습니다. UDR에 익숙하지 않은 경우 [사용자 정의 경로](virtual-networks-udr-overview.md#user-defined) 문서를 읽어보세요.

네트워크 인터페이스에 적용할 수 있는 다양한 경로를 사용하는 경우 유효한 집계 경로를 파악하기 어려울 수 있습니다. VM 네트워크 연결 문제를 해결하려면 Azure Resource Manager 배포 모델에서 네트워크 인터페이스에 대한 모든 유효 경로를 볼 수 있습니다.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>유효 경로를 사용하여 VM 트래픽 흐름 문제 해결
이 문서에서는 다음과 같은 시나리오를 예제로 사용하여 네트워크 인터페이스에 대한 유효 경로 문제를 해결하는 방법을 보여 줍니다.

VNet(*VNet1*, 접두사: 10.9.0.0/16)에 연결된 VM(*VM1*)이 새로 피어링된 VNet(*VNet3*, 접두사 10.10.0.0/16)의 VM(VM3)에 연결하지 못합니다. VM에 연결된 VM1-NIC1 네트워크 인터페이스에 적용된 UDR 또는 BGP 경로가 없으면 시스템 경로만 적용됩니다.

이 문서에서는 Azure 리소스 관리 배포 모델에서 유효 경로 기능을 사용하여 연결 실패의 원인을 확인하는 방법을 설명합니다.
이 예제에서는 시스템 경로만 사용하지만 동일한 단계를 사용하여 임의 경로 유형에 대한 인바운드 및 아웃바운드 연결 실패를 확인할 수 있습니다.

> [!NOTE]
> VM에 연결된 NIC가 두 개 이상 있으면 각 NIC에 대한 유효 경로를 확인하여 VM에서 들어오고 나가는 네트워크 연결 문제를 진단합니다.
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>가상 머신에 대한 유효 경로 보기
VM에 적용되는 집계 경로를 확인하려면 다음 단계를 완료합니다.

### <a name="view-effective-routes-for-a-network-interface"></a>네트워크 인터페이스에 대한 유효 경로 보기
네트워크 인터페이스에 적용되는 집계 경로를 확인하려면 다음 단계를 완료합니다.

1. Azure PowerShell 세션을 시작하고 Azure에 로그인합니다. Azure PowerShell에 친숙하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 문서를 읽어보세요. 사용자 계정은 네트워크 인터페이스에 대한 *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* 작업에 할당해야 합니다. 작업을 계정에 할당하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어의 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions)를 참조하세요.
2. 다음 명령은 을 입력하여 리소스 그룹 *RG1*의 네트워크 인터페이스 *VM1-NIC1*에 적용되는 모든 경로를 반환합니다.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > 네트워크 인터페이스의 이름을 모르는 경우 다음 명령을 입력하여 리소스 그룹에 있는 모든 네트워크 인터페이스의 이름을 검색합니다.*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   다음 출력은 NIC가 연결된 서브넷에 적용된 각 경로에 대한 출력과 비슷합니다.
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   출력에서 다음을 확인합니다.
   
   * **Name**: 명시적으로 지정하지 않은 경우 사용자 정의 경로에 대한 유효 경로의 이름은 비워 둘 수 있습니다. 
   * **State**: 유효 경로의 상태를 나타냅니다. 가능한 값은 “Active” 또는 “Invalid”입니다.
   * **AddressPrefixes**: CIDR 표기법에 따라 유효 경로의 주소 접두사를 지정합니다. 
   * **nextHopType**: 지정된 경로에 대한 다음 홉을 나타냅니다. 가능한 값은 *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* 또는 *Null*입니다. UDR에서 *nextHopType* 값이 **Null** 이면 잘못된 경로를 나타낼 수 있습니다. 예를 들어 **nextHopType** 이 *VirtualAppliance* 이고 네트워크 가상 어플라이언스 VM이 프로비전됨/실행 중 상태가 아닙니다. **nextHopType** 이 *VPNGateway* 이고 지정된 VNet에서 프로비전됨/실행 중 상태인 게이트웨이가 없으면 경로가 유효하지 않을 수 있습니다.
   * **NextHopIpAddress**: 유효 경로의 다음 홉에 대한 IP 주소를 지정합니다.
   
   다음 명령은 좀 더 보기 쉬운 표 형태로 경로를 반환합니다.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   다음 출력은 앞에서 설명한 시나리오에 대해 수신되는 출력의 일부입니다.
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. 이전 단계의 출력에서 *WestUS-VNet1*(접두사 10.9.0.0/16)으로부터 *WestUS-VNet3* VNet(접두사 10.10.0.0/16)**으로의 경로는 나열되지 않습니다. 다음 그림과 같이 *WestUS-VNet3* VNet과의 VNet 피어링 링크는 *연결 끊김* 상태입니다.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    피어링에 대한 양방향 링크가 끊어졌습니다. 이것은 *WestUS-VNet3* VNet에서 VM1이 VM3에 연결할 수 없는 이유를 설명합니다. *WestUS-VNet1* 및 *WestUS-VNet3* VNet에 대해 양방향 VNet 피어링 링크를 다시 설정합니다. VNet 피어링 링크가 올바르게 설정된 후 반환되는 출력은 다음과 같습니다.
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    문제를 확인한 경우 경로 및 경로 테이블을 추가, 제거 또는 변경할 수 있습니다. 이 작업에 사용되는 명령 목록을 보려면 다음 명령을 입력합니다.
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>고려 사항
반환된 경로 목록을 검토할 때 몇 가지 사항에 유의해야 합니다.

* 라우팅은 UDR, BGP 및 시스템 경로 중에서 LPM(가장 긴 접두사 일치)을 기준으로 합니다. LPM 일치가 동일한 경로가 두 개 이상 있으면 다음 순서대로 해당 원점에 따라 경로가 선택됩니다.
  
  * 사용자 정의 경로
  * BGP 경로
  * 시스템(기본) 경로
    
    유효 경로를 사용하면 사용 가능한 모든 경로를 기준으로 LPM 일치에 해당하는 유효 경로만 표시될 수 있습니다. 이 경우 지정된 NIC에 대해 경로가 실제로 평가되는 방식이 표시되므로 VM과의 연결에 영향을 미칠 수 있는 특정 경로 문제를 훨씬 더 쉽게 해결할 수 있습니다.
* UDR이 있고 *VirtualAppliance*를 **nextHopType**으로 지정하여 NVA(네트워크 가상 어플라이언스)로 트래픽을 보내는 경우 트래픽을 수신하는 NVA에 대해 IP 전달이 사용되도록 설정되어 있는지 확인합니다. 그렇지 않으면 패킷이 삭제됩니다. 
* 강제 터널링을 사용하도록 설정하면 모든 아웃바운드 인터넷 트래픽이 온-프레미스로 라우팅됩니다. 온-프레미스가 이 트래픽을 처리하는 방법에 따라 인터넷에서 VM으로의 RDP/SSH가 이 설정에서 작동하지 않을 수 있습니다. 
  다음과 같이 강제 터널링을 사용하도록 설정할 수 있습니다.
  * nextHopType을 VPN Gateway로 지정하고 UDR(사용자 정의 경로) 설정하여 사이트 간 VPN을 사용하는 경우
  * 기본 경로가 BGP를 통해 보급되는 경우
* VNet 피어링 트래픽이 제대로 작동하려면 피어링된 VNet의 접두사 범위에 대해 **nextHopType** *VNetPeering* 의 시스템 경로가 존재해야 합니다. 이러한 경로가 존재하지 않고 VNet 피어링 링크에 문제가 없어 보이는 경우
  * 몇 초 동안 기다린 후 새로 설정된 피어링 링크가 있으면 다시 시도합니다. 경우에 따라 서브넷에 있는 모든 네트워크 인터페이스로 경로를 전파하는 데 시간이 더 오래 걸립니다.
  * NSG(네트워크 보안 그룹) 규칙이 트래픽 흐름에 영향을 미칠 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 문제 해결](virtual-network-nsg-troubleshoot-powershell.md) 문서를 참조하세요.


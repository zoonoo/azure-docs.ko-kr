---
title: 네트워크 보안 그룹 문제 해결 - 포털 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Resource Manager 배포 모델에서 네트워크 보안 그룹 문제를 해결하는 방법에 알아봅니다.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 67ffe826ba13576578e8f09e36f84128f4ceb0f2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366497"
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Azure Portal을 사용하여 네트워크 보안 그룹 문제 해결
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

VM(가상 컴퓨터)에서 NSG(네트워크 보안 그룹)를 구성했으며 VM 연결 문제가 발생하는 경우 이 문서를 통해 문제 해결에 도움이 되는 NSG 진단 기능을 대략적으로 알 수 있습니다.

NSG에서는 VM(가상 머신)에서 들어오고 나가는 트래픽 유형을 제어할 수 있습니다. Azure VNet(Virtual Network), NIC(네트워크 인터페이스) 또는 둘 다의 서브넷에 NSG를 적용할 수 있습니다. NIC에 적용되는 유효한 규칙은 NIC 및 NIC에 연결된 서브넷에 적용되는 NSG에 존재하는 규칙을 집계한 것입니다. 때로는 이러한 NSG 간의 규칙이 서로 충돌하고 VM의 네트워크 연결에 영향을 줄 수 있습니다.

VM의 NIC에 적용된 NSG의 모든 유효 보안 규칙을 볼 수 있습니다. 이 문서에서는 Azure Resource Manager 배포 모델에서 이러한 규칙을 사용하여 VM 연결 문제를 해결하는 방법을 보여 줍니다. VNet 및 NSG 개념에 익숙하지 않은 경우 [가상 네트워크 개요](virtual-networks-overview.md) 및 [네트워크 보안 그룹 개요](security-overview.md)를 참조하세요.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>유효 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결
다음에 나오는 시나리오는 일반적인 연결 문제의 한 예입니다.

VM *VM1*은 *WestUS-VNet1*이라는 VNet 내에 있는 *Subnet1* 서브넷의 일부입니다. RDP over TCP 포트 3389 통해 VM에 연결하려고 하면 실패합니다. NSG는 NIC *VM1-NIC1*과 서브넷 *Subnet1* 둘 다에 적용됩니다. TCP 포트 3389로의 트래픽은 네트워크 인터페이스 *VM1-NIC1*에 연결된 NSG에서 허용되지만 VM1의 포트 3389에 대한 TCP ping은 실패합니다.

이 예제에서는 TCP 포트 3389를 사용하지만 다음 단계를 사용하여 임의 포트에 대한 인바운드 및 아웃바운드 연결 실패를 확인할 수 있습니다.

### <a name="vm"></a>가상 컴퓨터에 대한 유효 보안 규칙 보기
VM에 대한 NSG 문제를 해결하려면 다음 단계를 완료합니다.

VM 자체에서 NIC에 대한 유효 보안 규칙의 전체 목록을 볼 수 있습니다. 이러한 작업을 수행할 권한이 있는 경우 유효 규칙 블레이드에서 NIC 및 서브넷 NSG 규칙을 추가, 수정 및 삭제할 수도 있습니다.

1. Azure 계정을 사용하여 https://portal.azure.com에서 Azure Portal에 로그인합니다. 사용자 계정은 네트워크 인터페이스에 대한 *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* 작업에 할당해야 합니다. 작업을 계정에 할당하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어의 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. **모든 서비스**를 클릭한 다음, 표시되는 목록에서 **가상 머신**을 클릭합니다.
3. 표시되는 목록에서 문제를 해결할 VM을 선택합니다. 그러면 옵션을 포함하는 VM 블레이드가 나타납니다.
4. **문제 진단 및 해결**을 클릭하고 일반적인 문제를 선택합니다. 이 예제에서는 **Windows VM에 연결할 수 없습니다.** 가 선택됩니다. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. 다음 그림과 같이 문제 아래에 단계가 나타납니다. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    권장 단계 목록에서 *유효 보안 그룹 규칙* 을 클릭합니다.
6. 다음 그림과 같이 **유효 보안 규칙 가져오기** 블레이드가 나타납니다.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    그림의 다음 섹션을 확인합니다.
   
   * **범위:** 3단계에서 선택한 VM인 *VM1*으로 설정합니다.
   * **네트워크 인터페이스:** *VM1-NIC1* 이 선택됩니다. 하나의 VM에 여러 네트워크 인터페이스(NIC)가 있을 수 있습니다. 각 NIC에는 고유한 유효 보안 규칙이 있을 수 있습니다. 문제를 해결할 때는 각 NIC의 유효 보안 규칙을 확인해야 할 수 있습니다.
   * **관련 NSG:** NSG를 NIC 및 NIC가 연결된 서브넷에 적용할 수 있습니다. 그림에서 NSG는 NIC 및 NIC가 연결된 서브넷 둘 다에 적용되었습니다. NSG 이름을 클릭하여 NSG의 규칙을 직접 수정할 수 있습니다.
   * **VM1-nsg 탭:** 그림에 표시되는 규칙 목록은 NIC에 적용된 NSG에 대한 것입니다. NSG가 만들어질 때마다 Azure에서 몇 가지 기본 규칙이 생성됩니다. 기본 규칙을 제거할 수 없으나 더 높은 우선 순위의 규칙으로 재정의할 수 있습니다. [기본 보안 규칙](security-overview.md#default-security-rules)에 대해 자세히 알아보세요.
   * **대상 열:** 일부 규칙에는 열에 텍스트가 있지만 주소 접두사가 있는 경우도 있습니다. 이 텍스트는 보안 규칙이 만들어질 때 적용된 기본 태그의 이름입니다. 태그는 여러 개의 접두사를 나타내는 시스템 제공 식별자입니다. *AllowInternetOutBound*와 같은 태그를 가진 규칙을 선택하면 **주소 접두사** 블레이드에 접두사가 나열됩니다.
   * **다운로드:** 규칙의 목록이 길 수 있습니다. **다운로드** 를 클릭하고 파일을 저장하여 오프라인 분석을 위해 규칙의 .csv 파일을 다운로드할 수 있습니다.
   * **AllowRDP** 인바운드 규칙: 이 규칙은 VM에 대한 RDP 연결을 허용합니다.
7. 다음 그림과 같이 **Subnet1-NSG** 탭을 클릭하여 서브넷에 적용된 NSG의 유효 규칙을 확인합니다. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    *denyRDP* **인바운드** 규칙을 확인합니다. 서브넷에 적용된 인바운드 규칙은 네트워크 인터페이스에 적용된 규칙보다 먼저 평가됩니다. 거부 규칙이 서브넷에 적용되므로 NIC의 허용 규칙이 평가되지 않으므로 TCP 3389에 대한 연결 요청이 실패합니다. 
   
    *denyRDP* 규칙은 RDP 연결이 실패하는 원인이 됩니다. 이 규칙을 제거해야 문제가 해결됩니다.
   
   > [!NOTE]
   > NIC와 연결된 VM이 실행 중 상태가 아니거나 NSG가 NIC 또는 서브넷에 적용되지 않은 경우 규칙이 표시되지 않습니다.
   > 
   > 
8. NSG 규칙을 편집하려면 *관련 NSG* 섹션에서 **Subnet1-NSG** 를 클릭합니다.
   이렇게 하면 **Subnet1-NSG** 블레이드가 열립니다. **인바운드 보안 규칙**을 클릭하여 규칙을 직접 편집할 수 있습니다.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. **Subnet1-NSG**에서 *denyRDP* 인바운드 규칙을 제거하고 *allowRDP* 규칙을 추가하면 다음 그림과 같이 유효 규칙이 표시됩니다.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    VM에 대한 RDP 연결을 열거나 PsPing 도구를 사용하여 TCP 포트 3389가 열려 있는지 확인합니다. [PsPing 다운로드 페이지](https://technet.microsoft.com/sysinternals/psping.aspx)를 읽어 PsPing에 대해 자세히 알아볼 수 있습니다.

### <a name="nic"></a>네트워크 인터페이스에 대한 유효 보안 규칙 보기
VM 트래픽 흐름이 특정 NIC에 대해 영향을 받으면 다음 단계를 완료하여 네트워크 인터페이스 컨텍스트에서 NIC에 대한 유효 규칙의 전체 목록을 볼 수 있습니다.

1. Azure Portal ( https://portal.azure.com ) 에 로그인합니다.
2. **모든 서비스**를 클릭한 다음, 표시되는 목록에서 **네트워크 인터페이스**를 클릭합니다.
3. 네트워크 인터페이스를 선택합니다. 다음 그림에서는 *VM1-NIC1* 이라는 NIC가 선택됩니다.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    **범위** 가 선택한 네트워크 인터페이스로 설정되어 있는지 확인합니다. 표시되는 추가 정보에 대한 자세한 내용은 이 문서의 **VM에 대한 NSG 문제 해결** 섹션, 6단계를 읽어보세요.
   
   > [!NOTE]
   > 네트워크 인터페이스에서 NSG가 제거되어도 서브넷 NSG는 지정된 NIC에서 계속 유효합니다. 이 경우 출력에 서브넷 NSG의 규칙만 표시됩니다. 규칙은 NIC가 VM에 연결된 경우에만 표시됩니다.
   > 
   > 
4. NIC 및 서브넷에 연결된 NSG에 대한 규칙만 직접 편집할 수 있습니다. 방법을 알아보려면 이 문서의 **가상 머신에 대한 유효 보안 규칙 보기** 섹션, 8단계를 읽어보세요.

## <a name="nsg"></a>NSG(네트워크 보안 그룹)에 대한 유효 보안 규칙 보기
NSG 규칙을 수정할 경우 추가되는 규칙이 특정 VM에 미치는 영향을 검토하려고 할 수 있습니다. 지정된 NSG 블레이드에서 컨텍스트를 전환하지 않고도, 지정된 NSG가 적용되는 모든 NIC에 대한 유효 보안 규칙의 전체 목록을 볼 수 있습니다. NSG 내의 유효 규칙 문제를 해결하려면 다음 단계를 완료합니다.

1. Azure Portal ( https://portal.azure.com ) 에 로그인합니다.
2. **모든 서비스**를 클릭한 다음, 표시되는 목록에서 **네트워크 보안 그룹**을 클릭합니다.
3. NSG를 선택합니다. 다음 그림에서는 VM1-nsg라는 NSG가 선택되었습니다.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    이전 그림의 다음 섹션을 확인합니다.
   
   * **범위:** 선택한 NSG로 설정합니다.
   * **가상 머신:** NSG는 서브넷에 적용될 때 서브넷에 연결된 모든 VM에 연결된 모든 네트워크 인터페이스에 적용됩니다. 이 목록은 이 NSG가 적용되는 모든 VM을 보여 줍니다. 목록에서 어떤 VM도 선택할 수 있습니다.
     
     > [!NOTE]
     > NSG가 빈 서브넷에만 적용되면 VM이 나열되지 않습니다. NSG가 VM과 연결되지 않은 NIC에 적용되면 해당 NIC도 나열되지 않습니다. 
     > 
     > 
   * **네트워크 인터페이스:** 하나의 VM에 여러 네트워크 인터페이스가 있을 수 있습니다. 선택한 VM에 연결된 네트워크 인터페이스를 선택할 수 있습니다.
   * **AssociatedNSGs:** 언제든지 하나의 NIC에 최대 2개의 유효 NSG가 있을 수 있습니다. 하나는 NIC에 적용되고 다른 하나는 서브넷에 적용됩니다. 범위가 VM1-nsg로 선택되더라도 NIC에 유효 서브넷 NSG가 있으면 출력에는 두 NSG가 모두 표시됩니다.
4. NIC 또는 서브넷에 연결된 NSG에 대한 규칙만 직접 편집할 수 있습니다. 방법을 알아보려면 이 문서의 **가상 머신에 대한 유효 보안 규칙 보기** 섹션, 8단계를 읽어보세요.

표시되는 추가 정보에 대해 자세히 알아보려면 이 문서의 **가상 머신에 대한 유효 보안 규칙 보기** 섹션, 6단계를 읽어보세요.

> [!NOTE]
> 서브넷과 NIC에는 각각 하나의 NSG만 적용될 수 있지만 하나의 NSG가 여러 NIC 및 여러 서브넷에 연결될 수 있습니다.
> 
> 

## <a name="considerations"></a>고려 사항
연결 문제를 해결하는 경우 다음 사항을 고려합니다.

* 기본 NSG 규칙은 인터넷의 인바운드 액세스를 차단하고 VNet 인바운드 트래픽만 허용합니다. 필요에 따라 인터넷의 인바운드 액세스를 허용하는 규칙을 명시적으로 추가해야 합니다.
* VM의 네트워크 연결이 실패하도록 하는 NSG 보안 규칙이 없는 경우 다음이 문제의 원인일 수 있습니다.
  * VM의 운영 체제 내에서 방화벽 소프트웨어가 실행되고 있습니다.
  * 가상 어플라이언스 또는 온-프레미스 트래픽에 대해 경로가 구성되었습니다. 강제 터널링을 통해 인터넷 트래픽이 온-프레미스로 리디렉션될 수 있습니다. 온-프레미스 네트워크 하드웨어가 이 트래픽을 처리하는 방법에 따라 인터넷에서 VM으로의 RDP/SSH 연결이 이 설정에서 작동하지 않을 수 있습니다. VM에서 들어오고 나가는 트래픽 흐름을 방해할 수 있는 경로 문제를 진단하는 방법을 자세히 알아보려면 [경로 문제 해결](virtual-network-routes-troubleshoot-powershell.md) 문서를 읽어보세요. 
* VNet을 피어링한 경우 기본적으로, VIRTUAL_NETWORK 태그는 피러링된 VNet에 대한 접두사를 포함하도록 자동으로 확장됩니다. VNet 피어링 연결과 관련된 문제를 해결하기 위해 **ExpandedAddressPrefix** 목록에서 이러한 접두사를 볼 수 있습니다. 
* 유효 보안 규칙은 VM의 NIC 및/또는 서브넷에 연결된 NSG가 있을 때만 표시됩니다. 
* NIC 또는 서브넷과 연결된 NSG가 없고 VM에 할당된 공용 IP 주소가 있는 경우 모든 포트가 인바운드 및 아웃바운드 액세스를 위해 열립니다. VM에 공용 IP 주소가 있을 때는 NIC 또는 서브넷에 NSG를 적용하는 것이 강력하게 권장됩니다.


---
title: "가상 컴퓨터에 대한 가속 네트워킹 - Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure 가상 컴퓨터에 대한 가속 네트워킹을 구성하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: a2e8e0dc40a63c363f295149e35f9823c8e90fa8
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Azure Portal을 사용하는 가상 컴퓨터에 대한 가속 네트워킹
> [!div class="op_single_selector"]
> * [Azure 포털](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

가속 네트워킹에서는 VM(가상 컴퓨터)에 대한 단일 루트 I/O 가상화(SR-IOV)가 가능해지므로 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회하여 대기 시간, 지터 및 지원되는 VM 형식의 가장 까다로운 네트워크 작업에 사용할 CPU 사용률을 줄여줍니다. 이 문서에서는 Azure Portal을 사용하여 Azure Resource Manager 배포 모델에서 가속 네트워킹을 구성하는 방법을 설명합니다. 또한 Azure PowerShell에서 가속 네트워킹을 사용하여 VM을 만들 수 있습니다. 방법을 보려면 이 문서 맨 위에 있는 PowerShell 상자를 클릭합니다.

다음 그림은 가속 네트워킹을 사용할 때와 사용하지 않을 때 두 개의 VM(가상 컴퓨터) 간 통신을 보여 줍니다.

![비교](./media/virtual-network-accelerated-networking-portal/image1.png)

가속 네트워킹을 사용하지 않으면 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 트래버스해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다. 자세한 내용은 [Hyper-V 네트워크 가상화 및 가상 스위치](https://technet.microsoft.com/library/jj945275.aspx) 문서를 참조하세요.

가속 네트워킹을 사용할 경우 네트워크 트래픽이 네트워크 카드(NIC)에 도착하고 VM에 전달됩니다. 가상 스위치가 가속 네트워킹 없이 적용하는 모든 네트워크 정책은 오프로드되고 하드웨어에서 적용됩니다. 하드웨어에서 정책을 적용하면 NIC는 호스트에 적용된 모든 정책을 유지하면서 VM에 직접 네트워크 트래픽을 전달할 수 있으므로 호스트 및 가상 스위치를 우회할 수 있습니다.

가속 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 VNet에 연결된 두 개 이상의 VM에서이 기능을 사용하도록 설정하는 것이 좋습니다. VNet 간에 통신하거나 온-프레미스에서 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향이 최소로 유지됩니다.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>이점
* **더 낮은 대기 시간/더 높은 초당 패킷 수(pps):** 데이터 경로에서 가상 스위치를 제거하면 패킷이 정채 처리를 위해 호스트에서 소요하는 시간이 제거되고 VM 내에서 처리될 수 있는 패킷 수가 늘어납니다.
* **감소된 지터:** 가상 스위치 처리는 적용해야 하는 정책의 양과 처리를 수행하는 CPU의 워크로드에 따라 달라집니다. 정책 적용을 하드웨어로 오프로드하면 패킷이 VM으로 직접 전달되고, 호스트-VM 통신과 모든 소프트웨어 인터럽트 및 컨텍스트 전환이 제거되어 이러한 가변성이 해소됩니다.
* **CPU 사용률 감소:** 호스트의 가상 스위치를 무시하면 네트워크 트래픽 처리에 사용되는 CPU가 감소됩니다.

## <a name="limitations"></a>제한 사항
이 기능을 사용하는 경우 다음과 같은 제한이 적용됩니다.

* **네트워크 인터페이스 만들기:** 가속 네트워킹은 새 네트워크 인터페이스에서만 사용할 수 있습니다.  기존 네트워크 인터페이스에서는 사용할 수 없습니다.
* **VM 만들기:** VM을 만들 때 가속 네트워킹이 설정된 네트워크 인터페이스만 VM에 연결할 수 있습니다. 이 네트워크 인터페이스를 기존 VM에 연결할 수 없습니다.
* **지역:** 미국 중서부 및 유럽 서부 Azure 지역에만 제공됩니다. 이 지역 집합은 나중에 확장될 것입니다.
* **지원되는 운영 체제:** Microsoft Windows Server 2012 R2 및 Windows Server 2016 Technical Preview 5. Linux 및 Windows Server 2012 지원은 곧 추가될 예정입니다.
* **VM 크기:** Standard_D15_v2 및 Standard_DS15_v2만 VM 인스턴스 크기로 지원됩니다. 자세한 내용은 [Windows VM 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 문서를 참조하세요. 지원되는 VM 인스턴스 크기 집합은 앞으로 확장될 예정입니다.

이러한 제한 사항이 변경되면 [Azure 가상 네트워킹 업데이트](https://azure.microsoft.com/updates/accelerated-networking-in-preview) 페이지에 공지됩니다.

## <a name="create-a-windows-vm-with-accelerated-networking"></a>가속 네트워킹을 사용하여 Windows VM 만들기
1. 미리 보기를 등록하려면 구독 ID 및 사용 목적을 적은 전자 메일을 [가속 네트워킹 구독](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 으로 보냅니다. 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지 나머지 단계를 완료하지 마세요.
2. Azure Portal http://portal.azure.com 에 로그인합니다.
3. 다음 옵션을 선택해 [Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 문서에 나오는 단계를 완료하여 VM을 만듭니다.
   
   * 이 문서의 제한 사항 섹션에 실린 운영 체제를 선택합니다.
   * 이 문서의 제한 사항 섹션에 실린 위치(영역)을 선택합니다.
   * 이 문서의 제한 사항 섹션에 실린 VM 크기를 선택합니다. 지원되는 크기 중 하나가 나와 있지 않으면 확장된 목록에서 크기를 선택하기 위해 **크기 선택** 블레이드에서 **모두 보기**를 클릭합니다.
   * **설정** 블레이드에서 다음 그림에 표시된 대로 **가속 네트워킹**에 대한 *Enabled*를 클릭합니다.
     
       ![설정](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > 가속 네트워킹 옵션은 다음 경우에만 볼 수 있습니다.
     > 
     > * 미리 보기에 적용되었음
     > * 이 문서의 제한 사항 섹션에서 언급된 지원되는 운영 체제, 위치 및 VM 크기.
     > 
     > 
4. VM을 만든 후 [가속 네트워킹 드라이버](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)를 다운로드하고, VM에 연결 및 로그인하고, VM 내 드라이버 설치 관리자를 실행합니다.
5. Windows 단추를 마우스 오른쪽 단추로 클릭하고 **장치 관리자**를 클릭합니다. 다음 그림과 같이 **네트워크** 옵션을 확장할 때 아래에 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**가 표시되는지 확인합니다.
   
    ![장치 관리자](./media/virtual-network-accelerated-networking-portal/image2.png)



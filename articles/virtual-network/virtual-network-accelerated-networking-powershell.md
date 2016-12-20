---
title: "가상 컴퓨터에 대한 가속 네트워킹 - PowerShell | Microsoft Docs"
description: "PowerShell을 사용하여 Azure 가상 컴퓨터에 대한 가속 네트워킹을 구성하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a0b63599-c23b-40b5-a8ab-23af8b07dded
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 8ae59a99a8397676160c032b744460c0b2bcc1d7


---
# <a name="accelerated-networking-for-a-virtual-machine"></a>가상 컴퓨터에 대한 가속 네트워킹
> [!div class="op_single_selector"]
> * [Azure 앱 서비스에서 웹앱](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

가속 네트워킹에서는 VM(가상 컴퓨터)에 대한 단일 루트 I/O 가상화(SR-IOV)가 가능해지므로 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회하여 대기 시간, 지터 및 지원되는 VM 형식의 가장 까다로운 네트워크 작업에 사용할 CPU 사용률을 줄여줍니다. 이 문서에서는 Azure PowerShell을 사용하여 Azure Resource Manager 배포 모델에서 가속 네트워킹을 구성하는 방법을 설명합니다. 또한 Azure Portal에서 가속 네트워킹을 사용하여 VM을 만들 수 있습니다. 방법을 보려면 이 문서 맨 위에 있는 Azure Portal 상자를 클릭합니다.

다음 그림은 가속 네트워킹을 사용할 때와 사용하지 않을 때 두 개의 VM(가상 컴퓨터) 간 통신을 보여 줍니다.

![비교](./media/virtual-network-accelerated-networking-powershell/image1.png)

가속 네트워킹을 사용하지 않으면 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 트래버스해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다. 자세한 내용은 [Hyper-V 네트워크 가상화 및 가상 스위치](https://technet.microsoft.com/library/jj945275.aspx) 문서를 참조하세요.

가속 네트워킹을 사용할 경우 네트워크 트래픽이 네트워크 카드(NIC)에 도착하고 VM에 전달됩니다. 가상 스위치가 가속 네트워킹 없이 적용하는 모든 네트워크 정책은 오프로드되고 하드웨어에서 적용됩니다. 하드웨어에서 정책을 적용하면 NIC는 호스트에 적용된 모든 정책을 유지하면서 VM에 직접 네트워크 트래픽을 전달할 수 있으므로 호스트 및 가상 스위치를 우회할 수 있습니다.

가속 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 VNet에 연결된 두 개 이상의 VM에서이 기능을 사용하도록 설정하는 것이 좋습니다.  VNet 간에 통신하거나 온-프레미스에서 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향이 최소로 유지됩니다.

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
1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서의 단계를 완료합니다.
2. 미리 보기를 등록하려면 구독 ID 및 사용 목적을 적은 전자 메일을 [가속 네트워킹 구독](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 으로 보냅니다. 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지 나머지 단계를 완료하지 마세요.
3. 다음 명령을 입력하여 구독에 해당 기능을 등록합니다.
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. *westcentralus* 를 이 문서의 [제한 사항](#limitations) 섹션에 나열된 이 기능에서 지원하는 다른 위치의 이름으로 바꿉니다(원할 경우). 다음 명령을 입력하여 위치에 대한 변수를 설정합니다.
   
        $locName = "westcentralus"
5. *RG1* 을 새 네트워크 인터페이스를 포함할 리소스 그룹의 이름으로 바꾸고 다음 명령을 입력하여 만듭니다.
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. *VM1-NIC1* 을 원하는 네트워크 인터페이스 이름으로 변경한 후 다음 명령을 입력합니다.
   
        $NICName = "VM1-NIC1"
7. 네트워크 인터페이스는 네트워크 인터페이스와 동일한 위치 및 [구독](../azure-glossary-cloud-terminology.md#subscription) 에 있는 기존 Azure 가상 네트워크(VNet) 내의 서브넷에 연결되어야 합니다. 이 내용이 친숙하지 않은 경우 [가상 네트워크 개요](virtual-networks-overview.md) 문서를 읽어 VNet에 대해 자세히 알아봅니다. VNet을 만들려면 [VNet 만들기](virtual-networks-create-vnet-arm-ps.md) 문서의 단계를 완료합니다. 다음 $Variables의 "값"을 네트워크 인터페이스를 연결하려는 VNet의 이름과 서브넷으로 변경합니다.
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    3단계에서 선택한 위치에 있는 기존 VNet의 이름을 모르는 경우 다음 명령을 입력합니다.
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    반환된 목록이 비어 있으면 해당 위치에 VNet을 만들어야 합니다. VNet을 만들려면 [가상 네트워크 만들기](virtual-networks-create-vnet-arm-ps.md) 문서의 단계를 완료합니다.
   
    VNet 내의 서브넷 이름을 가져오려면 다음 명령을 입력하고 위의 *Subnet1* 을 서브넷의 이름으로 바꿉니다.
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. 다음 명령을 입력하여 VNet 및 서브넷을 검색하고 변수에 할당합니다.
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. 인터넷을 통해 연결할 수 있도록 네트워크 인터페이스에 연결될 수 있는 기존 공용 IP 주소 리소스를 식별합니다. 네트워크 인터페이스를 사용하여 인터넷을 통해 VM에 액세스하지 않으려는 경우 이 단계를 건너뛸 수 있습니다. 공용 IP 주소가 없으면 동일한 VNet에 연결된 다른 VM에서 해당 VM으로 연결해야 합니다. 
   
    *PIP1* 을 네트워크 인터페이스를 만드는 위치에 있으며 현재 다른 네트워크 인터페이스와 연결되지 않은 기존 공용 IP 주소 리소스의 이름으로 변경합니다. 필요한 경우 $rgName을 공용 IP 주소 리소스가 있는 리소스 그룹의 이름으로 변경하고 다음 명령을 입력합니다.
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    기존 공용 IP 주소 리소스의 이름을 모르는 경우 다음 명령을 입력합니다.
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    반환된 출력에서 **IPConfiguration** 열에 값이 없는 경우 공용 IP 주소 리소스가 기존 네트워크 인터페이스와 연결되지 않은 것이므로 사용할 수 있습니다. 이 목록이 비어 있거나 사용 가능한 공용 IP 주소 리소스가 없는 경우 New-AzureRmPublicIPAddress 명령을 사용하여 만들 수 있습니다.
   
   > [!NOTE]
   > 공용 IP 주소에는 명목 요금이 부과됩니다. [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지에서 가격 채정에 대해 자세히 알아봅니다.
   > 
   > 
10. 인터페이스에 공용 IP 주소 리소스를 추가하지 않도록 선택한 경우 다음에 나오는 명령 끝에서 *-PublicIPAddress $PIP1* 을 제거합니다. 다음 명령을 입력하여 가속 네트워킹으로 네트워크 인터페이스를 만듭니다.
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. [VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 문서의 3~6단계에 나오는 지침에 따라 VM을 만들 때 VM에 네트워크 인터페이스를 할당합니다. 6-2단계에서 *Standard_A1*을 이 문서의 [제한 사항](#limitations) 섹션에 나열된 VM 크기 중 하나로 바꿉니다.
    
    > [!NOTE]
    > 이 문서에서 $locName, $rgName 또는 $nic 변수의 *이름* 을 변경한 경우 VM 만들기 문서의 6단계가 실패합니다. 그러나 변수의 *값* 은 변경할 수 있습니다.
    > 
    > 
12. VM을 만든 후 [가속 네트워킹 드라이버](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)를 다운로드하고, VM에 연결하고, VM 내 드라이버 설치 관리자를 실행합니다.
13. Windows 단추를 마우스 오른쪽 단추로 클릭하고 **장치 관리자**를 클릭합니다. 다음 그림과 같이 **네트워크** 옵션을 확장할 때 아래에 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**가 표시되는지 확인합니다.
    
    ![장치 관리자](./media/virtual-network-accelerated-networking-powershell/image2.png)




<!--HONumber=Nov16_HO3-->



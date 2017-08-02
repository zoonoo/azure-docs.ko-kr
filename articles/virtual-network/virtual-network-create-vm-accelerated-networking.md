---
title: "가속화된 네트워킹을 사용하는 Azure 가상 컴퓨터 만들기 | Microsoft Docs"
description: "가속화된 네트워킹을 사용하는 가상 컴퓨터를 만드는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c852a1297261504015a3a985fe14a38957d1a64a
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>가속화된 네트워킹을 사용하는 가상 컴퓨터 만들기

이 자습서에서는 가속화된 네트워킹을 사용하는 Azure VM(가상 컴퓨터)을 만드는 방법에 대해 알아봅니다. 가속화된 네트워킹은 Windows용 GA이고 특정 Linux 배포를 위한 공개 미리 보기로 제공됩니다. 가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다. 다음 그림에서는 가속화된 네트워킹을 사용하거나 사용하지 않는 경우의 두 VM(가상 컴퓨터) 간 통신을 보여 줍니다.

![비교](./media/virtual-network-create-vm-accelerated-networking/image1.png)

가속화된 네트워킹을 사용하지 않는 경우 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 통과해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다. 가상 스위치에 대한 자세한 내용은 [Hyper-V 네트워크 가상화 및 가상 스위치](https://technet.microsoft.com/library/jj945275.aspx) 문서를 참조하세요.

가속화된 네트워킹을 사용하는 경우 네트워크 트래픽이 VM의 NIC(네트워크 인터페이스)에 도착한 다음 VM으로 전달됩니다. 가상 스위치가 가속화된 네트워킹 없이 적용되는 모든 네트워크 정책은 오프로드되어 하드웨어에 적용됩니다. 하드웨어에서 정책을 적용하면 NIC는 호스트에 적용된 모든 정책을 유지하면서 VM에 직접 네트워크 트래픽을 전달할 수 있으므로 호스트 및 가상 스위치를 우회할 수 있습니다.

가속화된 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 Azure VNet(가상 네트워크)에 연결된 둘 이상의 VM에서 이 기능을 사용하도록 설정하는 것이 좋습니다. VNet에서 통신하거나 온-프레미스에 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향을 최소화할 수 있습니다.

> [!WARNING]
> 이 Linux Public Preview는 현재 미리 보기로 있으며 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 이 기능은 지원되지 않으며, 기능이 제한될 수 있으며 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 이 기능의 가용성 및 상태에 대한 최신 알림을 보려면 Azure Virtual Network 업데이트 페이지를 참조하세요.

## <a name="benefits"></a>이점
* **더 낮은 대기 시간/더 높은 초당 패킷 수(pps):** 데이터 경로에서 가상 스위치를 제거하면 패킷이 정채 처리를 위해 호스트에서 소요하는 시간이 제거되고 VM 내에서 처리될 수 있는 패킷 수가 늘어납니다.
* **감소된 지터:** 가상 스위치 처리는 적용해야 하는 정책의 양과 처리를 수행하는 CPU의 워크로드에 따라 달라집니다. 정책 적용을 하드웨어로 오프로드하면 패킷이 VM으로 직접 전달되고, 호스트-VM 통신과 모든 소프트웨어 인터럽트 및 컨텍스트 전환이 제거되어 이러한 가변성이 해소됩니다.
* **CPU 사용률 감소:** 호스트의 가상 스위치를 무시하면 네트워크 트래픽 처리에 사용되는 CPU가 감소됩니다.

## <a name="Limitations"></a>제한 사항
이 기능을 사용하는 경우 다음과 같은 제한이 적용됩니다.

* **네트워크 인터페이스 만들기:** 가속화된 네트워킹은 새 NIC에서만 사용할 수 있으며, 기존 NIC에서는 사용할 수 없습니다.
* **VM 만들기:** VM을 만들 때 가속화된 네트워킹을 사용하도록 설정된 NIC만 VM에 연결할 수 있습니다. 기존 VM에는 이 NIC를 연결할 수 없습니다.
* **지역:** 가속화된 네트워킹 기능을 갖춘 Windows VM은 대부분의 Azure 지역에서 제공됩니다. 가속화된 네트워킹 기능을 갖춘 Linux VM은 여러 지역에서 제공됩니다. 이 기능을 사용할 수 있는 지역은 확장되고 있습니다. 최신 정보는 Azure 가상 네트워킹 업데이트 블로그를 참조하세요.   
* **지원되는 운영 체제:** Windows: Microsoft Windows Server 2012 R2 Datacenter 및 Windows Server 2016. Linux: Ubuntu Server 16.04 LTS(커널 4.4.0-77 이상), SLES 12 SP2, RHEL 7.3 및 CentOS 7.3(“Rogue Wave Software”에 의해 게시됨).
* **VM 크기:** 8개 이상의 코어가 있는 범용 및 계산 용도로 최적화된 인스턴스 크기입니다. 자세한 내용은 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 참조하세요. 지원되는 VM 인스턴스 크기 집합은 앞으로 확장될 예정입니다.
* **ARM(Azure Resource Manager)을 통한 배포:** 가속화된 네트워킹은 ASM/RDFE를 통해 배포할 수 없습니다.

이러한 제한 사항이 변경되면 [Azure 가상 네트워킹 업데이트](https://azure.microsoft.com/updates/accelerated-networking-in-preview) 페이지를 통해 발표됩니다.

## <a name="create-a-windows-vm"></a>Windows VM 만들기
Azure Portal 또는 Azure [PowerShell](#windows-powershell)을 사용하여 VM을 만들 수 있습니다.

### <a name="windows-portal"></a>포털

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. 포털에서 시작하여 **+ 새로 만들기** > **계산** > **Windows Server 2016 Datacenter**를 차례로 클릭합니다. 
3. 표시되는 **Windows Server 2016 Datacenter** 블레이드의 **배포 모델 선택** 아래에서 *Resource Manager*를 선택한 채로 두고 **만들기**를 클릭합니다.
4. 표시되는 **기본 사항** 블레이드에서 다음 값을 입력하고, 나머지 기본 옵션을 그대로 두거나 사용자 고유의 값을 선택하거나 입력한 다음, **확인** 단추를 클릭합니다.

    |설정|값|
    |---|---|
    |이름|MyVm|
    |리소스 그룹|**새로 만들기**를 선택한 채로 두고 *MyResourceGroup*을 입력합니다.|
    |위치|미국 서부 2|

    Azure를 처음 사용하는 경우 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 [위치](https://azure.microsoft.com/regions)(지역이라고도 함)에 대해 자세히 알아봅니다.
5. 표시되는 **크기 선택** 블레이드의 **최소 코어 수** 상자에서 *8*을 입력한 다음 **모두 보기**를 클릭합니다.
6. **DS4_V2 표준** 또는 지원되는 VM을 클릭한 다음 **선택** 단추를 클릭합니다.
7. 표시되는 **설정** 블레이드에서 모든 설정은 그대로 두는 한편, **가속화된 네트워킹** 아래에서 **사용**을 클릭한 다음 **확인** 단추를 클릭합니다. **참고:** 이 문서의 [제한 사항](#Limitations) 섹션에서 설명하지 않은 VM 크기, 운영 체제 또는 위치에 대한 값을 이전 단계에서 선택한 경우 **가속화된 네트워킹**은 표시되지 않습니다.
8. 표시되는 **요약** 블레이드에서 **확인** 단추를 클릭합니다. Azure VM을 만들기 시작합니다. VM을 만드는 데 몇 분이 걸립니다.
9. Windows용 가속화된 네트워킹 드라이버를 설치하려면 이 문서의 [Windows 구성](#configure-windows)섹션에서 설명하는 단계를 완료합니다.

### <a name="windows-powershell"></a>PowerShell
1. 최신 버전의 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.
2. [Windows 시작] 단추를 클릭하고 **powershell**을 입력한 다음 검색 결과에서 **PowerShell**을 클릭하여 PowerShell 세션을 시작합니다.
3. PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
4. 브라우저에서 다음 스크립트를 복사합니다.
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. PowerShell 창에서 마우스 오른쪽 단추를 클릭하여 스크립트를 붙여넣고 실행을 시작합니다. 사용자 이름과 암호를 묻는 메시지가 나타납니다. 다음 단계에서 VM에 연결할 때 이 자격 증명을 사용하여 해당 VM에 로그인합니다. 스크립트가 실패하고 스크립트를 실행하기 전에 스크립트의 값을 변경한 경우 VM 크기, 운영 체제 및 위치에 사용한 값이 이 문서의 [제한 사항](#Limitations) 섹션에서 설명하는 값인지 확인합니다.
6. Windows용 가속화된 네트워킹 드라이버를 설치하려면 이 문서의 [Windows 구성](#configure-windows)섹션에서 설명하는 단계를 완료합니다.

### <a name="configure-windows"></a>Windows 구성
Azure에서 VM을 만든 후에는 Windows용 가속화된 네트워킹 드라이버를 설치해야 합니다. 다음 단계를 수행하기 전에 이 문서의 [포털](#windows-portal) 또는 [PowerShell](#windows-powershell) 단계를 사용하여 가속화된 네트워킹을 사용하는 Windows VM을 만들어야 합니다. 

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *MyVm*을 입력합니다. 검색 결과에서 표시되는 **MyVm**을 클릭합니다.
3. 표시되는 **MyVm** 블레이드에서 왼쪽 위 모서리에 있는 **연결** 단추를 클릭합니다. **참고:** **연결** 단추 아래에서 **만들기**가 표시되는 경우 Azure에서 VM 만들기를 아직 완료하지 않은 것입니다. 따라서 **연결** 단추 아래에서 **만들기**가 더 이상 표시되지 않는 경우에만 **연결**을 클릭합니다.
4. 브라우저에서 **MyVm.rdp** 파일을 다운로드하도록 허용합니다.  다운로드한 후에 해당 파일을 클릭하여 엽니다. 
5. 원격 연결의 게시자를 식별할 수 없다고 알리는 메시지와 함께 표시되는 **원격 데스크톱 연결** 상자에서 **연결** 단추를 클릭합니다.
6. 표시되는 **Windows 보안** 상자에서 **추가 선택 항목**을 클릭한 다음 **다른 계정 사용**을 클릭합니다. 4단계에서 입력한 사용자 이름과 암호를 입력한 다음 **확인** 단추를 클릭합니다.
7. 원격 컴퓨터의 ID를 확인할 수 없다고 알리는 메시지와 함께 표시되는 [원격 데스크톱 연결] 상자에서 **예** 단추를 클릭합니다.
8. [Windows 시작] 단추를 마우스 오른쪽 단추로 클릭한 다음 **장치 관리자**를 클릭합니다. **네트워크 어댑터** 노드를 펼칩니다. 다음 그림과 같이 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**가 나타나는지 확인합니다.
   
    ![장치 관리자](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. 이제 가속화된 네트워킹을 VM에 사용할 수 있습니다.

## <a name="create-a-linux-vm"></a>Linux VM 만들기
Azure Portal 또는 Azure [PowerShell](#linux-powershell)을 사용하여 Ubuntu 또는 SLES VM을 만들 수 있습니다. RHEL 및 CentOS VM의 경우 다른 워크플로가 있습니다.  아래의 지침을 참조하세요.

### <a name="linux-portal"></a>포털
1. 이 문서의 [Linux VM 만들기 - PowerShell](#linux-powershell) 섹션에 나오는 1-5단계를 수행하여 Linux 가속화된 네트워킹 미리 보기에 등록합니다.  포털에서는 미리 보기에 등록할 수 없습니다.
2. 이 문서의 [Windows VM 만들기 - 포털](#windows-portal) 섹션에 나오는 1-8단계를 수행합니다. 2단계에서 **Windows Server 2016 Datacenter** 대신 **Ubuntu Server 16.04 LTS**를 클릭합니다. 이 자습서에서는 SSH 키 대신 암호를 사용하도록 선택하지만, 프로덕션 배포에서는 둘 중 하나를 사용할 수 있습니다. 이 문서의 [Windows VM 만들기 - 포털](#windows-portal) 섹션의 7단계를 수행할 때 **가속화된 네트워킹**이 나타나지 않으면 다음과 같은 이유 중 하나로 인해 발생할 수 있습니다.
    - 아직 미리 보기에 등록되지 않았습니다. 이 문서의 [Linux VM 만들기 - Powershell](#linux-powershell) 섹션의 4단계에서 설명한 대로 등록 상태가 **등록됨**인지 확인합니다. **참고:** Windows VM용 가속화된 네트워킹 미리 보기에 참여한 경우 Windows VM용 가속화된 네트워킹을 사용하기 위해 더 이상 등록할 필요가 없지만, Linux VM용 가속화된 네트워킹 미리 보기에서는 자동으로 등록되지 않습니다. 따라서 참여할 Linux VM 가속화된 네트워킹 미리 보기에 등록해야 합니다.
    - 이 문서의 [제한 사항](#limitations) 섹션에서 설명하는 VM 크기, 운영 체제 또는 위치를 선택하지 않았습니다.
3. Linux용 가속화된 네트워킹 드라이버를 설치하려면 이 문서의 [Linux 구성](#configure-linux)섹션에서 설명하는 단계를 완료합니다.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>구독에서 가속화된 네트워킹을 사용하는 Linux VM을 만든 다음 동일한 구독에서 가속화된 네트워킹을 사용하는 Windows VM을 만들려고 하면 Windows VM 만들기가 실패할 수 있습니다. 이 미리 보기에서는 가속화된 네트워킹을 사용하는 Linux VM과 Windows VM을 별도의 구독에서 테스트하는 것이 좋습니다.
>

1. 최신 버전의 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.
2. [Windows 시작] 단추를 클릭하고 **powershell**을 입력한 다음 검색 결과에서 **PowerShell**을 클릭하여 PowerShell 세션을 시작합니다.
3. PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
4. 다음 단계를 완료하여 Azure 가속화된 네트워킹 미리 보기에 등록합니다.
    - Azure 구독 ID 및 의도된 용도가 포함된 전자 메일을 [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)에 보냅니다. Microsoft에서 보내는 구독 활성화에 대한 메일 확인을 기다려 주세요.
    - 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        이전 명령을 입력한 후 출력에 **등록됨**이 나타날 때까지 5단계로 진행하지 마세요. 계속 진행하기 전에 출력은 다음과 같아야 합니다.
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Windows VM용 가속화된 네트워킹 미리 보기에 참여한 경우 Windows VM용 가속화된 네트워킹을 사용하기 위해 더 이상 등록할 필요가 없지만, Linux VM용 가속화된 네트워킹 미리 보기에서는 자동으로 등록되지 않습니다. 따라서 참여할 Linux VM 가속화된 네트워킹 미리 보기에 등록해야 합니다.
      >
5. 브라우저에서 Ubuntu 또는 SLES를 원하는 대로 대체하는 다음 스크립트를 복사합니다.  또한 Redhat 및 CentOS에는 아래에서 간략하게 설명되는 다른 워크플로가 있습니다.

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. PowerShell 창에서 마우스 오른쪽 단추를 클릭하여 스크립트를 붙여넣고 실행을 시작합니다. 사용자 이름과 암호를 묻는 메시지가 나타납니다. 다음 단계에서 VM에 연결할 때 이 자격 증명을 사용하여 해당 VM에 로그인합니다. 스크립트가 실패하면 다음을 확인합니다.
    - 4단계에서 설명한 대로 미리 보기에 등록되어 있습니다.
    - 스크립트를 실행하기 전에 스크립트에서 VM 크기, 운영 체제 유형 및 위치의 값을 변경한 경우 해당 값이 이 문서의 [제한 사항](#Limitations) 섹션에서 설명하는 값인지 확인합니다.
7. Linux용 가속화된 네트워킹 드라이버를 설치하려면 이 문서의 [Linux 구성](#configure-linux)섹션에서 설명하는 단계를 완료합니다.

### <a name="configure-linux"></a>Linux 구성

Azure에서 VM을 만든 후에는 Linux용 가속화된 네트워킹 드라이버를 설치해야 합니다. 다음 단계를 수행하기 전에 이 문서의 [포털](#linux-portal) 또는 [PowerShell](#linux-powershell) 단계를 사용하여 가속화된 네트워킹을 사용하는 Linux VM을 만들어야 합니다. 

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. 포털 위쪽의 *리소스 검색* 상자 오른쪽에 있는 **>_** 아이콘을 클릭하여 Bash 클라우드 셸(미리 보기)을 시작합니다. Bash 클라우드 셸 창이 포털의 아래쪽에 나타나고, 몇 초 후에 **username@Azure:~$** 프롬프트가 표시됩니다. 컴퓨터에서 VM으로 클라우드 셸 대신 SSH를 수행할 수 있지만, 이 자습서의 지침에서는 클라우드 셸을 사용한다고 가정합니다.
3. *리소스 검색* 텍스트가 있는 포털 위쪽의 상자에서 *MyVm*을 입력합니다. 검색 결과에서 표시되는 **MyVm**을 클릭합니다.
4. 표시되는 **MyVm** 블레이드에서 왼쪽 위 모서리에 있는 **연결** 단추를 클릭합니다. **참고:** **연결** 단추 아래에서 **만들기**가 표시되는 경우 Azure에서 VM 만들기를 아직 완료하지 않은 것입니다. 따라서 **연결** 단추 아래에서 **만들기**가 더 이상 표시되지 않는 경우에만 **연결**을 클릭합니다.
5. Azure에서 `ssh adminuser@<ipaddress>`를 입력하라는 상자를 엽니다. 이 명령을 클라우드 셸에서 입력하거나 4단계에서 표시되는 상자에서 복사하여 클라우드 셸에 붙여넣은 다음 Enter 키를 누릅니다.
6. 연결을 계속할 것인지 묻는 질문에 **예**를 입력한 다음 Enter 키를 누릅니다.
7. VM을 만들 때 입력한 암호를 입력합니다. VM에 성공적으로 로그인하면 adminuser@MyVm:~$ 프롬프트가 표시됩니다. 이제 클라우드 셸 세션을 통해 VM에 로그인했습니다. **참고:** 클라우드 셸 세션을 10분 동안 사용하지 않으면 시간이 초과됩니다.

이때 지침은 사용 중인 배포에 따라 달라집니다. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. 프롬프트에 `uname -r`을 입력하고 버전을 확인합니다.

    * Ubuntu는 "4.4.0-77-generic" 이상임
    * SLES는 "4.4.59-92.20-default" 이상임

2. 다음 명령을 실행하여 표준 네트워킹 vNIC와 가속화된 네트워킹 vNIC 간에 본드를 만듭니다. 네트워크 트래픽은 고성능 가속화된 네트워킹 vNIC를 사용하는 반면, 본드는 특정 구성 변경에서 네트워킹 트래픽이 중단되지 않도록 합니다.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. 스크립트를 실행한 후 VM은 60초 일시 정지된 후 다시 시작됩니다.
4. VM이 다시 시작되면 5-7단계를 다시 수행하여 VM에 다시 연결합니다.
5. `ifconfig` 명령을 실행하고 bond0이 나타나고 인터페이스가 UP으로 표시되는지 확인합니다. 
 
 >[!NOTE]
      >가속화된 네트워킹을 사용하는 응용 프로그램은 *eth0*이 아닌 *bond0* 인터페이스를 통해 통신해야 합니다.  가속화된 네트워킹이 일반적으로 공급되기 전에 인터페이스 이름이 변경될 수 있습니다.

#### <a name="rhelcentos"></a>RHEL/CentOS

Red Hat Enterprise Linux 또는 CentOS 7.3 VM을 만들려면 SR-IOV에 필요한 드라이버와 네트워크 카드용 VF(Virtual Function) 드라이버를 로드하는 추가적인 몇 단계가 필요합니다. 지침의 첫 번째 단계에서는 드라이버가 미리 로드된 하나 이상의 가상 컴퓨터를 만드는 데 사용할 수 있는 이미지를 준비합니다.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>1단계: Red Hat Enterprise Linux 또는 CentOS 7.3 기본 이미지를 준비합니다. 

1.  Azure에서 비SRIOV CentOS 7.3 VM 프로비전

2.  LIS 4.2.2를 설치합니다.
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  구성 파일을 다운로드합니다.
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  이 VM의 프로비전 해제

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Azure Portal에서 이 VM을 중지하고 VM의 "디스크"로 이동하여 OSDisk의 VHD URI를 캡처합니다. 이 URI에는 기본 이미지의 VHD 이름 및 저장소 계정이 포함됩니다. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>2단계: Azure에서 새 VM 프로비전

1.  vNIC에서 AcceleratedNetworking을 사용하도록 설정하고 1단계에서 캡처된 기본 이미지 VHD를 사용하는 New-AzureRMVMConfig를 기반으로 새 VM을 프로비전합니다.

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  VM이 부팅된 후 "lspci"를 사용하여 VF 장치를 확인하고 Mellanox 항목을 확인합니다. 예를 들어 lspci 출력에 다음 항목이 표시되어야 합니다.
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  다음을 사용하여 본딩 스크립트를 실행합니다.

    ```bash
    sudo bondvf.sh
    ```

4.  새 VM을 다시 부팅합니다.

    ```bash
    sudo reboot
    ```

VM은 bond0이 구성되고 가속화된 네트워킹 경로가 사용하도록 설정된 상태로 시작되어야 합니다.  `ifconfig`를 실행하여 확인합니다.


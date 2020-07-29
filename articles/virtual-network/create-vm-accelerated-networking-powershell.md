---
title: 가속 네트워킹을 사용 하 여 Windows VM 만들기-Azure PowerShell
description: 가속화 된 네트워킹을 사용 하 여 Windows VM (가상 머신)을 만들면 네트워킹 성능을 크게 향상 시킬 수 있습니다.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: fd50af98fe0d7f20273c45e2b86c18215a3626f0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289621"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 가속화 된 네트워킹을 사용 하는 Windows VM 만들기

이 자습서에서는 가속화 된 네트워킹을 사용 하 여 Windows VM (가상 머신)을 만드는 방법에 대해 알아봅니다.

> [!NOTE]
> Linux 가상 머신에서 가속화 된 네트워킹을 사용 하려면 가속화 된 [네트워킹을 사용 하 여 LINUX VM 만들기](create-vm-accelerated-networking-cli.md)를 참조 하세요.

가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회 하 여 지원 되는 VM 유형에 서 가장 까다로운 네트워크 작업에 대 한 대기 시간, 지터 및 CPU 사용률을 줄입니다. 다음 다이어그램에서는 두 Vm이 가속화 된 네트워킹을 사용 하거나 사용 하지 않고 통신 하는 방법을 보여 줍니다.

![가속화 된 네트워킹을 사용 하거나 사용 하지 않는 Azure virtual machines 간 통신](./media/create-vm-accelerated-networking/accelerated-networking.png)

가속화된 네트워킹을 사용하지 않는 경우 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 통과해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다.

> [!NOTE]
> 가상 스위치에 대 한 자세한 참조 [Hyper-v 가상 스위치](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)합니다.

가속화 된 네트워킹에서는 네트워크 트래픽이 VM의 NIC (네트워크 인터페이스)에 도착 하 고 VM으로 전달 됩니다. 이제 가상 스위치가 적용되는 모든 네트워크 정책은 오프로드되어 하드웨어에 적용됩니다. 정책은 하드웨어에서 적용 되므로 NIC는 네트워크 트래픽을 VM에 직접 전달할 수 있습니다. NIC는 호스트와 가상 스위치를 무시 하 고 호스트에 적용 된 모든 정책을 유지 관리 합니다.

가속화 된 네트워킹의 이점은 사용 하도록 설정 된 VM에만 적용 됩니다. 최상의 결과를 위해 동일한 Azure 가상 네트워크에 연결 된 두 개 이상의 Vm에서이 기능을 사용 하도록 설정 합니다. 가상 네트워크에서 통신 하거나 온-프레미스에 연결 하는 경우이 기능은 전체 대기 시간에 미치는 영향을 최소화 합니다.

## <a name="benefits"></a>이점

- **낮은 대기 시간/초당 패킷 (초당 패킷 수)**: 데이터 경로에서 가상 스위치를 제거 하면 호스트에서 패킷을 소비 하는 시간을 정책 처리에 사용할 수 없습니다. 또한 VM 내에서 처리할 수 있는 패킷 수를 늘립니다.

- **감소 된 지터**: 가상 스위치 처리는 적용 해야 하는 정책의 양에 따라 달라 집니다. 또한 처리를 수행 하는 CPU의 워크 로드에 따라 달라 집니다. 정책 적용을 하드웨어로 오프 로드 하면 VM에 직접 패킷을 전달 하 여 이러한 가변성을 제거 합니다. 또한 오프 로딩을 통해 호스트 간 통신, 모든 소프트웨어 인터럽트 및 모든 컨텍스트 전환이 제거 됩니다.

- **Cpu 사용률 감소**: 호스트에서 가상 스위치를 건너뛰면 네트워크 트래픽을 처리 하기 위한 cpu 사용률을 줄일 수 있습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure 갤러리에서 직접 지원 되는 배포는 다음과 같습니다.

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

### <a name="supported-vm-instances"></a>지원되는 VM 인스턴스

가속화 된 네트워킹은 둘 이상의 가상 Cpu (vCPUs)를 사용 하는 대부분의 범용 및 계산에 최적화 된 인스턴스 크기에서 지원 됩니다.  지원 되는 시리즈는 Dv2/DSv2 및 F/Fs입니다.

하이퍼스레딩을 지 원하는 인스턴스에서는 4 개 이상의 vCPUs가 있는 VM 인스턴스에서 가속화 된 네트워킹을 지원 합니다. 지원 되는 시리즈는 D/Dsv3, D/Dsv4, Da/Dasv4, E/Esv3, Ea/Easv4, Fsv2, Lsv2, Ms/Mms 및 Ms/Mmsv2입니다.

VM 인스턴스에 대 한 자세한 내용은 [Azure에서 Windows 가상 머신에 대 한 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요.

### <a name="custom-images"></a>사용자 지정 이미지

사용자 지정 이미지를 사용 중이 고 이미지가 가속화 된 네트워킹을 지 원하는 경우 Azure에서 Mellanox Connectx-3-3 및 Connectx-3-4 Lx Nic와 작동 하는 필수 드라이버가 있는지를 알고 있어야 합니다.

### <a name="regions"></a>영역

가속화 된 네트워킹은 모든 글로벌 Azure 지역 및 Azure Government 클라우드에서 사용할 수 있습니다.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>실행 중인 VM에서 가속화 된 네트워킹 사용

가속화 된 네트워킹을 사용 하지 않는 지원 되는 VM 크기는 중지 및 할당 취소 된 경우에만 기능을 사용할 수 있습니다.

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager를 통한 배포

가속화 된 네트워킹을 사용 하 여 가상 머신 (클래식)을 배포할 수 없습니다.

## <a name="vm-creation-using-the-portal"></a>포털을 사용 하 여 VM 만들기

이 문서에서는 Azure PowerShell를 사용 하 여 가속화 된 네트워킹을 사용 하는 VM을 만드는 단계를 제공 하지만 Azure Portal를 사용 하 여 가속화 된 네트워킹을 사용 하는 [가상 머신을 만들](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수도 있습니다. 포털에서 VM을 만들 때 **가상 머신 만들기** 페이지에서 **네트워킹** 탭을 선택 합니다. 이 탭에는 가속화 된 **네트워킹**옵션이 있습니다. 지원 되는 [운영 체제](#supported-operating-systems) 및 [VM 크기](#supported-vm-instances)를 선택한 경우이 옵션은 자동으로 설정 됨 **으로 설정 됩니다.** 그렇지 않으면 옵션이 **Off**로 설정 되 고 Azure에서 사용할 수 없는 이유가 표시 됩니다.

> [!NOTE]
> 지원 되는 운영 체제만 포털을 통해 사용 하도록 설정할 수 있습니다. 사용자 지정 이미지를 사용 하 고 이미지가 가속화 된 네트워킹을 지 원하는 경우 CLI 또는 PowerShell을 사용 하 여 VM을 만듭니다. 

VM을 만든 후 가속화 된 네트워킹을 사용할 수 있는지 여부를 확인할 수 있습니다. 다음 지침을 따릅니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 vm을 관리 합니다. **가상 머신**을 검색하여 선택합니다.

2. 가상 컴퓨터 목록에서 새 VM을 선택 합니다.

3. VM 메뉴 모음에서 **네트워킹**을 선택 합니다.

네트워크 인터페이스 정보의 **가속화 된 네트워킹** 레이블 옆에는 가속화 된 네트워킹 상태에 대해 **사용 안 함** 또는 **사용으로 설정** 된 포털이 표시 됩니다.

## <a name="vm-creation-using-powershell"></a>PowerShell을 사용 하 여 VM 만들기

계속 하기 전에 1.0.0 이상의 [Azure PowerShell](/powershell/azure/install-az-ps) 버전을 설치 합니다. 현재 설치된 버전을 찾으려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 또는 업그레이드 해야 하는 경우 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az)에서 Az module의 최신 버전을 설치 합니다. PowerShell 세션에서 [AzAccount](/powershell/module/az.accounts/connect-azaccount)를 사용 하 여 Azure 계정에 로그인 합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *Myresourcegroup*, *MyNic*, *myresourcegroup*이 포함 됩니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 명령은 *centralus* 위치에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. [AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)를 사용 하 여 서브넷 구성을 만듭니다. 다음 명령은 *mysubnet*이라는 서브넷을 만듭니다.

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. *Mysubnet* 서브넷이 있는 [AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)를 사용 하 여 가상 네트워크를 만듭니다.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

1. [AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)를 사용 하 여 네트워크 보안 그룹 규칙을 만듭니다.

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
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

2. [AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) 를 사용 하 여 네트워크 보안 그룹을 만들고 해당 그룹에 *허용-rdp-tcp* 보안 규칙을 할당 합니다. *허용-모든* 규칙 외에 네트워크 보안 그룹에는 몇 가지 기본 규칙이 포함 되어 있습니다. 기본 규칙 하나는 인터넷의 모든 인바운드 액세스를 사용 하지 않도록 설정 합니다. 만든 후에는 VM에 원격으로 연결할 수 있도록 네트워크 보안 그룹에 *허용 RDP-모든* 규칙이 할당 됩니다.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. [AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)를 사용 하 여 *mysubnet* 서브넷에 네트워크 보안 그룹을 연결 합니다. 네트워크 보안 그룹의 규칙은 서브넷에 배포된 모든 리소스에 효율적입니다.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>가속 네트워킹을 사용하여 네트워크 인터페이스 만들기

1. [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress)를 사용하여 공용 IP 주소를 만듭니다. 인터넷에서 VM에 액세스 하지 않으려는 경우 공용 IP 주소가 필요 하지 않습니다. 그러나이 문서의 단계를 완료 해야 합니다.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. 가속화 된 네트워킹을 사용 하는 [AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) 를 사용 하 여 네트워크 인터페이스를 만들고, 공용 IP 주소를 네트워크 인터페이스에 할당 합니다. 다음 예제에서는 *Mysubnet* 가상 네트워크의 *Mysubnet* 서브넷에 *myNic* 이라는 네트워크 인터페이스를 만들고, *myPublicIp* 공용 IP 주소를 할당 합니다.

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>VM 만들기 및 네트워크 인터페이스 연결

1. 사용자에 `$cred` 게 로그인 하 라는 메시지를 표시 하는 [자격 증명](/powershell/module/microsoft.powershell.security/get-credential)을 사용 하 여 VM 자격 증명을 변수로 설정 합니다.

    ```azurepowershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)를 사용하여 VM을 정의합니다. 다음 명령은*Standard_DS4_v2*(가속화 된 네트워킹)를 지 원하는 vm 크기를 사용 하 여 *myvm* 이라는 vm을 정의 합니다.

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    모든 VM 크기 및 특성 목록은 [Windows VM 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

3. [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) 및 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)를 사용하여 나머지 VM 구성을 만듭니다. 다음 명령은 Windows Server 2016 VM을 만듭니다.

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. [AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)를 사용 하 여 이전에 만든 네트워크 인터페이스를 연결 합니다.

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. [New-azvm](/powershell/module/az.compute/new-azvm)를 사용 하 여 VM을 만듭니다.

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Windows VM에 이더넷 컨트롤러가 설치 되어 있는지 확인 합니다.

Azure에서 VM을 만든 후 VM에 연결 하 고 Windows에서 이더넷 컨트롤러가 설치 되어 있는지 확인 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 vm을 관리 합니다. **가상 머신**을 검색하여 선택합니다.

2. 가상 컴퓨터 목록에서 새 VM을 선택 합니다.

3. Vm 개요 페이지에서 VM의 **상태가** **만들기**로 표시 되는 경우 Azure에서 vm 만들기를 완료할 때까지 기다립니다. VM 생성이 완료 되 면 **상태가** **실행 중** 으로 변경 됩니다.

4. VM 개요 도구 모음에서 **연결**  >  **rdp**  >  **파일 다운로드 rdp 파일**을 선택 합니다.

5. .Rdp 파일을 열고 [Vm 만들기 및 네트워크 인터페이스 연결](#create-a-vm-and-attach-the-network-interface) 섹션에 입력 한 자격 증명을 사용 하 여 vm에 로그인 합니다. Azure에서 Windows VM에 연결된 적이 없는 경우 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)을 참조하세요.

6. VM에 대 한 원격 데스크톱 세션이 나타나면 Windows 시작 단추를 마우스 오른쪽 단추로 클릭 하 고 **Device Manager**를 선택 합니다.

7. **Device Manager** 창에서 **네트워크 어댑터** 노드를 확장 합니다.

8. 다음 이미지와 같이 **Mellanox connectx-3-3 가상 함수 이더넷 어댑터가** 나타나는지 확인 합니다.

    ![Mellanox Connectx-3-3 가상 함수 이더넷 어댑터, 가속화 된 네트워킹을 위한 새 네트워크 어댑터, Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

이제 VM에 가속화 된 네트워킹을 사용할 수 있습니다.

> [!NOTE]
> Mellanox 어댑터를 시작 하지 못하는 경우 원격 데스크톱 세션에서 관리자 프롬프트를 열고 다음 명령을 입력 합니다.
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>기존 Vm에서 가속 네트워킹 사용

가속화 된 네트워킹 없이 VM을 만든 경우 기존 VM에서이 기능을 사용 하도록 설정할 수 있습니다. VM은 위에서 설명한 다음 필수 구성 요소를 충족 하 여 가속화 된 네트워킹을 지원 해야 합니다.

* VM은 가속화 된 네트워킹에 대해 지원 되는 크기 여야 합니다.
* VM은 지원 되는 Azure 갤러리 이미지 (및 Linux 용 커널 버전) 여야 합니다.
* 모든 NIC에서 가속 네트워킹을 사용 하려면 먼저 가용성 집합 또는 가상 머신 확장 집합의 모든 Vm을 중지 하거나 할당 취소 해야 합니다.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>가용성 집합의 개별 Vm 및 Vm

1. VM을 중지 하거나 할당을 취소 하거나 가용성 집합이 있으면 집합의 모든 Vm:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 가용성 집합 없이 VM을 개별적으로 만들 때 가속화 된 네트워킹을 사용 하려면 개별 VM을 중지 하거나 할당 취소 해야 합니다. VM이 가용성 집합을 사용 하 여 만들어진 경우에는 모든 Nic에서 가속 네트워킹을 사용 하도록 설정 하기 전에 가용성 집합에 포함 된 모든 Vm을 중지 하거나 할당 취소 해야 합니다. 이렇게 하면 Vm이 가속화 된 네트워킹을 지 원하는 클러스터에서 종료 됩니다. 가속화 된 네트워킹을 지 원하는 클러스터가 가속화 된 네트워킹을 사용 하지 않는 Nic와도 잘 작동 하므로 가속화 된 네트워킹을 사용 하지 않도록 설정 하는 경우 중지 또는 할당 취소 요구 사항이 필요 하지 않습니다.

2. VM의 NIC에서 가속 네트워킹을 사용 하도록 설정 합니다.

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. VM을 다시 시작 하거나 가용성 집합에 있는 경우 집합의 모든 Vm을 다시 시작 하 고 가속화 된 네트워킹을 사용 하도록 설정 되었는지 확인 합니다.

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>가상 머신 크기 집합

가상 머신 확장 집합은 약간 다르지만 동일한 워크플로를 따릅니다.

1. Vm 중지:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. 네트워크 인터페이스에서 가속화 된 네트워킹 속성을 업데이트 합니다.

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. 적용 된 업데이트를 자동으로 설정 하 여 변경 내용이 즉시 선택 되도록 합니다.

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > 크기 집합에는 자동, 롤링 및 수동의 세 가지 설정을 사용 하 여 업데이트를 적용 하는 VM 업그레이드가 있습니다. 이러한 지침에서 정책은 자동으로 설정 되므로 확장 집합은 다시 시작 된 후 즉시 변경 내용을 선택 합니다.

4. 확장 집합을 다시 시작 합니다.

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

다시 시작한 후에는 업그레이드가 완료 될 때까지 기다립니다. 업그레이드가 완료 되 면 가상 함수 (VF)가 VM 내부에 표시 됩니다. 지원 되는 OS 및 VM 크기를 사용 하 고 있는지 확인 합니다.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>가속화 된 네트워킹을 사용 하 여 기존 Vm 크기 조정

VM에서 가속화 된 네트워킹을 사용 하는 경우 가속화 된 네트워킹을 지 원하는 VM 으로만 크기를 조정할 수 있습니다.  

가속 네트워킹을 사용 하는 VM은 크기 조정 작업을 사용 하는 가속화 된 네트워킹을 지원 하지 않는 VM 인스턴스로 크기를 조정할 수 없습니다. 이러한 VM 중 하나의 크기를 조정하려면 다음을 수행합니다.

1. VM을 중지 하거나 할당을 취소 합니다. 가용성 집합 또는 확장 집합의 경우 가용성 집합 또는 확장 집합의 모든 Vm을 중지 하거나 할당을 취소 합니다.

2. VM의 NIC에서 가속화 된 네트워킹을 사용 하지 않도록 설정 합니다. 가용성 집합 또는 확장 집합의 경우 가용성 집합 또는 확장 집합에 있는 모든 Vm의 Nic에서 가속화 된 네트워킹을 사용 하지 않도록 설정 합니다.

3. 가속 네트워킹을 사용 하지 않도록 설정한 후에는 VM, 가용성 집합 또는 확장 집합을 가속화 된 네트워킹을 지원 하지 않는 새 크기로 이동한 후 다시 시작 합니다.

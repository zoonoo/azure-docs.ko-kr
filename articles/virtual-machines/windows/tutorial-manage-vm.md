---
title: "Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리 | Microsoft Docs"
description: "자습서 - Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a26f33247710431d433f3309ecdaca20e605c75a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리

Azure Virtual Machines는 완전히 구성 가능하고 유연한 컴퓨팅 환경을 제공합니다. 이 자습서에서는 VM 크기 선택, VM 이미지 선택 및 VM 배포 등 기본적인 Azure Virtual Machines 배포 항목에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령으로 리소스 그룹을 만듭니다. 

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 컴퓨터보다 먼저 리소스 그룹을 만들어야 합니다. 이 예제에서는 *EastUS* 지역에 *myResourceGroupVM*이라는 리소스 그룹을 만듭니다. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

리소스 그룹은 VM을 만들거나 수정할 때 지정되며 이 자습서 전체에서 확인할 수 있습니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

가상 컴퓨터는 가상 네트워크에 연결되어야 합니다. 가상 컴퓨터와는 공용 IP 주소를 사용하여 네트워크 인터페이스 카드를 통해 통신합니다.

### <a name="create-virtual-network"></a>가상 네트워크 만들기

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷을 만듭니다.

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>공용 IP 주소 만들기

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다.

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>네트워크 인터페이스 카드 만들기

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 네트워크 인터페이스 카드를 만듭니다.

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

Azure NSG([네트워크 보안 그룹](../../virtual-network/virtual-networks-nsg.md))는 하나 또는 여러 가상 컴퓨터에 대한 인바운드 및 아웃바운드 트래픽을 제어합니다. 네트워크 보안 그룹 규칙은 특정 포트 또는 포트 범위에 대한 네트워크 트래픽을 허용하거나 거부합니다. 이러한 규칙에는 미리 정의된 원본에서 시작되는 트래픽만 가상 컴퓨터와 통신할 수 있도록 원본 주소 접두사가 포함될 수도 있습니다. 설치하는 IIS 웹 서버에 액세스하려면 인바운드 NSG 규칙을 추가해야 합니다.

인바운드 NSG 규칙을 만들려면 [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)를 사용합니다. 다음 예제는 이름이 *myNSGRule*이고 가상 컴퓨터에 대해 포트 *3389*를 여는 NSG 규칙을 만듭니다.

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 통해 *myNSGRule*을 사용하여 NSG를 만듭니다.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)를 사용하여 가상 네트워크의 서브넷에 NSG를 추가합니다.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)를 사용하여 가상 네트워크를 업데이트합니다.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

가상 컴퓨터를 만들 때 운영 체제 이미지, 디스크 크기 조정 및 관리 자격 증명 등의 몇 가지 옵션을 사용할 수 있습니다. 이 예제에서는 최신 버전의 Windows Server 2016 Datacenter를 실행하는 가상 컴퓨터가 *myVM*이라는 이름으로 생성됩니다.

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 가상 컴퓨터의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)를 사용하여 가상 컴퓨터에 대한 초기 구성을 만듭니다.

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem)을 사용하여 운영 체제 정보를 가상 컴퓨터 구성에 추가합니다.

```powershell
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)를 사용하여 이미지 정보를 가상 컴퓨터 구성에 추가합니다.

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)를 사용하여 운영 체제 디스크 설정을 가상 컴퓨터 구성에 추가합니다.

```powershell
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface)를 사용하여 이전에 만든 네트워크 인터페이스 카드를 가상 컴퓨터 구성에 추가합니다.

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 컴퓨터를 만듭니다.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>VM에 연결

배포가 완료된 후 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다.

다음 명령을 실행하여 가상 컴퓨터의 공용 IP 주소를 반환합니다. 다음 단계에서 웹 연결을 테스트하기 위해 브라우저와 연결할 수 있도록 이 IP 주소를 기록해 둡니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

다음 명령을 사용하여 가상 컴퓨터와의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 컴퓨터의 *publicIPAddress*로 바꿉니다. 가상 컴퓨터를 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>VM 이미지 이해

Azure Marketplace에는 새 가상 컴퓨터를 만드는 데 사용할 수 있는 여러 가상 컴퓨터 이미지가 포함되어 있습니다. 이전 단계에서는 Windows Server 2016-Datacenter 이미지를 사용하여 가상 컴퓨터를 만들었습니다. 이번 단계에서는 PowerShell 모듈을 사용하여 Marketplace에서 새 VM의 기반이 될 수도 있는 다른 Windows 이미지를 검색합니다. 이 프로세스는 게시자, 제안 및 이미지 이름(SKU) 검색으로 이루어져 있습니다. 

이미지 게시자 목록을 반환하려면 [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 명령을 사용합니다.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

이미지 제안 목록을 반환하려면 [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer)를 사용합니다. 이 명령을 사용하면 반환된 목록이 지정된 게시자를 기준으로 필터링됩니다. 

```powershell
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

그런 다음 [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) 명령으로 게시자 및 제안 이름을 기준으로 필터링하여 이미지 이름 목록을 반환합니다.

```powershell
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

이 정보를 사용하여 특정 이미지가 있는 VM을 배포할 수 있습니다. 이 예제에서는 VM 개체에서 이미지 이름을 설정합니다. 배포 단계를 완료하려면 이 자습서의 이전 예제를 참조하세요.

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>VM 크기 이해

가상 컴퓨터 크기에 따라 CPU, GPU, 메모리 등 가상 컴퓨터에 사용할 수 있는 계산 리소스의 양이 결정됩니다. Virtual Machines는 예상되는 워크로드에 맞는 크기로 만들어야 합니다. 워크로드가 증가할 경우 기존 가상 컴퓨터의 크기를 조정할 수 있습니다.

### <a name="vm-sizes"></a>VM 크기

다음 표에서는 크기를 사용 사례로 분류합니다.  

| 형식                     | 크기           |    설명       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| 범용 가상 컴퓨터         |DSv2, Dv2, DS, D, Av2, A0-7| CPU 대 메모리 비율이 적당합니다. 개발/테스트와 소규모에서 중간 정도의 응용 프로그램 및 데이터 솔루션에 적합합니다.  |
| Compute에 최적화      | Fs, F             | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 응용 프로그램, 네트워크 어플라이언스 및 일괄 처리 프로세스에 적합합니다.        |
| 메모리에 최적화       | GS, G, DSv2, DS, Dv2, D   | 메모리 대 코어 비율이 높습니다. 관계형 데이터베이스, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| Storage에 최적화       | Ls                | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| GPU           | NV, NC            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 VM입니다.       |
| 고성능 | H, A8-11          | 당사의 가장 강력한 CPU VM으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. 


### <a name="find-available-vm-sizes"></a>사용 가능한 VM 크기 찾기

특정 영역에서 사용할 수 있는 VM 크기의 목록을 보려면 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 명령을 사용합니다.

```powershell
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>VM 크기 조정

VM을 배포한 후에 크기를 조정하여 리소스 할당을 늘리거나 줄일 수 있습니다.

VM의 크기를 조정하기 전에 원하는 크기를 현재 VM 클러스터에서 사용할 수 있는지 확인합니다. [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 명령은 크기 목록을 반환합니다. 

```powershell
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

원하는 크기를 사용할 수 있는 경우 전원이 켜진 상태에서 VM 크기를 조정할 수 있지만 작업 중 다시 부팅됩니다.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

원하는 크기가 현재 클러스터에 없는 경우 크기 조정 작업 전에 VM 할당을 취소해야 합니다. VM의 전원이 다시 켜지면 임시 디스크의 모든 데이터가 제거되고 고정 IP 주소를 사용하지 않는 한 공용 IP 주소가 변경됩니다. 

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>VM 전원 상태

Azure VM의 전원 상태는 여러 상태 중 하나일 수 있습니다. 이 상태는 하이퍼바이저의 관점에서 VM의 현재 상태를 나타냅니다. 

### <a name="power-states"></a>전원 상태

| 전원 상태 | 설명
|----|----|
| Starting | 가상 컴퓨터가 시작되고 있음을 나타냅니다. |
| 실행 중 | 가상 컴퓨터가 실행되고 있음을 나타냅니다. |
| 중지 중 | 가상 컴퓨터가 중지되고 있음을 나타냅니다. | 
| 중지됨 | 가상 컴퓨터가 중지되었음을 나타냅니다. 중지됨 상태의 Virtual Machines에도 여전히 계산 요금이 발생됩니다.  |
| 할당 취소 중 | 가상 컴퓨터의 할당이 취소되고 있음을 나타냅니다. |
| 할당 취소됨 | 가상 컴퓨터가 하이퍼바이저에서 완전히 제거되었지만 제어 영역에서 계속 사용할 수 있음을 나타냅니다. 할당 취소됨 상태의 Virtual Machines에는 계산 요금이 발생하지 않습니다. |
| - | 가상 컴퓨터의 전원 상태가 알 수 없음을 나타냅니다. |

### <a name="find-power-state"></a>전원 상태 찾기

특정 VM의 상태를 검색하려면 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 명령을 사용합니다. 가상 컴퓨터 및 리소스 그룹에 대한 올바른 이름을 지정해야 합니다. 

```powershell
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

출력:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>관리 작업

가상 컴퓨터의 수명 주기 동안 가상 컴퓨터 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 스크립트를 만들 수도 있습니다. Azure PowerShell을 사용하면 명령줄이나 스크립트로 여러 가지 일반적인 관리 작업을 실행할 수 있습니다.

### <a name="stop-virtual-machine"></a>가상 컴퓨터 중지

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 가상 컴퓨터를 중지하고 할당을 취소합니다.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

가상 컴퓨터를 프로비전된 상태로 유지하려면 -StayProvisioned 매개 변수를 사용합니다.

### <a name="start-virtual-machine"></a>가상 컴퓨터 시작

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>리소스 그룹 삭제

리소스 그룹을 삭제하면 그 안에 포함된 리소스도 모두 삭제됩니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법과 같이 기본 VM을 만들고 관리하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

VM 디스크에 대해 자세히 알아보려면 다음 자습서로 이동합니다.  

> [!div class="nextstepaction"]
> [VM 디스크 만들기 및 관리](./tutorial-manage-data-disk.md)


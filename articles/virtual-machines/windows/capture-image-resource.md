---
title: Azure에서 관리 이미지 만들기 | Microsoft Docs
description: Azure에서 일반화된 VM 또는 VHD의 관리 이미지를 만듭니다. 이미지를 사용하여 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: aa1858a27d4df413deb562391251a523c28673ad
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787949"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지 만들기

저장소 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM(가상 머신)에서 관리 이미지 리소스를 만들 수 있습니다. 여러 VM을 만드는 데 이미지를 사용할 수 있습니다. 관리형 이미지의 청구 방법에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 모든 개인 계정 및 보안 정보를 제거한 다음 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 개요](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [Sysprep의 서버 역할 지원](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

> [!IMPORTANT]
> VM에서 Sysprep을 실행하고 나면 해당 VM은 *일반화*된 것으로 간주되므로 다시 시작할 수 없습니다. VM 일반화 프로세스는 되돌릴 수 없습니다. 원래 VM을 작동하는 상태로 유지해야 하는 경우에는 [VM의 복사본](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)을 만들고 복사본을 일반화해야 합니다. 
>
> Azure에 VHD(가상 하드 디스크)를 처음으로 업로드하기 전에 Sysprep을 실행하려는 경우 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다.  
> 
> 

Windows VM을 일반화하려면 다음 단계를 수행합니다.

1. Windows VM에 로그인합니다.
   
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 %windir%\system32\sysprep로 변경한 후 `sysprep.exe`를 실행합니다.
   
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 시작**을 선택하고 **일반화** 확인란을 선택합니다.
   
4. **종료 옵션**에서 **종료**를 선택합니다.
   
5. **확인**을 선택합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep은 작업을 완료하면 VM을 종료합니다. VM을 다시 시작하지 않습니다.


## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. 왼쪽 메뉴에서 **가상 머신**을 선택하고 목록에서 VM을 선택합니다.

3. VM의 **가상 머신** 페이지 위쪽 메뉴에서 **캡처**를 선택합니다.

   **이미지 만들기** 페이지가 나타납니다.

4. **이름**에서 미리 입력된 이름을 적용하거나 이미지에 사용할 이름을 입력합니다.

5. **리소스 그룹**에서 **새로 만들기**를 선택하고 이름을 입력하거나 **기존 항목 사용**을 선택하고 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.

6. 이미지가 만들어진 후 원본 VM을 삭제하려면 **이미지를 만든 후 이 가상 머신을 자동으로 삭제**를 선택합니다.

7. 모든 [가용성 영역](../../availability-zones/az-overview.md)에서 이미지를 사용하려면 **영역 복원력**에서 **설정**을 선택합니다.

8. **만들기**를 선택하여 이미지를 만듭니다.

9. 생성된 이미지는 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시됩니다.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Powershell을 사용하여 VM 이미지 만들기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다. 이 예제에서는 VM에서 관리되는 디스크를 사용하는 관리되는 이미지를 만드는 방법을 보여 줍니다.

시작 하기 전에 Azure PowerShell 모듈의 최신 버전이 있는지 확인 합니다. 버전을 확인하려면 PowerShell에서 `Get-Module -ListAvailable Az`를 실행합니다. 버전을 업그레이드해야 하는 경우 [PowerShellGet을 사용하여 Windows에서 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결합니다.


> [!NOTE]
> 이미지를 영역 중복 저장소에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 이미지 구성에 `-ZoneResilient` 매개 변수(`New-AzImageConfig` 명령)를 포함하는 지역에 이미지를 만들어야 합니다.

VM 이미지를 만들려면 다음 단계를 수행합니다.

1. 일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 가상 머신의 상태를 **일반화됨**으로 설정합니다. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 가상 머신을 가져옵니다. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell을 사용하여 관리되는 디스크에서 이미지 만들기

OS 디스크의 이미지만 만들려면 관리 디스크 ID를 OS 디스크로 지정합니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 관리되는 디스크의 ID를 가져옵니다.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Powershell을 사용하여 스냅샷에서 이미지 만들기

다음 단계를 수행하면 일반화된 VM의 스냅샷에서 관리되는 이미지를 만들 수 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 스냅샷을 가져옵니다.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>저장소 계정의 VHD에서 이미지 만들기

저장소 계정의 일반화된 OS VHD에서 관리되는 이미지를 만듭니다. 스토리지 계정에 있는 VHD의 URI가 필요합니다. 이 URI는 https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd* 형식입니다. 이 예제의 VHD는 *vhdcontainer* 컨테이너의 *mystorageaccount*에 있으며 VHD 파일 이름은 *vhdfilename.vhd*입니다.


1.  일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM을 중지하고 할당을 취소합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM을 일반화됨으로 표시합니다.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>다음 단계
- [관리되는 이미지에서 VM 만들기](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행합니다.    


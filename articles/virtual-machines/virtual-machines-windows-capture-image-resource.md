---
title: "Azure에서 관리 이미지 만들기 | Microsoft Docs"
description: "Azure에서 일반화된 VM 또는 VHD의 관리 이미지를 만듭니다. 이미지를 사용하여 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: 12b2d12c5152c6e9b8f2e8c78fbbd6663cb24e45


---
# <a name="how-to-capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지를 캡처하는 방법

저장소 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM으로 관리 이미지 리소스를 만들 수 있습니다. 그런 다음 그 이미지를 사용하여 저장소에 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다. 


## <a name="prerequisites"></a>필수 조건
이미 [일반화된 VM](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)이 있어야 하고 VM을 중지/할당 취소해야 합니다. VM을 일반화하면 여러 정보 중에서 모든 개인 계정 정보가 제거되고 이미지로 사용할 컴퓨터가 준비됩니다.



## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. [포털](https://portal.azure.com)을 엽니다.
2. 더하기 기호를 클릭하여 새 리소스를 만듭니다.
3. 필터 검색에 **이미지**를 입력합니다.
4. 결과에서 **이미지**를 선택합니다.
5. **이미지** 블레이드에서 **만들기**를 클릭합니다.
6. **이름**에 이미지 이름을 입력합니다.
7. **리소스 그룹**에서 **새로 만들기**를 선택하고 이름을 입력하거나 **기존 항목**을 선택하고 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.
8. **위치**에서 리소스 그룹의 위치를 선택합니다.
9. **OS 유형**에서 Windows 또는 Linux 중에 운영 체제 유형을 선택합니다.
11. **저장소 BLOB**에서 **찾아보기**를 클릭하여 Azure 저장소의 VHD를 찾습니다.
12. **계정 유형**에서 Standard_LRS 또는 Premium_LRS를 선택합니다. 표준은 하드 디스크 드라이브를 사용하고 프리미엄은 반도체 드라이브를 사용합니다. 둘 다 로컬 중복 저장소를 사용합니다.
13. **디스크 캐싱**에서 적절한 디스크 캐싱 옵션을 선택합니다. 옵션은 **없음**, **읽기 전용** 및 **읽기/쓰기**입니다.
14. 선택 사항: 이미지에 기존 데이터 디스크를 추가할 수도 있습니다.  
15. 선택을 마치면 **만들기**를 클릭합니다.
16. 이미지가 생성되면 선택한 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시될 것입니다.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Powershell을 사용하여 VM 관리 이미지 만들기

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다.


시작하기 전에 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)를 참조하세요.


1. 일부 변수를 만듭니다. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -VMName $vmName -Force
    ```
    
3. 가상 컴퓨터의 상태를 **일반화됨**으로 설정합니다. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 가상 컴퓨터를 가져옵니다. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 이미지 구성을 만듭니다.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. 이미지를 만듭니다.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>PowerShell에서 VHD 관리 이미지 만들기

일반화된 OS VHD를 사용하여 관리 이미지를 만듭니다.


1.  먼저, 공통 매개 변수를 설정합니다.

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. VM을 중지/할당 취소합니다.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -VMName $vmName -Force
    ```
    
3. VM을 일반화됨으로 표시합니다.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Powershell을 사용하여 스냅숏으로 관리 이미지 만들기

일반화된 VM에서 VHD 스냅숏으로 관리 이미지를 만들 수도 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 스냅숏을 가져옵니다.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 이미지 구성을 만듭니다.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 이미지를 만듭니다.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>다음 단계
- 이제 [일반화된 관리 이미지로 VM 만들기](virtual-machines-windows-create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행할 수 있습니다.    




<!--HONumber=Feb17_HO2-->



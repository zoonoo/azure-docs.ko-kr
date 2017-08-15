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
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e428b755f6696bd6d4047ad77579a8e9665dfbd8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/09/2017

---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지 만들기

저장소 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM으로 관리 이미지 리소스를 만들 수 있습니다. 여러 VM을 만드는 데 이미지를 사용할 수 있습니다. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Azure에 VHD를 업로드하기 전에 Sysprep을 처음으로 실행하는 경우 Sysprep을 실행하기 전에 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 컴퓨터에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\system32\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 클릭합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. VM을 다시 시작하지 마세요.


## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. [포털](https://portal.azure.com)을 엽니다.
2. 더하기 기호를 클릭하여 새 리소스를 만듭니다.
3. 필터 검색에 **이미지**를 입력합니다.
4. 결과에서 **이미지**를 선택합니다.
5. **이미지** 블레이드에서 **만들기**를 클릭합니다.
6. **이름**에 이미지 이름을 입력합니다.
7. 둘 이상의 구독이 있는 경우 **구독** 드롭다운에서 올바른 구독을 선택합니다.
7. **리소스 그룹**에서 **새로 만들기**를 선택하고 이름을 입력하거나 **기존 항목**을 선택하고 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.
8. **위치**에서 리소스 그룹의 위치를 선택합니다.
9. **OS 유형**에서 Windows 또는 Linux 중에 운영 체제 유형을 선택합니다.
11. **저장소 Blob**에서 **찾아보기**를 클릭하여 Azure Storage에서 VHD를 찾습니다.
12. **계정 유형**에서 Standard_LRS 또는 Premium_LRS를 선택합니다. 표준은 하드 디스크 드라이브를 사용하고 프리미엄은 반도체 드라이브를 사용합니다. 둘 다 로컬 중복 저장소를 사용합니다.
13. **디스크 캐싱**에서 적절한 디스크 캐싱 옵션을 선택합니다. 옵션은 **없음**, **읽기 전용** 및 **읽기/쓰기**입니다.
14. 선택 사항: **+ 데이터 디스크 추가**를 클릭하여 이미지에 기존 데이터 디스크를 추가할 수도 있습니다.  
15. 선택을 마치면 **만들기**를 클릭합니다.
16. 이미지가 생성되면 선택한 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시될 것입니다.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Powershell을 사용하여 VM 관리 이미지 만들기

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다.


시작하기 전에 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.


1. 일부 변수를 만듭니다.

    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
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
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
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
- 이제 [일반화된 관리 이미지로 VM 만들기](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행할 수 있습니다.  



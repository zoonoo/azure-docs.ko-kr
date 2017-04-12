---
title: "Azure에서 VHD로 관리 디스크 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델을 사용하여 현재 Azure 저장소 계정에 있는 VHD로 관리 디스크를 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 51dbf591138434f1639dd6d4e93758927cff6d33
ms.lasthandoff: 03/31/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>저장소 계정의 비관리 디스크로 관리 디스크 만들기

현재 Azure 저장소 계정에 있는 기존 데이터 디스크 또는 OS 디스크로 관리 디스크를 만들 수 있습니다. 또한 VM의 새 데이터 디스크로 사용할 수 있는 빈 디스크를 만들 수 있습니다. 

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)를 참조하세요.


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Azure 저장소 계정에서 VHD로 관리 디스크 만들기 | Microsoft Docs

이 예제에서는 VHD의 디스크로 관리 디스크를 만들고 나중에 사용할 수 있도록 **$disk1** 매개 변수에 할당합니다. 

관리 디스크는 **미국 서부** 위치의 **myResourceGroup**이라는 리소스 그룹에 생성됩니다. 디스크 이름은 **myDisk**로 할 것이며 이전에 **mystorageaccount**라는 저장소 계정에 업로드한 **myDisk.vhd** VHD 파일로 만들겠습니다. 관리 디스크는 프리미엄 LRS(로컬 중복 저장소)에 생성됩니다. StandardLRS 및 PremiumLRS는 관리 디스크에 제공되는 유일한 **-AccountType** 옵션입니다. 

1.  일부 매개 변수 설정

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. 데이터 디스크를 만듭니다. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>관리 디스크로 빈 데이터 디스크 만들기

이 예제에서는 관리 디스크로 빈 데이터 디스크를 만들고 나중에 사용할 수 있도록 **$dataDisk2** 매개 변수에 할당합니다. 실행 중인 VM에 빈 데이터 디스크가 연결되면 diskmgmt.msc를 사용하거나 [원격으로 WinRM 및 스크립트를 사용](attach-disk-ps.md#initialize-the-disk)하여 빈 데이터 디스크를 초기화하고 VM에 로그인해야 합니다.

빈 데이터 디스크는 **미국 중서부** 위치의 **myResourceGroup** 리소스 그룹에 생성됩니다. 디스크 이름은 **myEmptyDataDisk**입니다. 빈 디스크는 프리미엄 LRS(로컬 중복 저장소)에 생성됩니다. StandardLRS 및 PremiumLRS는 관리 디스크에 제공되는 유일한 **-AccountType** 옵션입니다.

이 예제의 디스크 크기는 128GB입니다. 하지만 VM에서 실행 중인 모든 응용 프로그램의 요구를 충족하는 크기를 선택해야 합니다.

1.  일부 매개 변수 설정

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. 데이터 디스크를 만듭니다.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>다음 단계    
- 이미 VM이 있는 경우 [데이터 디스크를 연결](attach-disk-portal.md)할 수 있습니다.

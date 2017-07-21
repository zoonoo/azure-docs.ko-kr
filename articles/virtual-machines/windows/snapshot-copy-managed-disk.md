---
title: "백업용 Azure Managed Disk의 복사본 만들기 | Microsoft Docs"
description: "백업 또는 디스크 문제 해결에 사용할 수 있는 Azure Managed Disk의 복사본을 만드는 방법을 알아봅니다."
documentationcenter: 
author: cwatson-cat
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: bb913050fd3388d4632e6f75b36415006f370cbd
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017

---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>관리 스냅숏을 사용하여 Azure Managed Disk로 저장된 VHD 복사본 만들기
백업용 Managed Disk의 스냅숏을 만들거나 스냅숏으로 Managed Disk를 만들고 테스트 가상 컴퓨터에 연결하여 문제를 해결합니다. 관리 스냅숏은 VM Managed Disk의 특정 시점 전체 복사본입니다. VHD의 읽기 전용 복사본을 만들어서 기본적으로 표준 Managed Disk로 저장합니다. Managed Disk에 대한 자세한 내용은 [Azure Managed Disks 개요](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.

## <a name="copy-the-vhd-with-a-snapshot"></a>스냅숏을 사용하여 VHD 복사
Azure Portal 또는 PowerShell을 사용하여 Managed Disk의 스냅숏을 만듭니다.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal을 사용하여 스냅숏 만들기 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위에서 **새로 만들기**를 클릭하고 **스냅숏**을 검색합니다.
3. 스냅숏 블레이드에서 **만들기**를 클릭합니다.
4. 스냅숏의 **이름**을 입력합니다.
5. 기존 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
6. Azure 데이터 센터 위치를 선택합니다.  
7. **원본 디스크**에서 스냅숏을 만들 Managed Disk를 선택합니다.
8. 스냅숏 저장에 사용할 **계정 유형**을 선택합니다. 고성능 디스크에 저장할 필요가 없다면 **Standard_LRS**를 권장합니다.
9. **만들기**를 클릭합니다.

### <a name="use-powershell-to-take-a-snapshot"></a>PowerShell을 사용하여 스냅숏 만들기
다음 단계에서는 New-AzureRmSnapshot cmdlet<!--Add link to cmdlet when available-->을 사용하여 VHD 디스크를 복사하고, 스냅숏 구성을 만들며 디스크의 스냅숏을 만드는 방법을 보여 줍니다. 

1. 일부 매개 변수를 설정합니다. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  매개 변수 값을 바꿉니다.
  -  "myResourceGroup"을 VM의 리소스 그룹으로 바꿉니다.
  -  "southeastasia"를 관리 스냅숏을 저장하려는 지리적 위치로 바꿉니다. <!---How do you look these up? -->
  -  "ContosoMD_datadisk1"을 복사하려는 VHD 디스크의 이름으로 바꿉니다.
  -  "ContosoMD_datadisk1_snapshot1"을 새 스냅숏을 사용 하려는 이름으로 바꿉니다.

2. 복사할 VHD 디스크를 가져옵니다.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 스냅숏 구성을 만듭니다. 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 스냅숏을 만듭니다.

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
스냅숏을 사용하여 Managed Disk를 만들고 고성능이 필요한 VM에 스냅숏을 연결하려는 경우 `-AccountType Premium_LRS` 매개 변수와 New-AzureRmSnapshot 명령을 사용합니다. 매개 변수는 스냅숏을 만들어서 프리미엄 Managed Disk로 저장되도록 합니다. 프리미엄 Managed Disks는 표준 Managed Disks보다 비용이 많이 듭니다. 따라서 해당 매개 변수를 사용하기 전에 프리미엄이 필요한지 확인합니다.




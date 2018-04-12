---
title: Azure에서 VHD의 스냅숏 만들기 | Microsoft Docs
description: 백업 또는 문제 해결을 위해 사용할 Azure VM의 복사본을 만드는 방법을 알아봅니다.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: c5f4c7224e04b601d7d3fe4da7d8f5f0c02c7039
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-snapshot"></a>스냅숏 만들기

백업 또는 VM 문제 해결을 위해 OS 또는 데이터 디스크 VHD의 스냅숏을 만듭니다. 스냅숏은 VHD의 전체 읽기 전용 복사본입니다. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal을 사용하여 스냅숏 만들기 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위에서 **리소스 만들기**를 클릭하고 **스냅숏**을 검색합니다.
3. 스냅숏 블레이드에서 **만들기**를 클릭합니다.
4. 스냅숏의 **이름**을 입력합니다.
5. 기존 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
6. Azure 데이터 센터 위치를 선택합니다.  
7. **원본 디스크**에서 스냅숏을 만들 Managed Disk를 선택합니다.
8. 스냅숏 저장에 사용할 **계정 유형**을 선택합니다. 고성능 디스크에 저장할 필요가 없다면 **Standard_LRS**를 권장합니다.
9. **만들기**를 클릭합니다.

## <a name="use-powershell-to-take-a-snapshot"></a>PowerShell을 사용하여 스냅숏 만들기
다음 단계에서는 [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet을 사용하여 VHD 디스크를 복사하고, 스냅숏 구성을 만들며 디스크의 스냅숏을 만드는 방법을 보여 줍니다. 

AzureRM.Compute PowerShell 모듈의 최신 버전이 설치되어 있는지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.


1. 일부 매개 변수를 설정합니다. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. 복사할 VHD 디스크를 가져옵니다.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 스냅숏 구성을 만듭니다. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 스냅숏을 만듭니다.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
스냅숏을 사용하여 Managed Disk를 만들고 고성능이 필요한 VM에 스냅숏을 연결하려는 경우 `-AccountType Premium_LRS` 매개 변수와 New-AzureRmSnapshot 명령을 사용합니다. 매개 변수는 스냅숏을 만들어서 프리미엄 Managed Disk로 저장되도록 합니다. 프리미엄 Managed Disks는 표준 Managed Disks보다 비용이 많이 듭니다. 따라서 해당 매개 변수를 사용하기 전에 프리미엄이 필요한지 확인합니다.

## <a name="next-steps"></a>다음 단계

스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 머신을 만듭니다. 자세한 내용은 [스냅숏에서 VM 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) 샘플을 참조하세요.

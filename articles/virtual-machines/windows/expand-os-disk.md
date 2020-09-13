---
title: Azure에서 Windows VM의 OS 드라이브 확장
description: 리소스 관리자 배포 모델에서 Azure PowerShell를 사용 하 여 가상 머신의 OS 드라이브 크기를 확장 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 3f1c7fb08c3dcaa078de8f1ee0b90409289cfb43
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469262"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>가상 머신의 OS 드라이브 확장 방법

[Azure Marketplace](https://azure.microsoft.com/marketplace/)에서 이미지를 배포 하 여 리소스 그룹에 새 VM (가상 컴퓨터)을 만들 때 기본 os 드라이브는 127 GB입니다 (일부 이미지는 기본적으로 OS 디스크 크기가 작음). VM에 데이터 디스크를 추가할 수 있지만 (숫자가 선택한 SKU에 따라 다름), 이러한 추 록 디스크에 응용 프로그램 및 CPU 집약적 워크 로드를 설치 하는 것이 좋습니다. 일반적으로 고객은 특정 시나리오를 지원 하기 위해 OS 드라이브를 확장 해야 합니다.

- OS 드라이브에 구성 요소를 설치 하는 레거시 응용 프로그램을 지원 합니다.
- 더 큰 OS 드라이브가 있는 온-프레미스에서 실제 PC 또는 VM을 마이그레이션합니다.

> [!IMPORTANT]
> Azure Virtual Machine의 OS 디스크 크기를 조정하려면 가상 머신의 할당을 취소해야 합니다.
>
> 디스크를 확장한 후 더 큰 디스크를 활용하려면 [OS 내에서 볼륨](#expand-the-volume-within-the-os)을 확장해야 합니다.
> 

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Azure Portal에서 관리 디스크 크기 조정

1. [Azure Portal](https://portal.azure.com)에서 디스크를 확장 하려는 가상 머신으로 이동 합니다. **중지** 를 선택 하 여 VM의 할당을 취소 합니다.
2. VM이 중지 되 면 **설정**아래의 왼쪽 메뉴에서 **디스크**를 선택 합니다.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="메뉴의 설정 섹션에서 선택한 디스크 옵션을 보여 주는 스크린샷":::

 
3. **디스크 이름**에서 크기를 조정 하려는 디스크를 선택 합니다.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="디스크 이름이 선택 된 디스크 창을 보여 주는 스크린샷":::

4. 왼쪽 메뉴의 **설정**아래에서 **구성**을 선택 합니다.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="메뉴의 설정 섹션에서 선택한 구성 옵션을 보여 주는 스크린샷":::

5. **크기 (GiB)** 에서 원하는 디스크 크기를 선택 합니다.
   
   > [!WARNING]
   > 새 크기가 기존 디스크 크기보다 커야 합니다. 허용 되는 최대값은 OS 디스크에 2048 GB입니다. (이 크기를 초과 하는 VHD blob을 확장할 수 있지만 OS는 처음 2048 GB의 공간만 사용할 수 있습니다.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="선택한 디스크 크기를 사용 하 여 구성 창을 보여 주는 스크린샷":::

6. **저장**을 선택합니다.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="저장 단추가 선택 된 상태에서 구성 창을 보여 주는 스크린샷":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>PowerShell을 사용 하 여 관리 디스크 크기 조정

관리 모드에서 PowerShell ISE 또는 PowerShell 창을 열고 다음 단계를 수행 합니다.

1. 리소스 관리 모드에서 Microsoft Azure 계정에 로그인 하 고 구독을 선택 합니다.
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. 리소스 그룹 이름 및 VM 이름 설정:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM에 대 한 참조를 가져옵니다.
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. 디스크 크기를 조정 하기 전에 VM을 중지 합니다.
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. 관리되는 OS 디스크에 대한 참조를 가져옵니다. 관리 되는 OS 디스크의 크기를 원하는 값으로 설정 하 고 디스크를 업데이트 합니다.
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > 새 크기가 기존 디스크 크기보다 커야 합니다. 허용 되는 최대값은 OS 디스크에 2048 GB입니다. (이 크기를 초과 하는 VHD blob을 확장할 수 있지만 OS는 처음 2048 GB의 공간만 사용할 수 있습니다.)
    > 
         
6. VM을 업데이트 하는 데 몇 초 정도 걸릴 수 있습니다. 명령 실행이 완료 되 면 VM을 다시 시작 합니다.
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

이것으로 끝입니다! 이제 VM에 RDP를 실행하고, 컴퓨터 관리(또는 디스크 관리)를 열고, 새로 할당된 공간을 사용하여 드라이브를 확장합니다.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>PowerShell을 사용 하 여 관리 되지 않는 디스크 크기 조정

관리 모드에서 PowerShell ISE 또는 PowerShell 창을 열고 다음 단계를 수행 합니다.

1. 리소스 관리 모드에서 Microsoft Azure 계정에 로그인 하 고 구독을 선택 합니다.
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. 리소스 그룹 이름 및 VM 이름 설정:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM에 대 한 참조를 가져옵니다.
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. 디스크 크기를 조정 하기 전에 VM을 중지 합니다.
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. 관리 되지 않는 OS 디스크의 크기를 원하는 값으로 설정 하 고 VM을 업데이트 합니다.
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > 새 크기가 기존 디스크 크기보다 커야 합니다. 허용 되는 최대값은 OS 디스크에 2048 GB입니다. (이 크기를 초과 하는 VHD blob을 확장할 수는 있지만, OS는 처음 2048 GB의 공간만 사용할 수 있습니다.)
    > 
    > 
   
6. VM을 업데이트 하는 데 몇 초 정도 걸릴 수 있습니다. 명령 실행이 완료 되 면 VM을 다시 시작 합니다.
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>OS 디스크용 스크립트

관리 디스크 및 관리되지 않는 디스크에 대한 참조용 전체 스크립트는 다음과 같습니다.


**관리되는 디스크**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**관리되지 않는 디스크**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>데이터 디스크 크기 조정

이 문서는 주로 VM의 OS 디스크를 확장하는 데 중점을 두지만 스크립트는 VM에 연결된 데이터 디스크를 확장하는 데 사용할 수도 있습니다. 예를 들어 VM에 연결된 첫 번째 데이터 디스크를 확장하려면 아래와 같이 `StorageProfile`의 `OSDisk` 개체를 `DataDisks` 배열로 바꾸고 숫자 인덱스를 사용하여 첫 번째 연결된 데이터 디스크의 참조를 가져옵니다.

**관리 디스크**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**관리되지 않는 디스크**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

마찬가지로 위에 표시 된 것 처럼 인덱스를 사용 하거나 디스크의 **이름** 속성을 사용 하 여 VM에 연결 된 다른 데이터 디스크를 참조할 수 있습니다.


**관리 디스크**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**관리되지 않는 디스크**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>OS 내에서 볼륨 확장

VM에 대 한 디스크를 확장 한 경우 OS로 이동 하 고 볼륨을 확장 하 여 새 공간을 포함 해야 합니다. 파티션을 확장하는 여러 방법이 있습니다. 이 섹션에서는 RDP 연결을 사용하여 VM을 연결하고 **DiskPart**를 사용하여 파티션을 확장하는 방법을 설명합니다.

1. VM에 대한 RDP 연결을 엽니다.

2. 명령 프롬프트를 열고 **diskpart**를 입력합니다.

3. **DISKPART** 프롬프트에 `list volume`를 입력합니다. 확장할 볼륨을 기록해 둡니다.

4. **DISKPART** 프롬프트에 `select volume <volumenumber>`를 입력합니다. 동일한 디스크의 인접한 빈 공간으로 확장하려는 볼륨 *volumenumber*가 선택됩니다.

5. **DISKPART** 프롬프트에 `extend [size=<size>]`를 입력합니다. 선택된 볼륨이 *size*(MB)만큼 확장됩니다.


## <a name="next-steps"></a>다음 단계

[Azure Portal](attach-managed-disk-portal.md)을 사용하여 디스크를 연결할 수도 있습니다.

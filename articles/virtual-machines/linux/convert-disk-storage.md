---
title: Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환 | Microsoft Docs
description: Azure CLI를 사용하여 Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 42b0b7a1ca2767a7051a6c57ef2aeac8cf2bb64c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477406"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환

Managed Disk는 세 가지 스토리지 옵션을 제공합니다. [프리미엄 SSD](../windows/premium-storage.md), 표준 SSD 및 [표준 HDD](../windows/standard-storage.md)입니다. 성능 요구 사항에 따라 최소한의 가동 중지 시간으로 옵션 사이를 쉽게 전환할 수 있습니다. 이 항목은 현재 관리되지 않는 디스크에 지원되지 않습니다. 하지만 디스크 유형 사이를 쉽게 전환하도록 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)할 수 있습니다.

이 문서는 Azure CLI를 사용하여 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법을 설명합니다. CLI를 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에

* 변환은 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약하도록 VM의 재시작이 필요합니다. 
* 관리되지 않는 디스크를 사용하는 경우, 이 문서를 사용하여 저장소 옵션 사이로 전환하려면 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)해야 합니다. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM의 모든 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

다음 예제에서는 VM의 모든 디스크를 표준에서 Premium Storage로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합할 수도 있습니다. 더 나은 성능을 요구하는 디스크만 Premium Storage로 업그레이드하여 이를 수행할 수 있습니다. 다음 예제에서는 VM의 단일 디스크를 표준에서 Premium Storage로, 또 그 반대로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>관리 디스크를 표준 HDD에서 표준 SSD로, 또 그 반대로 변환

다음 예제에서는 VM의 단일 디스크를 표준 HDD에서 표준 SSD로 변환하는 방법을 보여줍니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Azure Portal을 사용하여 변환

Azure Portal을 사용하여 관리되지 않는 디스크에서 관리 디스크로 변환할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 VM 목록에서 VM을 선택합니다.
3. VM 블레이드의 메뉴에서 **디스크**를 선택합니다.
4. **디스크** 블레이드 상단에서 **관리 디스크로 마이그레이션**을 선택합니다.
5. VM이 가용성 집합에 있으면 **관리 디스크로 마이그레이션** 블레이드에 가용성 집합을 먼저 변환해야 한다는 경고가 표시됩니다. 경고에는 클릭하여 가용성 집합을 변환할 수 있는 링크가 있습니다. 가용성 집합이 변환되거나 VM이 가용성 집합에 없는 경우에는 **마이그레이션**을 클릭하여 디스크를 관리 디스크로 마이그레이션하는 프로세스를 시작합니다. 

VM이 중지되고 마이그레이션이 완료된 후 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.


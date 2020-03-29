---
title: 관리 디스크 스토리지를 표준 SSD와 프리미엄 SSD 간에 변환
description: Azure CLI를 사용하여 Azure 관리 디스크 저장소를 표준에서 프리미엄 또는 프리미엄으로 변환하는 방법
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431498"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure 관리 디스크 저장소를 표준에서 프리미엄 또는 프리미엄으로 표준으로 변환

Azure 관리 형 디스크에는 Azure 울트라 SSD (미리 보기), 프리미엄 SSD, 표준 SSD 및 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구에 따라 세 가지 GA 디스크 유형(프리미엄 SSD, 표준 SSD 및 표준 HDD) 간에 전환할 수 있습니다. 아직 울트라 SSD에서 전환하거나 울트라 SSD로 전환할 수 없는 경우 새 SSD를 배포해야 합니다.

관리되지 않는 디스크에는 이 기능이 지원되지 않습니다. 그러나 [관리되지 않는 디스크를 관리 디스크로](convert-unmanaged-to-managed-disks.md) 쉽게 변환하여 디스크 유형 간에 전환할 수 있습니다.

이 문서에서는 Azure CLI를 사용하여 관리디스크를 표준에서 프리미엄 또는 프리미엄으로 변환하는 방법을 보여 주며, 이를 통해 도구를 설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

* 디스크 변환에는 VM(가상 시스템)이 다시 시작되므로 기존 유지 관리 기간 동안 디스크 저장소마이그레이션을 예약해야 합니다.
* 관리되지 않는 디스크의 경우 먼저 저장소 옵션 간에 전환할 수 있도록 [관리디스크로 변환합니다.](convert-unmanaged-to-managed-disks.md)


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>프리미엄과 표준 간에 VM의 모든 관리 디스크 전환

이 예제에서는 VM의 모든 디스크를 표준 저장소에서 프리미엄 저장소또는 프리미엄 저장소에서 표준 저장소로 변환하는 방법을 보여 주며 있습니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기를](sizes.md) 사용해야 합니다. 이 예제는 프리미엄 저장소를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>표준 디스크와 프리미엄 간에 개별 관리 디스크 전환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 디스크와 Premium 디스크를 혼합하여 사용할 수 있습니다. 더 나은 성능이 필요한 디스크만 업그레이드하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준 저장소에서 프리미엄 저장소또는 프리미엄 저장소에서 표준 저장소로 변환하는 방법을 보여 주십습니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기를](sizes.md) 사용해야 합니다. 이 예제는 프리미엄 저장소를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>표준 HDD와 표준 SSD 간에 관리 디스크 전환

이 예제에서는 단일 VM 디스크를 표준 HDD에서 표준 SSD또는 표준 SSD에서 표준 HDD로 변환하는 방법을 보여 주며 있습니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Azure 포털에서 표준 및 프리미엄 간에 관리되는 디스크 전환

다음 단계를 수행하세요.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. **가상 컴퓨터**목록에서 VM을 선택합니다.
3. VM이 중지되지 않으면 VM **개요** 창 상단에서 **중지를** 선택하고 VM이 멈출 때까지 기다립니다.
4. VM창에서 메뉴에서 **디스크를 선택합니다.**
5. 변환할 디스크를 선택합니다.
6. 메뉴에서 **구성을** 선택합니다.
7. 계정 유형을 **표준 HDD에서** 프리미엄 **SSD로** 변경하거나 **프리미엄 SSD에서** 표준 **HDD로** **변경합니다.**
8. **저장을**선택하고 디스크 창을 닫습니다.

디스크 유형의 업데이트는 즉시 수행됩니다. 변환 후 VM을 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[스냅숏을](snapshot-copy-managed-disk.md)사용하여 VM의 읽기 전용 복사본을 만듭니다.

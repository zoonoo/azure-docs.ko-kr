---
title: 가상 머신 확장 집합 관리를 위한 Azure CLI 샘플
description: 이 샘플은 가상 머신 확장 집합에 디스크를 추가하는 방법을 보여 줍니다. 디스크를 업그레이드하고 Azure AD 인증에 가상 머신을 추가할 수 있습니다.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 493f479a09fc7b21bb857ebd98c35824c548b5d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99821960"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기 및 관리

다음 샘플 명령을 사용하여 Azure CLI를 통해 가상 머신 확장 집합을 프로토타입합니다.

이러한 샘플 명령은 다음 작업을 보여 줍니다.

* 가상 머신 확장 집합을 만듭니다.
* 새 또는 기존 디스크를 추가하고 확장 집합 또는 집합의 인스턴스로 업그레이드합니다.
* Azure AD(Azure Active Directory) 인증에 확장 집합을 추가합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>샘플 명령

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

새 데이터 디스크를 추가한 후 디스크를 포맷하고 탑재합니다. Windows 가상 머신의 경우 [Azure Portal을 사용하여 Windows VM에 관리되는 데이터 디스크 연결](../../virtual-machines/windows/attach-managed-disk-portal.md)을 참조하세요. Linux 가상 머신의 경우 [Linux VM에 디스크 추가](../../virtual-machines/linux/add-disk.md)를 참조하세요.

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

확장된 디스크를 사용하려면 기본 파티션을 확장합니다. 자세한 내용은 [디스크 파티션 및 파일 시스템 확장](/azure/virtual-machines/linux/expand-disks#expand-a-disk-partition-and-filesystem)을 참조하세요.

이 예제에서는 데이터 디스크의 크기를 조정합니다. 이와 동일한 절차를 사용하여 OS 디스크를 업데이트할 수 있습니다. Windows 가상 머신에 대한 자세한 내용은 [가상 머신의 OS 드라이브 확장 방법](../../virtual-machines/windows/expand-os-disk.md)을 참조하세요. Linux 가상 머신에 대한 자세한 내용은 [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](../../virtual-machines/linux/expand-disks.md)을 참조하세요.

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>리소스 정리

이러한 명령을 사용한 후 다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>이 문서에서 사용되는 Azure CLI 참조

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)

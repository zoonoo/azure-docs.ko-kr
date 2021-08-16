---
title: Azure Managed Disks의 성능 변경 - CLI/PowerShell
description: Azure PowerShell 모듈 또는 Azure CLI를 사용하여 기존 관리 디스크의 성능 계층을 변경하는 방법을 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/13/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 32f589be39740d62ef77967867522688d29a3618
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110673502"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Azure PowerShell 모듈 또는 Azure CLI를 사용하여 성능 계층 변경

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>기준 계층보다 높은 계층으로 빈 데이터 디스크 만들기

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace 이미지에서 기준 계층보다 높은 계층으로 OS 디스크 만들기

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>디스크 계층 업데이트

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>디스크 계층 표시

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>가동 중지 시간 없이 디스크의 성능 계층 변경(미리 보기)

가동 중지 시간 없이 성능 계층을 변경할 수도 있으므로 VM 할당을 취소하거나 디스크를 분리하여 계층을 변경할 필요가 없습니다.

### <a name="prerequisites"></a>필수 조건

디스크는 [가동 중지 시간 없이 성능 등급 변경(미리 보기)](#change-performance-tier-without-downtime-preview) 섹션에 나온 요구 사항을 충족해야 합니다. 충족하지 않는 경우 성능 등급을 변경하면 가동 중지 시간이 발생합니다.

가동 중지 시간 없이 디스크의 성능 계층을 변경하려면 먼저 구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 수행하여 구독에 대한 기능을 사용하도록 설정하세요.

1.  다음 명령을 실행하여 구독에 대한 기능 등록

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  기능을 사용해 보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 소요될 수 있음)인지 확인합니다.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="update-the-performance-tier-of-a-disk-without-downtime-via-azure-cli"></a>Azure CLI를 통해 가동 중지 시간 없이 디스크의 성능 계층 업데이트

다음 스크립트는 기준 계층보다 높은 디스크 계층을 업데이트합니다. `<yourResourceGroupNameHere>`, `<yourDiskNameHere>` 및 `<yourDesiredPerformanceTier>`를 바꾼 다음, 다음 스크립트를 실행합니다.

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
 
az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

### <a name="update-the-performance-tier-of-a-disk-without-downtime-via-arm-template"></a>ARM 템플릿을 통해 가동 중지 시간 없이 디스크의 성능 계층 업데이트

다음 스크립트는 샘플 템플릿 [CreateUpdateDataDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)을 사용하여 기준 계층보다 높은 디스크 계층을 업데이트합니다. `<yourSubScriptionID>`, `<yourResourceGroupName>`, `<yourDiskName>`, `<yourDiskSize>` 및 `<yourDesiredPerformanceTier>`를 바꾼 후 다음 스크립트를 실행합니다.

 ```cli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
diskName=<yourDiskName>
diskSize=<yourDiskSize>
performanceTier=<yourDesiredPerformanceTier>
region=EastUS2EUAP

 az login

 az account set --subscription $subscriptionId

 az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
--parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
```

## <a name="confirm-your-disk-has-changed-tiers"></a>디스크가 계층을 변경했는지 확인

성능 계층 변경을 완료하는 데 최대 15분이 걸릴 수 있습니다. 디스크가 계층을 변경했는지 확인하려면 다음 방법 중 하나를 사용합니다.

### <a name="azure-resource-manager"></a>Azure Resource Manager

```cli
az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-12-01 --query [properties.tier] -o tsv
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>다음 단계

더 높은 성능 계층을 활용하기 위해 디스크 크기를 조정해야 하는 경우 다음 문서를 참조하세요.

- [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](linux/expand-disks.md)
- [Windows 가상 머신에 연결된 관리 디스크 확장](windows/expand-os-disk.md)

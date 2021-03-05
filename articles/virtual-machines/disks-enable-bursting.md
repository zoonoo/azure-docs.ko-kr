---
title: 주문형 디스크 버스트 사용
description: 관리 디스크에서 주문형 디스크 버스트를 사용 하도록 설정 합니다.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f5865646200a783e7139bb5e22576ea404f58203
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216652"
---
# <a name="enable-on-demand-bursting"></a>주문형 버스트 사용

프리미엄 SSD (반도체 드라이브)에는 사용 가능한 두 개의 버스트 모델이 있습니다. 신용 기반 버스트 및 주문형 버스트 이 문서에서는 주문형 버스트로 전환 하는 방법을 설명 합니다. 요청 시 모델을 사용 하는 디스크는 원래 프로 비전 된 대상 이상으로 버스트 될 수 있습니다. 주문형 버스트는 최대 버스트 대상까지 워크 로드에 필요한 만큼 자주 발생 합니다. 주문형 버스트에는 추가 요금이 발생 합니다.

디스크 버스트에 대 한 자세한 내용은 [관리 디스크 버스트](disk-bursting.md)를 참조 하세요.

> [!IMPORTANT]
> 이 문서의 단계에 따라 신용 기반 버스트를 사용할 필요가 없습니다. 기본적으로 신용 기반 버스트는 모든 적격 디스크에서 사용 하도록 설정 됩니다.

주문형 버스트를 사용 하도록 설정 하기 전에 다음 사항을 이해 해야 합니다.

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>시작하기

주문형 버스트는 Azure PowerShell 모듈, Azure CLI 또는 Azure Resource Manager 템플릿 중 하나를 사용 하 여 사용 하도록 설정할 수 있습니다. 다음 예에서는 요청 시 버스트를 사용 하 고 기존 디스크에서 주문형 버스트를 사용 하도록 설정 하 여 새 디스크를 만드는 방법을 설명 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

주문형 버스트 cmdlet은 Az module 버전 5.5.0 이상에서 사용할 수 있습니다. 또는 [Azure Cloud Shell](https://shell.azure.com/)를 사용할 수 있습니다.
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>주문형 버스트를 사용 하 여 빈 데이터 디스크 만들기

주문형 버스트를 사용 하려면 관리 되는 디스크가 512 GiB 커야 합니다. `<myResourceGroupDisk>`및 `<myDataDisk>` 매개 변수를 바꾼 후 다음 스크립트를 실행 하 여 요청 시 버스트로 프리미엄 SSD를 만듭니다.

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>기존 디스크에서 주문형 버스트 사용

주문형 버스트를 사용 하려면 관리 되는 디스크가 512 GiB 커야 합니다. `<myResourceGroupDisk>`, `<myDataDisk>` 매개 변수를 바꾸고이 명령을 실행 하 여 기존 디스크에서 요청 시 버스트를 사용 하도록 설정 합니다.

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

주문형 버스트 cmdlet은 2.19.0 이상 버전의 [Azure CLI 모듈](https://docs.microsoft.com/cli/azure/install-azure-cli)에서 사용할 수 있습니다. 또는 [Azure Cloud Shell](https://shell.azure.com/)를 사용할 수 있습니다.

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>주문형 버스트 데이터 디스크 만들기 및 연결

주문형 버스트를 사용 하려면 관리 되는 디스크가 512 GiB 커야 합니다. `<yourDiskName>`, `<yourResourceGroup>` 및 `<yourVMName>` 매개 변수를 바꾼 후 다음 명령을 실행 하 여 요청 시 버스트로 프리미엄 SSD를 만듭니다.

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>기존 디스크에서 주문형 버스트 사용-CLI

주문형 버스트를 사용 하려면 관리 되는 디스크가 512 GiB 커야 합니다. `<myResourceGroupDisk>`및 `<yourDiskName>` 매개 변수를 바꾸고이 명령을 실행 하 여 기존 디스크에서 요청 시 버스트를 사용 하도록 설정 합니다.

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

`2020-09-30`디스크 API를 사용 하 여 새로 만들었거나 기존 프리미엄 ssd를 512 GiB 큼 이상에서 주문형 버스트를 사용 하도록 설정할 수 있습니다. API에는 `2020-09-30` 새 속성인가 도입 `burstingEnabled` 되었습니다. 기본적으로이 속성은 false로 설정 됩니다. 다음 샘플 템플릿은 디스크 버스트를 사용 하 여 미국 서 부에 1TiB premium SSD를 만듭니다.

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>다음 단계

버스트 리소스를 파악 하는 방법을 알아보려면 [디스크 버스트 메트릭](disks-metrics.md)을 참조 하세요.
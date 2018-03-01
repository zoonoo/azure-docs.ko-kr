---
title: "Azure에서 VHD의 스냅숏 만들기 | Microsoft Docs"
description: "백업 또는 문제 해결을 위해 Azure에서 VHD의 복사본을 만드는 방법을 알아봅니다."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>스냅숏 만들기 

백업 또는 VM 문제 해결을 위해 OS 또는 데이터 디스크 VHD의 스냅숏을 만듭니다. 스냅숏은 VHD의 전체 읽기 전용 복사본입니다. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Azure CLI 2.0을 사용하여 스냅숏 만들기

다음 예제는 Azure CLI 2.0을 설치하고 Azure 계정에 로그인해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

다음 단계에서는 `az snapshot create` 명령과 `--source-disk` 매개 변수를 사용하여 스냅숏을 만드는 방법을 보여줍니다. 다음 예제는 `myResourceGroup` 리소스 그룹의 관리 OS 디스크로 만든 `myVM`이라는 VM이 있는 것으로 가정합니다.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

다음과 비슷하게 출력됩니다.

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

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

스냅숏을 사용하여 관리 디스크를 만들고 고성능이 필요한 VM에 스냅숏을 연결하려는 경우 `--sku Premium_LRS` 매개 변수와 `az snapshot create` 명령을 사용합니다. 그러면 프리미엄 관리 디스크로 저장되는 스냅숏이 만들어집니다. 프리미엄 Managed Disks는 SSD(반도체 드라이브)이기 때문에 성능이 우수하지만 표준 디스크(HDD)보다 가격이 비쌉니다.


## <a name="next-steps"></a>다음 단계

 스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 머신을 만듭니다. 자세한 내용은 [스냅숏에서 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) 스크립트를 참조하세요.


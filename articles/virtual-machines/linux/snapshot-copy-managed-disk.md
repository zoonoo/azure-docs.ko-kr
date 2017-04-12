---
title: "백업용 Azure 관리 디스크 복사 | Microsoft Docs"
description: "백업 또는 디스크 문제 해결에 사용할 수 있는 Azure 관리 디스크의 복사본을 만드는 방법을 알아봅니다."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 22013ec8e5531a2f61d811300bce016fcde5ab86
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>관리 스냅숏을 사용하여 Azure 관리 디스크로 저장된 VHD 복사본 만들기
백업용 관리 디스크의 스냅숏을 만들거나 스냅숏으로 관리 디스크를 만들고 테스트 가상 컴퓨터에 연결하여 문제를 해결합니다. 관리 스냅숏은 VM 관리 디스크의 완전한 특정 시점 복사본입니다. VHD의 읽기 전용 복사본을 만들어서 기본적으로 표준 관리 디스크로 저장합니다. 

가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. <!--Add link to topic or blog post that explains managed disks. -->

Azure Portal 또는 Azure CLI 2.0을 사용하여 Managed Disk 스냅숏을 만듭니다.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Azure CLI 2.0을 사용하여 스냅숏 만들기

> [!NOTE] 
> 다음 예제는 Azure CLI 2.0을 설치하고 Azure 계정에 로그인해야 합니다.

다음 단계에서는 `az snapshot create` 명령과 `--source-disk` 매개 변수를 사용하여 관리 OS 디스크의 스냅숏을 가져오고 만드는 방법을 보여줍니다. 다음 예제는 `myResourceGroup` 리소스 그룹의 관리 OS 디스크로 만든 `myVM`이라는 VM이 있는 것으로 가정합니다.

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

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위에서 **새로 만들기**를 클릭하고 **스냅숏**을 검색합니다.
3. 스냅숏 블레이드에서 **만들기**를 클릭합니다.
4. 스냅숏의 **이름**을 입력합니다.
5. 기존 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
6. Azure 데이터 센터 위치를 선택합니다.  
7. **원본 디스크**에서 스냅숏을 만들 Managed Disk를 선택합니다.
8. 스냅숏 저장에 사용할 **계정 유형**을 선택합니다. 고성능 디스크에 저장할 필요가 없다면 **Standard_LRS**를 권장합니다.
9. **만들기**를 클릭합니다.

스냅숏을 사용하여 관리 디스크를 만들고 고성능이 필요한 VM에 스냅숏을 연결하려는 경우 `--sku Premium_LRS` 매개 변수와 `az snapshot create` 명령을 사용합니다. 그러면 프리미엄 관리 디스크로 저장되는 스냅숏이 만들어집니다. 프리미엄 Managed Disks는 SSD(반도체 드라이브)이기 때문에 성능이 우수하지만 표준 디스크(HDD)보다 가격이 비쌉니다.




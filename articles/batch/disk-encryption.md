---
title: 디스크 암호화가 사용된 풀 만들기
description: 디스크 암호화 구성을 사용하여 플랫폼 관리형 키로 노드를 암호화하는 방법을 알아봅니다.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d332a6e48e288988599adbfb6e173676ae8d3a3
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891809"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>디스크 암호화가 사용된 풀 만들기

[가상 머신 구성](nodes-and-pools.md#virtual-machine-configuration)을 사용하여 Azure Batch 풀을 만들 때 디스크 암호화 구성을 지정하여 플랫폼 관리형 키를 통해 풀의 컴퓨팅 노드를 암호화할 수 있습니다.

이 문서에서는 디스크 암호화가 사용된 풀을 만드는 방법에 대해 설명합니다.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>디스크 암호화 구성이 있는 풀을 사용하는 이유는 무엇인가요?

Batch 풀을 사용하면 컴퓨팅 노드의 OS 및 임시 디스크에 있는 데이터에 액세스하고 데이터를 저장할 수 있습니다. 플랫폼 관리형 키로 서버 측 디스크를 암호화하면 낮은 오버헤드와 편리함으로 이 데이터를 보호할 수 있습니다.

Batch는 풀 구성 및 지역 지원 가능성에 따라 컴퓨팅 노드에 이러한 디스크 암호화 기술 중 하나를 적용합니다.

- [플랫폼 관리형 키를 사용한 미사용 관리 디스크 암호화](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [플랫폼 관리형 키를 사용하여 호스트에서 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 디스크 암호화](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

풀의 노드에 적용할 암호화 방법을 지정할 수 없습니다. 대신 해당 노드에서 암호화할 대상 디스크를 제공하면 Batch가 적절한 암호화 방법을 선택하여 지정된 디스크가 컴퓨팅 노드에서 암호화되도록 할 수 있습니다.

> [!IMPORTANT]
> [사용자 지정 이미지](batch-sig-images.md)로 풀을 만드는 경우 Windows VM을 사용하는 경우에만 디스크 암호화를 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal에서 Batch 풀을 만들 때 **디스크 암호화 구성** 에서 **TemporaryDisk** 또는 **OsAndTemporaryDisk** 를 선택합니다.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Azure Portal의 디스크 암호화 구성 옵션의 스크린샷":::

풀이 생성되면 풀의 **속성** 섹션에서 디스크 암호화 구성 대상을 볼 수 있습니다.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Azure Portal의 디스크 암호화 구성 대상을 보여 주는 스크린샷.":::

## <a name="examples"></a>예제

다음 예에서는 Batch .NET SDK, Batch REST API 및 Azure CLI를 사용하여 Batch 풀에서 OS 및 임시 디스크를 암호화하는 방법을 보여 줍니다.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

본문 요청:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Storage의 서버 측 암호화](../virtual-machines/disk-encryption.md)에 대해 자세히 알아봅니다.
- Batch에 대한 자세한 개요는 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.

---
title: 공유 이미지 갤러리를 사용 하 여 풀을 만듭니다.-Azure Batch | Microsoft Docs
description: 응용 프로그램에 필요한 소프트웨어와 데이터를 포함 하는 계산 노드에 사용자 지정 이미지를 프로 비전 하기 위해 공유 이미지 갤러리를 사용 하 여 Batch 풀을 만듭니다. 사용자 지정 이미지는 Batch 워크로드를 실행하도록 컴퓨팅 노드를 구성하는 효율적인 방법입니다.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036704"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>공유 이미지 갤러리를 사용 하 여 풀 만들기

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 컴퓨팅 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. 지원 되는 Azure Marketplace 이미지를 사용 하 여 가상 머신 풀을 만들거나 [공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md)를 사용 하 여 사용자 지정 이미지를 만들 수 있습니다.

## <a name="benefits-of-the-shared-image-gallery"></a>공유 이미지 갤러리의 이점

사용자 지정 이미지에 대 한 공유 이미지 갤러리를 사용 하는 경우 운영 체제 유형과 구성 뿐만 아니라 데이터 디스크의 유형을 제어할 수 있습니다. 공유 이미지는 프로 비전 되는 즉시 모든 Batch 풀 노드에서 사용할 수 있게 되는 응용 프로그램 및 참조 데이터를 포함할 수 있습니다.

또한 사용자 환경에 맞게 여러 버전의 이미지를 사용할 수 있습니다. 이미지 버전을 사용 하 여 VM을 만드는 경우 이미지 버전은 VM에 대 한 새 디스크를 만드는 데 사용 됩니다. 

공유 이미지를 사용 하면 풀의 계산 노드를 준비 하 여 Batch 워크 로드를 실행 하는 데 걸리는 시간을 절약할 수 있습니다. 프로 비전 후에는 Azure Marketplace 이미지를 사용 하 고 각 계산 노드에 소프트웨어를 설치할 수 있지만 일반적으로 공유 이미지를 사용 하는 것이 더 효율적입니다. 또한 여러 Vm (600 개 이상)을 사용 하 여 풀을 만들 때 풀을 만들 때 시간을 절약 하기 위해 공유 이미지에 대해 여러 복제본을 지정할 수 있습니다.

시나리오에 대해 구성 된 공유 이미지를 사용 하면 다음과 같은 여러 가지 이점을 얻을 수 있습니다.

* **지역에서 동일한 이미지를 사용 합니다.** 모든 풀에서 동일한 이미지를 사용 하도록 여러 지역에 걸쳐 공유 이미지 복제본을 만들 수 있습니다.
* **OS (운영 체제)를 구성 합니다.** 이미지의 운영 체제 디스크 구성을 사용자 지정할 수 있습니다.
* **애플리케이션 미리 설치**. 시작 태스크를 사용 하 여 계산 노드를 프로 비전 한 후 응용 프로그램을 설치 하는 것 보다 더 효율적이 고 오류가 발생 하기 쉬우므로 OS 디스크에 응용 프로그램을 사전 설치할 수 있습니다.
* **많은 양의 데이터를 한 번 복사 합니다.** 관리 되는 공유 이미지를 관리 되는 이미지의 데이터 디스크로 복사 하 여 정적 데이터를 관리 되는 공유 이미지의 일부로 만듭니다. 이 작업은 한 번만 수행하며 풀의 각 노드에서 데이터를 사용할 수 있게 됩니다.
* **풀을 더 큰 크기로 확장 합니다.** 공유 이미지 갤러리를 사용 하면 더 많은 공유 이미지 복제본과 함께 사용자 지정 된 이미지를 사용 하 여 더 큰 풀을 만들 수 있습니다.
* **사용자 지정 이미지 보다 성능이 우수 합니다.** 공유 이미지를 사용 하는 경우 풀이 안정 된 상태에 도달 하는 데 걸리는 시간은 최대 25% 더 빠르며 VM 유휴 대기 시간은 최대 30% 더 짧습니다.
* **보다 쉽게 관리할 수 있도록 이미지 버전 관리 및 그룹화** 이미지 그룹화 정의에는 이미지를 만든 이유에 대 한 정보, 사용 중인 OS 및 이미지 사용에 대 한 정보가 포함 되어 있습니다. 이미지를 그룹화 하면 이미지를 쉽게 관리할 수 있습니다. 자세한 내용은 [이미지 정의](../virtual-machines/windows/shared-image-galleries.md#image-definitions)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* **Azure Batch 계정.** Batch 계정을 만들려면 [Azure Portal](quick-create-portal.md) 또는 [Azure CLI](quick-create-cli.md)를 사용 하 여 일괄 처리 퀵 스타트를 참조 하세요.

* **공유 이미지 갤러리 이미지**입니다. 공유 이미지를 준비 하는 방법에 대 한 자세한 내용 및 단계는 [Azure CLI 사용 하 여 공유 이미지 갤러리 만들기](../virtual-machines/linux/shared-images.md) 또는 [Azure Portal를 사용 하 여 공유 이미지 갤러리 만들기](../virtual-machines/linux/shared-images-portal.md)를 참조 하세요.

> [!NOTE]
> 공유 이미지는 Batch 계정과 동일한 구독에 있어야 합니다. 공유 이미지는 Batch 계정과 동일한 지역에 복제본이 있는 경우 다른 지역에 있을 수 있습니다.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI를 사용 하 여 공유 이미지에서 풀 만들기

Azure CLI를 사용 하 여 공유 이미지에서 풀을 만들려면 `az batch pool create` 명령을 사용 합니다. `--image` 필드에 공유 이미지 ID를 지정 합니다. OS 유형 및 SKU가에 지정 된 버전과 일치 하는지 확인 합니다.`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>을 사용 하 여 공유 이미지에서 풀 만들기C#

또는 C# SDK를 사용 하 여 공유 이미지에서 풀을 만들 수 있습니다.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

공유 이미지를 사용 하 여 수백 또는 수천 개의 Vm이 포함 된 풀을 만들려는 경우 다음 지침을 따르십시오.

* **공유 이미지 갤러리 복제본 번호입니다.**  최대 600 인스턴스를 포함 하는 모든 풀에 대해 하나 이상의 복제본을 유지 하는 것이 좋습니다. 예를 들어 3000 Vm을 사용 하 여 풀을 만드는 경우 이미지의 복제본을 5 개 이상 유지 해야 합니다. 성능 향상을 위해 항상 최소 요구 사항 보다 더 많은 복제본을 유지 하는 것이 좋습니다.

* **크기 조정 시간 제한** 풀이 고정 된 수의 노드를 포함 하는 경우 (자동 크기 조정 하지 않는 경우 `resizeTimeout` ) 풀 크기에 따라 풀의 속성을 늘립니다. 1000 Vm 마다 권장 크기 조정 시간 제한이 15 분 이상입니다. 예를 들어 2000 Vm이 있는 풀에 권장 되는 크기 조정 제한 시간은 30 분 이상입니다.

## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 컴퓨팅 솔루션 개발](batch-api-basics.md)을 참조하세요.

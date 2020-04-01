---
title: 공유 이미지 갤러리를 사용하여 사용자 지정 풀 만들기 - Azure Batch | 마이크로 소프트 문서
description: 공유 이미지 갤러리를 사용하여 일괄 처리 풀을 만들어 사용자 지정 이미지를 프로비전하여 응용 프로그램에 필요한 소프트웨어 및 데이터가 포함된 노드를 계산합니다. 사용자 지정 이미지는 Batch 워크로드를 실행하도록 컴퓨팅 노드를 구성하는 효율적인 방법입니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 95f27d913cd288c186bae1a6375212b072f50bb4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422429"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>공유 이미지 갤러리를 사용하여 사용자 지정 풀 만들기

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 컴퓨팅 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. 지원되는 Azure Marketplace 이미지로 가상 컴퓨터 풀을 만들거나 공유 [이미지 갤러리를](../virtual-machines/windows/shared-image-galleries.md)사용하여 사용자 지정 이미지를 만들 수 있습니다.

## <a name="benefits-of-the-shared-image-gallery"></a>공유 이미지 갤러리의 이점

사용자 지정 이미지에 공유 이미지 갤러리를 사용하면 운영 체제 유형 및 구성과 데이터 디스크 유형을 제어할 수 있습니다. 공유 이미지에는 프로비전되는 즉시 모든 Batch 풀 노드에서 사용할 수 있는 응용 프로그램 및 참조 데이터가 포함될 수 있습니다.

환경에 필요에 따라 여러 버전의 이미지를 가질 수도 있습니다. 이미지 버전을 사용하여 VM을 만들 면 이미지 버전이 VM에 대한 새 디스크를 만드는 데 사용됩니다.

공유 이미지를 사용하면 일괄 처리 워크로드를 실행하기 위해 풀의 계산 노드를 준비하는 데 시간을 절약할 수 있습니다. 프로비저닝 후 Azure Marketplace 이미지를 사용하고 각 계산 노드에 소프트웨어를 설치할 수 있지만 일반적으로 공유 이미지를 사용하는 것이 더 효율적입니다. 또한 공유 이미지에 대해 여러 복제본을 지정할 수 있으므로 VM(VM이 600개 이상)이 많은 풀을 만들 때 풀 생성 시간을 절약할 수 있습니다.

시나리오에 맞게 구성된 공유 이미지를 사용하면 다음과 같은 몇 가지 이점을 얻을 수 있습니다.

* **리전 전체에서 동일한 이미지를 사용합니다.** 모든 풀이 동일한 이미지를 사용할 수 있도록 여러 지역에 걸쳐 공유 이미지 복제본을 만들 수 있습니다.
* **운영 체제(OS)를 구성합니다.** 이미지의 운영 체제 디스크 구성을 사용자 지정할 수 있습니다.
* **애플리케이션 미리 설치**. OS 디스크에 응용 프로그램을 사전 설치하는 것은 시작 작업으로 계산 노드를 프로비전한 후 응용 프로그램을 설치하는 것보다 효율적이고 오류가 발생하기 쉽습니다.
* **많은 양의 데이터를 한 번 복사합니다.** 관리되는 이미지의 데이터 디스크에 복사하여 관리되는 공유 이미지의 정적 데이터 부분을 만듭니다. 이 작업은 한 번만 수행하며 풀의 각 노드에서 데이터를 사용할 수 있게 됩니다.
* **풀을 더 큰 크기로 늘리는 것입니다.** 공유 이미지 갤러리를 사용하면 더 많은 공유 이미지 복제본과 함께 사용자 지정된 이미지로 더 큰 풀을 만들 수 있습니다.
* **사용자 지정 이미지보다 성능이 우수합니다.** 공유 이미지를 사용하면 풀이 정상 상태에 도달하는 데 걸리는 시간이 최대 25% 빨라지고 VM 유휴 대기 시간이 최대 30% 짧습니다.
* **보다 쉬운 관리를 위한 이미지 버전 관리 및 그룹화.** 이미지 그룹화 정의에는 이미지가 만들어진 이유, 사용 하는 OS 및 이미지 사용에 대 한 정보가 포함 되어 있습니다. 이미지를 그룹화하면 이미지 관리가 더 쉬워집니다. 자세한 내용은 [이미지 정의를](../virtual-machines/windows/shared-image-galleries.md#image-definitions)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> Azure AD를 사용하여 인증해야 합니다. 공유 키 인증을 사용하는 경우 인증 오류가 발생합니다.  

* **Azure Batch 계정.** 일괄 처리 계정을 만들려면 [Azure 포털](quick-create-portal.md) 또는 [Azure CLI를](quick-create-cli.md)사용하여 일괄 처리 빠른 시작을 참조하세요.

* **공유 이미지 갤러리 이미지**. 공유 이미지를 만들려면 관리되는 이미지 리소스가 있거나 만들어야 합니다. VM OS 디스크 및 연결된 데이터 디스크(선택 사항)의 스냅샷에서 이미지를 만들어야 합니다. 자세한 내용은 [관리되는 이미지 준비를](#prepare-a-managed-image)참조하십시오.

> [!NOTE]
> 공유 이미지는 Batch 계정과 동일한 구독에 있어야 합니다. 공유 이미지는 Batch 계정과 동일한 지역에 복제본이 있는 한 다른 지역에 있을 수 있습니다.

## <a name="prepare-a-managed-image"></a>관리되는 이미지 준비

Azure에서 다음 에서 관리되는 이미지를 준비할 수 있습니다.

* Azure VM의 OS 및 데이터 디스크의 스냅숏
* 관리 디스크가 있는 일반화된 Azure VM
* 클라우드에 업로드된 일반화된 온-프레미스 VHD

사용자 지정 이미지를 사용하여 안정적으로 Batch 풀 크기를 조정하려는 경우 VM 디스크의 스냅샷을 사용하는 *첫 번째 방법만* 사용하여 관리되는 이미지를 만드는 것이 좋습니다. VM을 준비하고 스냅샷을 생성하고 스냅샷에서 이미지를 만들려면 다음 단계를 참조하세요.

### <a name="prepare-a-vm"></a>VM 준비

이미지에 대한 새 VM을 만드는 경우 Batch에서 지원하는 자사 Azure Marketplace 이미지를 관리되는 이미지의 기본 이미지로 사용합니다. 자파티 이미지만 기본 이미지로 사용할 수 있습니다. Azure Batch에서 지원하는 Azure Marketplace 이미지 참조의 전체 목록을 얻으려면 [목록 노드 에이전트 SCO](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 작업을 참조하십시오.

> [!NOTE]
> 추가 라이선스 및 구매 약관이 있는 타사 이미지는 기본 이미지로 사용할 수 없습니다. Marketplace 이미지에 대한 자세한 내용은 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) 또는 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM을 참조하세요.

* 관리 디스크로 VM이 만들어지도록 합니다. VM을 만들 때 기본 스토리지 설정입니다.
* 사용자 지정 스크립트 확장 등의 Azure 확장을 VM에 설치해서는 안 됩니다. 이미지에 미리 설치된 확장이포함되어 있으면 Batch 풀을 배포할 때 Azure에서 문제가 발생할 수 있습니다.
* 연결된 데이터 디스크를 사용하는 경우 VM 내에서 디스크를 마운트하고 포맷하여 사용해야 합니다.
* 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
* VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사합니다.  

### <a name="create-a-vm-snapshot"></a>VM 스냅샷 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM OS 또는 데이터 디스크의 스냅샷을 만들려는 경우 Azure Portal 또는 명령줄 도구를 사용할 수 있습니다. 스냅샷을 만드는 단계와 옵션은 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 또는 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM용 지침을 참조하세요.

### <a name="create-an-image-from-one-or-more-snapshots"></a>스냅샷 하나 이상에서 이미지 만들기

스냅샷에서 관리되는 이미지를 만들려면 [az image create](/cli/azure/image) 명령과 같은 Azure 명령줄 도구를 사용합니다. OS 디스크 스냅샷과 선택적으로 하나 이상의 데이터 디스크 스냅숏을 지정하여 이미지를 만듭니다.

### <a name="create-a-shared-image-gallery"></a>Shared Image Gallery 만들기

관리되는 이미지를 성공적으로 만든 후에는 공유 이미지 갤러리를 만들어 사용자 지정 이미지를 사용할 수 있도록 해야 합니다. 이미지에 대한 공유 이미지 갤러리를 만드는 방법을 알아보려면 [Azure CLI를 사용하여 공유 이미지 갤러리 만들기](../virtual-machines/linux/shared-images.md) 또는 Azure [포털을 사용하여 공유 이미지 갤러리 만들기를](../virtual-machines/linux/shared-images-portal.md)참조하세요.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI를 사용하여 공유 이미지에서 풀 만들기

Azure CLI를 사용하여 공유 이미지에서 풀을 `az batch pool create` 만들려면 명령을 사용합니다. 필드에서 공유 이미지 ID를 지정합니다. `--image` OS 유형과 SKU가 지정된 버전과 일치하는지 확인합니다.`--node-agent-sku-id`

> [!NOTE]
> Azure AD를 사용하여 인증해야 합니다. 공유 키 인증을 사용하는 경우 인증 오류가 발생합니다.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>C를 사용하여 공유 이미지에서 풀 만들기 #

또는 C# SDK를 사용하여 공유 이미지에서 풀을 만들 수 있습니다.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Azure 포털을 사용하여 공유 이미지에서 풀 만들기

다음 단계를 사용하여 Azure 포털의 공유 이미지에서 풀을 만듭니다.

1. Azure [포털을](https://portal.azure.com)엽니다.
1. 일괄 **처리 계정으로** 이동하여 계정을 선택합니다.
1. **풀을** 선택한 다음 **추가를** 선택하여 새 풀을 만듭니다.
1. 이미지 **유형** 섹션에서 **공유 이미지 갤러리를**선택합니다.
1. 관리되는 이미지에 대한 정보를 통해 나머지 섹션을 완료합니다.
1. **확인**을 선택합니다.

![포털을 사용하여 공유 이미지에서 풀을 만듭니다.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

공유 이미지를 사용하여 수백 또는 수천 개 이상의 VM이 있는 풀을 만들려는 경우 다음 지침을 사용합니다.

* **공유 이미지 갤러리 복제 번호입니다.**  인스턴스가 최대 600개인 모든 풀에 대해 하나 이상의 복제본을 유지하는 것이 좋습니다. 예를 들어 VM이 3000개인 풀을 만드는 경우 이미지의 복제본을 5개 이상 유지해야 합니다. 성능 향상을 위해 최소 요구 사항보다 더 많은 복제본을 유지하는 것이 좋습니다.

* **시간 초과 크기를 조정합니다.** 풀에 고정된 수의 노드가 포함된 경우(자동 크기 조정이 없는 경우) 풀 크기에 따라 풀의 `resizeTimeout` 속성을 늘립니다. 1000VM마다 권장되는 크기 조정 시간 초과는 최소 15분입니다. 예를 들어 VM이 2000개인 풀에 권장되는 크기 조정 시간 초과는 30분 이상입니다.

## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 컴퓨팅 솔루션 개발](batch-api-basics.md)을 참조하세요.

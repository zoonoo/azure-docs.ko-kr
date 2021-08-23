---
title: 가용성 영역 전반에 풀 만들기
description: 영역 정책이 포함된 배치 풀을 만들어 오류로부터 보호하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: 0abec404b0ec951bb398cfb06ae41290e986a241
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566973"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>가용성 영역에서 Azure Batch 풀 만들기

[가용성 영역](https://azure.microsoft.com/global-infrastructure/availability-zones/)을 지원하는 Azure 지역에는 최소 3개의 개별 영역이 있으며, 각 영역에는 자체 독립 전원, 네트워크 및 냉각 시스템이 있습니다. 가상 머신 구성을 사용하여 Azure Batch 풀을 만드는 경우 가용성 영역에 전반에서 Batch 풀을 프로비전하도록 선택할 수 있습니다. 이 영역 정책을 사용하여 풀을 만들면 Azure 데이터 센터 수준 오류로부터 Batch 컴퓨팅 노드를 보호할 수 있습니다.

예를 들어 3 개의 가용성 영역을 지원하는 Azure 지역에서 영역 정책을 사용하여 풀을 만들 수 있습니다. 하나의 가용성 영역에 있는 Azure 데이터 센터에서 인프라 오류가 발생하는 경우 Batch 풀에는 다른 두 가용성 영역의 정상 노드가 남아 있으므로 작업 예약에 풀을 계속 사용할 수 있습니다.

## <a name="regional-support-and-other-requirements"></a>지역별 지원 및 기타 요구 사항

Batch는 가용성 영역 지원 시 Azure에서 패리티를 유지 관리합니다. 영역 옵션을 사용하려면 [지원되는 Azure 지역](../availability-zones/az-region.md)에 풀을 만들어야 합니다.

Batch 풀을 가용성 영역 전반에 할당하려면 풀이 생성되는 Azure 지역은 둘 이상의 영역에서 요청된 VM SKU를 지원해야 합니다. [리소스 SKU 목록 API](/rest/api/compute/resourceskus/list)를 호출하고 [resourceSku](/rest/api/compute/resourceskus/list#resourcesku)의 **locationinfo** 필드를 확인하여 이 유효성을 검사할 수 있습니다. 요청된 VM SKU에 대해 둘 이상의 영역이 지원되는지 확인합니다.

[사용자 구독 모드 Batch 계정](accounts.md#batch-accounts)에 대해 풀을 만드는 구독에 요청된 VM SKU에 대한 영역 제공 제한이 없는지 확인합니다. 이를 확인하려면 [리소스 SKU 목록 API](/rest/api/compute/resourceskus/list)를 호출하고 [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)를 확인합니다. 영역 제한이 있는 경우 [지원 티켓](/troubleshoot/azure/general/region-access-request-process)을 제출하여 영역 제한을 제거할 수 있습니다.

또한 노드 간 통신을 사용하도록 설정하고 [InfiniBand를 지원하는 VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md)를 사용하는 경우 영역 정책을 사용하여 풀을 만들 수 없습니다.

## <a name="create-a-batch-pool-across-availability-zones"></a>가용성 영역에 Batch 풀 만들기

다음 예에서는 가용성 영역에서 Batch 풀을 만드는 방법을 보여 줍니다.

> [!NOTE]
> 영역 정책을 사용하여 풀을 만들 때 Batch 서비스는 선택한 지역의 모든 가용성 영역에 풀을 할당하려고 합니다. 영역에서 특정 할당을 지정할 수는 없습니다.

### <a name="batch-management-client-net-sdk"></a>Batch 관리 클라이언트 .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

요청 본문

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure 가상 네트워크의 서브넷에서 풀을 만드는 방법](batch-virtual-network.md)을 알아봅니다.
- [공용 IP 주소가 없는 Azure Batch 풀을 만드는 방법](./batch-pool-no-public-ip-address.md)을 알아봅니다.

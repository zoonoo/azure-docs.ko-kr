---
title: 가용성 영역 간에 풀 만들기
description: 장애 로부터 보호 하는 데 도움이 되는 영역 정책을 사용 하 여 Batch 풀을 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056112"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>가용성 영역에서 Azure Batch 풀 만들기

[가용성 영역](https://azure.microsoft.com/global-infrastructure/availability-zones/) 를 지 원하는 Azure 지역에는 각각 독립적인 전원, 네트워크 및 냉각 시스템이 포함 된 세 개의 별도 영역이 있습니다. 가상 컴퓨터 구성을 사용 하 여 Azure Batch 풀을 만드는 경우 가용성 영역에서 Batch 풀을 프로 비전 하도록 선택할 수 있습니다. 이 영역 정책을 사용 하 여 풀을 만들면 Azure 데이터 센터 수준 오류 로부터 Batch 계산 노드를 보호 하는 데 도움이 됩니다.

예를 들어 3 개의 가용성 영역를 지 원하는 Azure 지역에서 영역 정책을 사용 하 여 풀을 만들 수 있습니다. 하나의 가용성 영역에 있는 Azure 데이터 센터에서 인프라 오류가 발생 하는 경우 Batch 풀에는 다른 두 가용성 영역의 정상 노드가 남아 있으므로 풀은 작업 예약에 계속 사용할 수 있습니다.

## <a name="regional-support-and-other-requirements"></a>국가별 지원 및 기타 요구 사항

Batch는 가용성 영역 지원 하기 위해 Azure에서 패리티를 유지 관리 합니다. 영역 옵션을 사용 하려면 [지원 되는 Azure 지역](../availability-zones/az-region.md)에 풀을 만들어야 합니다.

Batch 풀이 가용성 영역에 걸쳐 할당 되려면 풀이 생성 되는 Azure 지역은 둘 이상의 영역에서 요청 된 VM SKU를 지원 해야 합니다. [리소스 Sku 목록 API](/rest/api/compute/resourceskus/list) 를 호출 하 고 [ResourceSku](/rest/api/compute/resourceskus/list#resourcesku)의 **locationinfo** 필드를 확인 하 여이 유효성을 검사할 수 있습니다. 요청 된 VM SKU에 대해 둘 이상의 영역이 지원 되는지 확인해 두어야 합니다.

[사용자 구독 모드 Batch 계정](accounts.md#batch-accounts)에 대해 풀을 만드는 구독에 요청 된 VM SKU에 대 한 영역 제공 제한이 있는지 확인 합니다. 이를 확인 하려면 [리소스 Sku 목록 API](/rest/api/compute/resourceskus/list) 를 호출 하 고 [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)를 확인 합니다. 영역 제한이 있는 경우 [지원 티켓](../azure-portal/supportability/sku-series-unavailable.md) 을 제출 하 여 영역 제한을 제거할 수 있습니다.

또한 노드 간 통신을 사용 하도록 설정 하 고 [InfiniBand를 지 원하는 VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md)를 사용 하는 경우 영역 정책을 사용 하 여 풀을 만들 수 없습니다.

## <a name="create-a-batch-pool-across-availability-zones"></a>가용성 영역에서 Batch 풀 만들기

다음 예에서는 가용성 영역에서 Batch 풀을 만드는 방법을 보여 줍니다.

> [!NOTE]
> 영역 정책을 사용 하 여 풀을 만들 때 Batch 서비스는 선택한 지역의 모든 가용성 영역에서 풀을 할당 하려고 합니다. 영역에서 특정 할당을 지정할 수 없습니다.

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
- [Azure 가상 네트워크의 서브넷에서 풀을 만드는](batch-virtual-network.md)방법에 대해 알아봅니다.
- [공용 IP 주소가 없는 Azure Batch 풀을 만드는](./batch-pool-no-public-ip-address.md)방법에 대해 알아봅니다.


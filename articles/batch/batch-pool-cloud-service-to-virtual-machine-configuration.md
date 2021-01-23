---
title: Cloud Services에서 Virtual Machines 배치 풀 구성 마이그레이션
description: 풀 구성을 최신 및 권장 구성으로 업데이트 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: 417738be2c69101129079b8ff3a3d80634f9f99c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731502"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Cloud Services에서 Virtual Machines 배치 풀 구성 마이그레이션

[CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) 또는 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)중 하나를 사용 하 여 Batch 풀을 만들 수 있습니다. 모든 일괄 처리 기능을 지원 하므로 ' virtualMachineConfiguration '은 권장 되는 구성입니다. ' cloudServiceConfiguration ' 풀은 모든 기능을 지원 하지 않으며 새로운 기능이 계획 되지 않았습니다.

' CloudServiceConfiguration ' 풀을 사용 하는 경우 ' virtualMachineConfiguration ' 풀을 사용 하도록 이동 하는 것이 좋습니다. 이렇게 하면 확장 된 [VM 시리즈](batch-pool-vm-sizes.md), Linux vm, [컨테이너](batch-docker-container-workloads.md), [Azure Resource Manager 가상 네트워크](batch-virtual-network.md)및 [노드 디스크 암호화](disk-encryption.md)와 같은 모든 배치 기능을 활용할 수 있습니다.

이 문서에서는 ' virtualMachineConfiguration '로 마이그레이션하는 방법을 설명 합니다.

## <a name="new-pools-are-required"></a>새 풀이 필요 합니다.

기존 활성 풀을 ' cloudServiceConfiguration '에서 ' virtualMachineConfiguration ' (으)로 업데이트할 수 없습니다. 새 풀을 만들어야 합니다. ' VirtualMachineConfiguration '를 사용 하 여 풀을 만드는 것은 모든 Batch Api, 명령줄 도구, Azure Portal 및 Batch Explorer UI에서 지원 됩니다.

**[.Net](tutorial-parallel-dotnet.md) 및 [Python](tutorial-parallel-python.md) 자습서에서는 ' virtualMachineConfiguration '을 사용 하 여 풀을 만드는 예제를 제공 합니다.**

## <a name="pool-configuration-differences"></a>풀 구성 차이점

풀 구성을 업데이트 하는 경우 다음 사항을 고려해 야 합니다.

- ' cloudServiceConfiguration ' 풀 노드는 항상 Windows OS입니다. ' virtualMachineConfiguration ' 풀은 Linux 또는 Windows OS 일 수 있습니다.
- ' CloudServiceConfiguration ' 풀에 비해 ' virtualMachineConfiguration ' 풀에는 컨테이너 지원, 데이터 디스크 및 디스크 암호화와 같은 풍부한 기능 집합이 있습니다.
- ' virtualMachineConfiguration ' 풀 노드는 관리 되는 OS 디스크를 활용 합니다. 각 노드에 사용 되는 [관리 디스크 유형은](../virtual-machines/disks-types.md) 풀에 대해 선택한 VM 크기에 따라 달라 집니다. 풀에 대 한 ' VM 크기를 지정 하는 경우 (예: ' Standard_D2s_v3 ') premium SSD를 사용 합니다. ' 비 s ' VM 크기를 지정 하는 경우 (예: ' Standard_D2_v3 ') 표준 HDD가 사용 됩니다.

   > [!IMPORTANT]
   > Virtual Machines 및 Virtual Machine Scale Sets와 마찬가지로 각 노드에 사용 되는 OS 관리 디스크는 비용을 발생 시킵니다 .이는 Vm 비용에 추가 됩니다. 노드 로컬 SSD에서 OS 디스크를 만들 때 ' cloudServiceConfiguration ' 노드에 대 한 OS 디스크 비용은 없습니다.

- 풀 및 노드 시작 및 삭제 시간은 ' cloudServiceConfiguration ' 풀과 ' virtualMachineConfiguration ' 풀 사이에서 약간 다를 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [풀 구성](nodes-and-pools.md#configurations)에 대해 자세히 알아보세요.
- [풀 모범 사례](best-practices.md#pools)에 대해 자세히 알아보세요.
- [풀 추가](/rest/api/batchservice/pool/add) 및 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)에 대 한 참조를 REST API 합니다.
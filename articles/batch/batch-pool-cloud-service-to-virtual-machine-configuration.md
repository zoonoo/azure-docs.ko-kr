---
title: Cloud Services에서 Virtual Machines 배치 풀 구성 마이그레이션
description: 풀 구성을 최신 및 권장 구성으로 업데이트 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200569"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Cloud Services에서 가상 머신으로 Batch 풀 구성 마이그레이션

현재 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 또는 [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration)를 사용 하 여 Batch 풀을 만들 수 있습니다. 이 구성은 모든 일괄 처리 기능을 지원 하므로 가상 컴퓨터 구성만 사용 하는 것이 좋습니다.

Cloud Services 구성 풀은 현재 배치 기능 중 일부를 지원 하지 않으며 새로 추가 된 기능을 지원 하지 않습니다. [2024 년 2 월 29 일 이후에는](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)새 ' CloudServiceConfiguration ' 풀을 만들거나 기존 풀에 새 노드를 추가할 수 없습니다.

Batch 솔루션에서 현재 ' cloudServiceConfiguration ' 풀을 사용 하는 경우 가능한 한 빨리 ' virtualMachineConfiguration '로 변경 하는 것이 좋습니다. 이렇게 하면 확장 된 [VM 시리즈](batch-pool-vm-sizes.md), Linux vm, [컨테이너](batch-docker-container-workloads.md), [Azure Resource Manager 가상 네트워크](batch-virtual-network.md)및 [노드 디스크 암호화](disk-encryption.md)와 같은 모든 배치 기능을 활용할 수 있습니다.

## <a name="create-a-pool-using-virtual-machine-configuration"></a>가상 컴퓨터 구성을 사용 하 여 풀 만들기

' CloudServiceConfiguration '를 사용 하는 기존 활성 풀을 ' virtualMachineConfiguration '를 사용 하도록 전환할 수 없습니다. 대신 새 풀을 만들어야 합니다. 새 ' virtualMachineConfiguration ' 풀을 만들고 모든 작업과 작업을 복제 한 후에는 더 이상 사용 하지 않는 이전 ' cloudServiceConfiguration'pools을 삭제할 수 있습니다.

모든 Batch Api, 명령줄 도구, Azure Portal 및 Batch Explorer UI를 사용 하면 ' virtualMachineConfiguration '를 사용 하 여 풀을 만들 수 있습니다.

' VirtualMachineConfiguration를 사용 하는 풀을 만드는 프로세스에 대 한 연습은 [.net 자습서](tutorial-parallel-dotnet.md) 또는 [Python 자습서](tutorial-parallel-python.md)를 참조 하세요.

## <a name="pool-configuration-differences"></a>풀 구성 차이점

두 구성의 주요 차이점은 다음과 같습니다.

- ' cloudServiceConfiguration ' 풀 노드는 Windows OS만 사용 합니다. ' virtualMachineConfiguration ' 풀에서 Linux 또는 Windows OS를 사용할 수 있습니다.
- ' CloudServiceConfiguration ' 풀에 비해 ' virtualMachineConfiguration ' 풀에는 컨테이너 지원, 데이터 디스크 및 디스크 암호화와 같은 풍부한 기능 집합이 있습니다.
- 풀 및 노드 시작 및 삭제 시간은 ' cloudServiceConfiguration ' 풀과 ' virtualMachineConfiguration ' 풀 사이에서 약간 다를 수 있습니다.
- ' virtualMachineConfiguration ' 풀 노드는 관리 되는 OS 디스크를 활용 합니다. 각 노드에 사용 되는 [관리 디스크 유형은](../virtual-machines/disks-types.md) 풀에 대해 선택한 VM 크기에 따라 달라 집니다. 풀에 대 한 ' VM 크기를 지정 하는 경우 (예: ' Standard_D2s_v3 ') premium SSD를 사용 합니다. ' 비 s ' VM 크기를 지정 하는 경우 (예: ' Standard_D2_v3 ') 표준 HDD가 사용 됩니다.

   > [!IMPORTANT]
   > Virtual Machines 및 Virtual Machine Scale Sets와 마찬가지로 각 노드에 사용 되는 OS 관리 디스크는 비용을 발생 시킵니다 .이는 Vm 비용에 추가 됩니다. 노드 로컬 SSD에 OS 디스크가 만들어지므로 ' cloudServiceConfiguration ' 노드에 대 한 OS 디스크 비용이 없습니다.

## <a name="azure-data-factory-custom-activity-pools"></a>사용자 지정 활동 풀 Azure Data Factory

Azure Batch 풀은 Data Factory 사용자 지정 작업을 실행 하는 데 사용할 수 있습니다. 사용자 지정 작업을 실행 하는 데 사용 되는 모든 ' cloudServiceConfiguration ' 풀을 삭제 하 고 새 ' virtualMachineConfiguration ' 풀이 생성 되어야 합니다.

Data Factory 사용자 지정 활동을 실행할 새 풀을 만들 때 다음 방법을 따르십시오.

- 새 풀을 만들기 전에 모든 파이프라인을 일시 중지 하 고 기존 풀을 삭제 하 여 실행이 중단 되지 않도록 합니다.
- 연결 된 서비스 구성 변경을 방지 하기 위해 동일한 풀 id를 사용할 수 있습니다.
- 새 풀을 만든 경우 파이프라인을 다시 시작 합니다.

Azure Batch를 사용 하 여 Data Factory 사용자 지정 작업을 실행 하는 방법에 대 한 자세한 내용은 Data Factory 파이프라인에서 [연결 된 서비스](../data-factory/compute-linked-services.md#azure-batch-linked-service) 및 [사용자 지정 작업](../data-factory/transform-data-using-dotnet-custom-activity.md) Azure Batch을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [풀 구성](nodes-and-pools.md#configurations)에 대해 자세히 알아보세요.
- [풀 모범 사례](best-practices.md#pools)에 대해 자세히 알아보세요.
- [풀 덧셈](/rest/api/batchservice/pool/add) 및 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)에 대 한 REST API 참조를 참조 하세요.
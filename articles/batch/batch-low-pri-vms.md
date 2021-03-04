---
title: 경제적이며 우선 순위가 낮은 VM에서 워크로드 실행
description: 우선 순위가 낮은 VM을 프로비전하여 Azure Batch 워크로드의 비용을 줄이는 방법을 알아봅니다.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098471"
---
# <a name="use-low-priority-vms-with-batch"></a>Batch에서 낮은 우선 순위 VM 사용

Azure Batch는 낮은 우선 순위 VM(가상 머신)을 사용하여 Batch 워크로드의 비용을 줄입니다. 우선 순위가 낮은 VM은 비용 절감을 위해 대량의 컴퓨팅 능력을 사용할 수 있도록 하여 새로운 유형의 Batch 워크로드를 가능하게 합니다.

우선 순위가 낮은 VM은 Azure에서 남는 용량을 활용합니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Azure Batch는 가능한 경우 이러한 남는 용량을 사용할 수 있습니다.

우선 순위가 낮은 Vm을 사용 하는 경우의 단점은 사용 가능한 용량에 따라 이러한 Vm을 항상 할당 하지 못할 수도 있고 언제 든 지 선점 될 수도 있다는 것입니다. 이러한 이유로 우선 순위가 낮은 Vm은 작업 완료 시간이 유연 하 고 작업이 여러 Vm에 분산 되는 일괄 처리 및 비동기 처리 작업에 가장 적합 합니다.

우선 순위가 낮은 VM은 전용 VM에 비해 상당히 저렴한 가격으로 제공됩니다. 가격 책정 세부 정보에 대해서는 [Batch 가격 책정](https://azure.microsoft.com/pricing/details/batch/)을 참조하세요.

> [!NOTE]
> 이제 [스폿 VM](https://azure.microsoft.com/pricing/spot/)은 [단일 인스턴스 VM](../virtual-machines/spot-vms.md) 및 [VM 확장 집합](../virtual-machine-scale-sets/use-spot.md)에 사용 가능합니다. 스폿 VM은 우선 순위가 낮은 VM이 개선된 것으로, 스폿 VM을 할당하는 경우 가격 책정이 다를 수 있고 선택적인 최대 가격을 설정할 수 있다는 점에서 다릅니다.
>
>Azure Batch 풀은 나중에 새 버전의 [Batch api 및 도구](./batch-apis-tools.md)를 사용 하 여 스폿 vm을 지원 하기 시작 합니다. 지점 VM 지원 기능을 사용할 수 있게 되 면 우선 순위가 낮은 Vm은 더 이상 사용 되지 않습니다. 현재 Api 및 도구 버전을 사용 하 여 최소 12 개월 동안 현재 Api 및 도구 버전을 사용 하 여 지점 Vm으로의 마이그레이션에 충분 한 시간을 허용 합니다.
>
> 지점 Vm은 가상 컴퓨터 구성 풀에 대해서만 지원 됩니다. 지점 Vm을 사용 하려면 클라우드 서비스 구성 풀을 [가상 컴퓨터 구성 풀로 마이그레이션해야](batch-pool-cloud-service-to-virtual-machine-configuration.md)합니다.

## <a name="batch-support-for-low-priority-vms"></a>우선 순위가 낮은 VM에 대한 Batch 지원

Azure Batch는 우선 순위가 낮은 VM을 쉽게 활용하고 혜택을 얻을 수 있도록 하는 몇 가지 기능을 제공합니다.

- Batch 풀은 전용 VM 및 우선 순위가 낮은 VM을 모두 포함할 수 있습니다. 각 유형의 VM 수는 풀이 만들어질 때 지정될 수 있으며, 기준 풀에 대해서는 명시적 크기 조정 작업이나 자동 크기 조정 기능을 사용해서 언제든지 변경될 수 있습니다. 작업 및 태스크 전송은 풀의 VM 유형과 관계없이 변경되지 않은 상태로 유지될 수 있습니다. 작업을 가능한 한 경제적으로 실행할 수 있게 우선 순위가 낮은 VM을 완전히 사용하도록 풀을 구성할 수도 있지만 용량이 최소 임계값 아래로 떨어질 경우 작업이 계속 실행되도록 하기 위해 전용 VM을 스핀업할 수 있습니다.
- Batch 풀은 목표 개수의 우선 순위가 낮은 VM을 자동으로 검색합니다. Vm을 선점 하거나 사용할 수 없는 경우 Batch는 손실 된 용량을 대체 하 고 대상으로 돌아갑니다.
- 작업이 중단되면 Batch는 작업을 검색하고 자동으로 다시 큐에 넣어 다시 실행합니다.
- 우선 순위가 낮은 VM은 전용 VM의 vCPU 할당량과는 다른 별도의 코어 할당량을 갖습니다. 우선 순위가 낮은 VM은 비용이 저렴하므로 할당량이 전용 VM보다 높습니다. 자세한 내용은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#resource-quotas)을 참조하세요.

> [!NOTE]
> 우선 순위가 낮은 VM은 [사용자 구독 모드](accounts.md)에서 만든 배치 계정에서 지원되지 않습니다.

## <a name="considerations-and-use-cases"></a>고려 사항 및 사용 사례

많은 Batch 워크 로드는 우선 순위가 낮은 Vm에 적합 합니다. 여러 병렬 작업으로 작업을 나눌 때 또는 여러 Vm에서 규모 확장 및 분산 된 많은 작업이 있는 경우이를 사용 하는 것이 좋습니다.

Batch 처리 사용 사례의 일부 예제에는 우선 순위가 낮은 VM을 사용하는 것이 적합합니다.

- **개발 및 테스트**: 특히 대규모 솔루션을 개발 중인 경우 상당한 비용 절감을 실현할 수 있습니다. 모든 유형의 테스트가 혜택을 볼 수 있지만 대규모 부하 테스트 및 회귀 테스트에 사용하면 아주 좋습니다.
- **주문형 용량 보완**: 우선 순위가 낮은 vm은 일반 전용 vm을 보완 하는 데 사용할 수 있습니다. 사용 가능한 경우 작업을 확장 하 여 저렴 한 비용으로 더 빠르게 완료할 수 있습니다. 사용할 수 없는 경우 전용 Vm의 기준선은 계속 사용할 수 있습니다.
- **유연한 작업 실행 시간**: 작업이 완료되어야 하는 시간이 유연한 경우 잠재적인 용량 감소가 허용될 수 있지만 우선 순위가 낮은 VM을 추가하면 작업이 더 낮은 비용으로 더 빠르게 실행됩니다.

다음과 같은 몇 가지 방법으로 우선 순위가 낮은 Vm을 사용 하도록 Batch 풀을 구성할 수 있습니다.

- 풀은 우선 순위가 낮은 Vm만 사용할 수 있습니다. 이 경우 Batch는 사용 가능한 경우 선점된 용량을 모두 복구합니다. 이 구성은 작업을 실행 하는 가장 저렴 한 방법입니다.
- 우선 순위가 낮은 VM을 고정된 전용 VM과 함께 사용할 수 있습니다. 고정된 전용 VM의 수가 있으면 항상 일정한 용량을 작업 처리에 사용할 수 있도록 보장됩니다.
- 풀은 전용 및 우선 순위가 낮은 Vm의 동적 조합을 사용 하 여 사용 가능한 경우에만 저렴 한 우선 순위가 낮은 Vm을 사용할 수 있지만, 필요한 경우에는 전체 가격의 전용 Vm을 확장할 수 있습니다. 이 구성은 최소한의 용량으로 작업을 계속 진행할 수 있도록 합니다.

우선 순위가 낮은 Vm의 사용을 계획할 때 다음 사항에 유의 하세요.

- Azure에서 남는 용량을 최대한 활용하기 위해 적절한 작업을 확장할 수 있습니다.
- 경우에 따라 VM을 사용할 수 없거나 선점되면 작업에 대한 용량이 줄어들어 태스크가 중단되고 다시 실행될 수 있습니다.
- 실행 시간이 짧은 작업은 우선 순위가 낮은 Vm에서 가장 잘 작동 합니다. 좀 더 긴 태스크를 포함하는 작업은 중단될 경우 더 많은 영향을 받을 수 있습니다. 장기 실행 태스크가 검사점을 구현 하 여 실행 되는 동안 진행 상태를 저장 하면 이러한 영향을 줄일 수 있습니다. 
- 여러 VM을 활용하는 장기 실행 MPI 작업은 하나의 선점된 VM 때문에 전체 작업이 다시 실행되어야 하므로 우선 순위가 낮은 VM을 사용하는 것이 별로 적합하지 않습니다.
- [NSG (네트워크 보안 그룹) 규칙](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) 을 잘못 구성 하면 우선 순위가 낮은 노드를 사용할 수 없는 것으로 표시할 수 있습니다.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>우선 순위가 낮은 Vm을 사용 하 여 풀 만들기 및 관리

Batch 풀은 전용 VM 및 우선 순위가 낮은 VM(Compute 노드라고도 함)을 둘 다 포함할 수 있습니다. 전용 및 우선 순위가 낮은 VM 둘 다에 대해 Compute 노드의 목표 수를 설정할 수 있습니다. 목표 노드 수는 풀에 유지하려는 VM의 수를 지정합니다.

예를 들어 5 개의 전용 Vm 및 20 개의 낮은 우선 순위 Vm을 사용 하 여 Azure virtual machines (이 경우 Linux Vm)를 사용 하는 풀을 만들려면 다음을 수행 합니다.

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

전용 및 우선 순위가 낮은 VM 둘 다의 현재 노드 수를 가져올 수 있습니다.

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

풀 노드에는 해당 노드가 전용 VM인지 또는 우선 순위가 낮은 VM인지를 나타내는 속성이 있습니다.

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Vm을 종종 선점할 수 있습니다. 이 경우 선점 된 노드 Vm에서 실행 중인 작업이 다시 큐에 대기 되 고 다시 실행 됩니다.

가상 컴퓨터 구성 풀의 경우 Batch는 다음 작업도 수행 합니다.

- 선점된 VM의 상태는 **선점됨** 으로 업데이트됩니다. 
- VM은 효과적으로 삭제되므로 VM에 로컬로 저장된 모든 데이터는 손실됩니다.
- 풀에 대 한 노드 나열 작업은 계속 해 서 선점 된 노드를 반환 합니다.
- 풀은 계속해서 우선 순위가 낮은 노드의 목표 개수가 사용 가능해지도록 하려고 합니다. 대체 용량이 발견되면 노드는 해당 ID를 유지하지만 다시 초기화되며, 작업 예약에 사용되기 전에 먼저 **만드는 중** 및 **시작 중** 상태를 거치게 됩니다.
- 선점 수는 Azure Portal에서 메트릭으로 사용할 수 있습니다.

## <a name="scale-pools-containing-low-priority-vms"></a>우선 순위가 낮은 Vm을 포함 하는 풀 크기 조정

전용 VM으로만 구성된 풀의 경우처럼, Resize 메서드를 호출하거나 자동 크기 조정을 사용하여 우선 순위가 낮은 VM을 포함하는 풀의 크기를 조정할 수 있습니다.

풀 크기 조정 작업은 **targetLowPriorityNodes** 값을 업데이트하는 두 번째 선택적 매개 변수를 사용합니다.

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

풀 자동 크기 조정 수식은 우선 순위가 낮은 VM을 다음과 같이 지원합니다.

- 서비스 정의 변수 **$TargetLowPriorityNodes** 의 값을 가져오거나 설정할 수 있습니다.
- 서비스 정의 변수 **$CurrentLowPriorityNodes** 의 값을 가져올 수 있습니다.
- 서비스 정의 변수 **$PreemptedNodeCount** 의 값을 가져올 수 있습니다. 이 변수는 선점 상태의 노드 수를 반환하고 사용할 수 없는 선점된 노드 수에 따라 전용 노드의 수를 늘리거나 줄일 수 있도록 합니다.

## <a name="configure-jobs-and-tasks"></a>작업 및 태스크 구성

작업 및 태스크에는 우선 순위가 낮은 노드에 대 한 추가 구성이 거의 필요 하지 않습니다. 고려할 사항은 다음과 같습니다.

- 작업의 JobManagerTask 속성에는 **AllowLowPriorityNode** 속성이 있습니다. 이 속성이 true이면 작업 관리자 태스크는 전용 또는 우선 순위가 낮은 노드에서 예약될 수 있습니다. False 인 경우 작업 관리자 태스크는 전용 노드에서만 예약 됩니다.
- AZ_BATCH_NODE_IS_DEDICATED [환경 변수](batch-compute-node-environment-variables.md) 는 작업 응용 프로그램에서 사용할 수 있으므로 우선 순위가 낮거나 전용 노드에서 실행 중인지 여부를 확인할 수 있습니다.

## <a name="view-metrics-for-low-priority-vms"></a>우선 순위가 낮은 Vm에 대 한 메트릭 보기

우선 순위가 낮은 노드의 경우 [Azure Portal](https://portal.azure.com)에서 새 메트릭을 사용할 수 있습니다. 이러한 메트릭은 다음과 같습니다.

- 우선 순위가 낮은 노드 수
- 우선 순위가 낮은 코어 수
- 선점된 노드 수

Azure Portal에서 이러한 메트릭을 보려면

1. Azure Portal에서 Batch 계정으로 이동합니다.
2. **모니터링** 섹션에서 **메트릭** 을 선택합니다.
3. **메트릭** 목록에서 원하는 메트릭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드 및 작업과 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
- 우선 순위가 낮은 VM에서 스폿 VM으로 이동하도록 계획합니다. **클라우드 서비스 구성** 풀에서 우선 순위가 낮은 vm을 사용 하는 경우 대신 [ **가상 머신 구성** 풀](nodes-and-pools.md#configurations) 로 마이그레이션할 계획을 세워야 합니다.

---
title: 동시에 태스크를 실행하여 Batch 컴퓨팅 노드의 사용량 극대화
description: Azure Batch 풀의 각 노드에서 병렬 작업을 실행하고 더 적은 수의 계산 노드를 사용하여 효율성은 높이고 비용은 낮춥니다.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389301"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>동시에 태스크를 실행하여 Batch 컴퓨팅 노드의 사용량 극대화

각 노드에서 두 개 이상의 작업을 동시에 실행하여 풀의 더 적은 계산 노드로 리소스 사용량을 최대화할 수 있습니다.

일부 시나리오는 단일 작업 전용의 모든 노드 리소스에서 가장 잘 작동하지만, 여러 작업에서 해당 리소스를 공유하는 경우 특정 워크로드에서 작업 시간이 단축되고 비용이 절감될 수 있습니다. 다음과 같은 시나리오를 고려해 보세요.

- 데이터를 공유할 수 있는 작업에서 **데이터 전송 최소화하기**. 공유 데이터를 더 적은 수의 노드로 복사하고 각 노드에서 병렬로 작업을 실행하여 데이터 전송 요금을 많이 줄일 수 있습니다. 특히 각 노드로 복사할 데이터를 지역 간에 전송해야 하는 경우 적용됩니다.
- 작업의 **메모리 사용 최대화** 는 많은 양의 메모리가 필요하지만 단기간에만 그리고 실행 중에는 가변 시간에 메모리 사용량을 최대화합니다. 이러한 급증을 효율적으로 처리하기 위해 많은 메모리를 가진 적지만 크기가 큰 컴퓨팅 노드를 사용할 수 있습니다. 해당 노드에는 각 노드에서 병렬로 실행되는 여러 작업이 있지만 각 작업은 시간을 두고 노드의 풍부한 메모리를 활용합니다.
- 풀에서 노드 간 통신이 필요한 경우 **노드 숫자 제한 완화**. 현재 노드 간 통신에 대해 구성된 풀은 컴퓨팅 노드가 50개로 제한됩니다. 이러한 풀의 각 노드가 병렬로 태스크를 실행할 수 있으면 더 많은 수의 태스크를 동시에 실행할 수 있습니다.
- 최초로 컴퓨팅 환경을 Azure로 이동하는 경우 등 **온-프레미스 계산 클러스터 복제**. 현재 온-프레미스 솔루션이 컴퓨팅 노드당 여러 태스크를 실행하는 경우 최대 노드 태스크 수를 늘려 해당 구성을 보다 자세히 미러링할 수 있습니다.

## <a name="example-scenario"></a>예제 시나리오

예를 들어, CPU 및 메모리 요구 사항이 포함된 작업 애플리케이션에서 [표준 \_ D1](../cloud-services/cloud-services-sizes-specs.md#d-series) 노드가 충분하다고 가정합니다. 하지만 주어진 시간에 작업을 완료하기 위해 해당 노드가 1,000개 필요합니다.

1개 CPU 코어가 있는 Standard\_D1 노드를 사용하는 대신 각각 16개 코어가 있는 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) 노드를 사용하여 병렬 태스크 실행을 구현할 수 있습니다. 따라서 사용되는 노드 수가 1/16로 줄기 때문에 필요한 노드 수는 1000개가 아니라 63개입니다. 대규모 애플리케이션 파일 또는 참조 데이터가 각 노드에 대해 필요한 경우 데이터가 63개의 노드로 복사되기 때문에 작업 기간 및 효율성이 개선됩니다.

## <a name="enable-parallel-task-execution"></a>병렬 작업 실행 사용

풀 수준에서 병렬 작업 실행을 위해 컴퓨팅 노드를 구성합니다. Batch .NET 라이브러리를 사용하여 풀을 만들 때 [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 속성을 설정합니다. Batch REST API를 사용하는 경우 풀을 만들 때 요청 본문에 [taskSlotsPerNode](/rest/api/batchservice/pool/add) 요소를 설정합니다.

> [!NOTE]
> 풀을 만들 때만 `taskSlotsPerNode` 요소 및 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 설정할 수 있습니다. 풀이 이미 만들어진 후에는 수정될 수 없습니다.

Azure Batch를 사용하면 코어 노드의 최대 4배수의 노드마다 작업 슬롯을 설정할 수 있습니다. 예를 들어, 풀이 노드 크기 “Large”로 구성되었다면(4코어) `taskSlotsPerNode` 는 16으로 설정될 수 있습니다. 그러나 노드에 있는 코어 수에 관계없이 노드당 태스크 슬롯 수는 256개를 초과할 수 없습니다. 각 노드 크기에 대한 코어 수에 대한 자세한 내용은 [Cloud Services에 적합한 크기](../cloud-services/cloud-services-sizes-specs.md)를 참조하세요. 서비스 제한에 대한 자세한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

> [!TIP]
> 풀에 [자동 크기 조정 수식](/rest/api/batchservice/pool/enableautoscale)을 구성할 때는 `taskSlotsPerNode` 값을 고려해야 합니다. 예를 들어, `$RunningTasks` 를 평가하는 수식은 노드당 작업 수 증가에 크게 영향을 받을 수 있습니다. 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 스케일링](batch-automatic-scaling.md)을 참조하세요.

## <a name="specify-task-distribution"></a>작업 배포 지정

동시 작업을 사용하도록 설정할 때 풀의 노드 간에 작업을 분산하는 방법을 지정하는 것이 중요합니다.

[CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) 속성을 사용하여 풀의 모든 노드에서 태스크를 균등하게 할당하도록 지정할 수 있습니다("확산"). 또는 작업이 풀의 다른 노드로 할당되기 전에 최대한 많은 작업이 각 노드에 할당되도록 지정할 수 있습니다.("압축")

예를 들어, [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 값이 16으로 구성된 [표준\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) 노드(위 예제)의 풀을 고려하세요. [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy)가 *Pack* 인 [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype)으로 구성된 경우, 각 노드의 모든 16개 코어의 사용량을 최대화하며 [풀 자동 스케일링](batch-automatic-scaling.md)을 통해 풀에서 사용되지 않는 노드(태스크가 할당되지 않은 노드)를 제거합니다. 리소스 사용량을 최소화하고 비용을 절감합니다.

## <a name="define-variable-slots-per-task"></a>작업당 가변 슬롯 정의

작업은 계산 노드에서 실행할 슬롯 수를 지정하여 [CloudTask.RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) 속성으로 정의할 수 있습니다. 기본값은 1입니다. 계산 노드의 리소스 사용량과 관련하여 작업의 가중치가 다른 경우 가변 작업 슬롯을 설정할 수 있습니다. 이렇게 하면 CPU나 메모리와 같은 대용량 시스템 리소스 없이 각 계산 노드에 적절한 수의 동시 실행 작업을 수행할 수 있습니다.

예를 들어, `taskSlotsPerNode = 8` 속성이 있는 풀의 경우, `requiredSlots = 8`을 사용하여 다중 코어가 필요한 CPU 집약적 작업을 전송할 수 있지만 다른 작업은 `requiredSlots = 1`로 설정할 수 있습니다. 이 혼합된 워크로드가 예약되면 CPU를 많이 사용하는 작업이 해당 계산 노드에서 독점적으로 실행되지만 다른 태스크는 동시에(한 번에 최대 8개의 작업) 다른 노드에서 실행될 수 있습니다. 이를 통해 계산 노드에서 워크로드의 균형을 조정하고 리소스 사용 효율성을 개선할 수 있습니다.

작업의 `requiredSlots`을 풀의 `taskSlotsPerNode`보다 크게 지정하지 않아야 합니다. 이렇게 하면 작업을 실행할 수 없습니다. 작업이 제출 시간에만 종속되지 않았거나 사용 안 함/재사용 설정으로 인해 다른 풀로 변경될 수 있으므로 배치 서비스는 작업을 제출할 때 동시에 이 충돌의 유효성을 검사하지 않습니다.

> [!TIP]
> 변수 작업 슬롯을 사용하는 경우 일부 노드에 여전히 유휴 슬롯이 있는 경우에도 모든 계산 노드에서 충분한 슬롯을 사용할 수 없기 때문에 더 많은 필수 슬롯이 있는 대량 작업을 일시적으로 예약하지 못할 수 있습니다. 해당 작업에 대한 작업 우선 순위를 높여 노드의 사용 가능한 슬롯에 대해 경쟁하는 기회를 늘릴 수 있습니다.
>
> 배치 서비스는 실행할 작업을 예약하지 못할 때 [TaskScheduleFailEvent](batch-task-schedule-fail-event.md)를 내보내고, 필요한 슬롯을 사용할 수 있을 때까지 일정 예약을 다시 시도합니다. 이 이벤트를 수신하여 잠재적인 작업 예약 문제를 감지하고 적절하게 완화할 수 있습니다.

## <a name="batch-net-example"></a>Batch .NET 예

다음 [Batch .Net](/dotnet/api/microsoft.azure.batch) API 코드 조각은 노드당 여러 작업 슬롯과 함께 풀을 만드는 방법 및 필수 슬롯을 사용하여 작업을 제출하는 방법을 보여줍니다.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>노드당 여러 작업 슬롯을 사용하여 풀 만들기

이 코드 조각은 노드당 최대 4개의 작업 슬롯이 허용되는 4개의 노드를 포함하는 풀을 만드는 요청을 나타냅니다. 풀의 다른 노드로 작업을 할당하기 전에 각 노드를 채울 정책을 예약하는 작업을 지정합니다.

Batch .NET API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)을 참조하세요.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>필수 슬롯을 사용하여 작업 만들기

이 코드 조각은 기본값이 아닌 `requiredSlots`이 있는 작업을 만듭니다. 이 작업은 계산 노드에 사용할 수 있는 여유 슬롯이 충분한 경우에만 실행됩니다.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>실행 중인 작업 및 슬롯 수를 포함하는 계산 노드 나열

이 코드 조각은 풀의 모든 계산 노드를 나열하고 노드당 실행 중인 작업 및 작업 슬롯 수를 출력합니다.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>작업의 작업 수 나열

이 코드 조각은 작업의 작업 수를 가져오며 작업 상태별로 작업 및 작업 슬롯 수를 모두 포함합니다.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST 예

다음 [Batch REST](/rest/api/batchservice/) API 코드 조각은 노드당 여러 개의 작업 슬롯이 있는 풀을 만드는 방법 및 필수 슬롯이 있는 작업을 제출하는 방법을 보여줍니다.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>노드당 여러 작업 슬롯을 사용하여 풀 만들기

이 코드 조각은 노드당 최대 4개의 작업이 있는 2개의 대규모 노드를 포함하는 풀을 만드는 요청을 나타냅니다.

REST API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [계정에 풀 추가](/rest/api/batchservice/pool/add)를 참조하세요.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>필수 슬롯을 사용하여 작업 만들기

이 코드 조각은 기본값이 아닌 `requiredSlots`이 있는 작업을 추가하는 요청을 보여줍니다. 이 작업은 계산 노드에 사용할 수 있는 여유 슬롯이 충분한 경우에만 실행됩니다.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub의 코드 샘플

GitHub의 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 프로젝트는 [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 속성의 사용법을 보여줍니다.

이 C# 콘솔 애플리케이션은 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리를 사용하여 하나 이상의 컴퓨팅 노드가 있는 풀을 만듭니다. 해당 노드에서 구성 가능한 다양한 작업을 실행하여 변수 부하를 시뮬레이션합니다. 애플리케이션의 출력은 각 작업을 실행한 노드를 보여줍니다. 또한 애플리케이션은 작업 매개 변수 및 기간의 요약을 제공합니다.

예를 들어, 다음은 ParallelTasks 샘플 애플리케이션의 두 가지 다른 실행에서 출력의 요약 부분입니다. 제출 시간에 계산 노드가 *유휴* 상태였던 이전에 만든 풀로 각 작업을 제출했으므로 여기에 표시된 작업 기간에는 풀 생성 시간이 포함되지 않습니다.

샘플 애플리케이션의 최초 실행에서는 풀 내 단일 노드 및 노드 당 작업의 기본값 설정을 보여주며 작업 길이는 30분이 넘습니다.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

샘플의 두 번째 실행은 작업 기간에서 크게 감소합니다. 풀이 노드당 4개의 작업으로 구성되었기 때문에 이를 사용하면 병렬 작업 실행이 거의 1/4 시간에서 작업을 완료할 수 있습니다.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>다음 단계

- [Batch Explorer](https://azure.github.io/BatchExplorer/) 열 지도를 사용해 보세요. Batch Explorer는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 샘플 애플리케이션을 실행할 때 Batch Explorer 열 지도 기능을 사용하면 각 노드에서 병렬 태스크의 실행을 쉽게 시각화할 수 있습니다.
- [GitHub의 Azure Batch 샘플](https://github.com/Azure/azure-batch-samples)을 알아보세요.
- [배치 작업 종속성](batch-task-dependencies.md)에 대해 자세히 알아보세요.

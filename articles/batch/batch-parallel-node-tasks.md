---
title: 동시에 태스크를 실행하여 Batch 컴퓨팅 노드의 사용량 극대화
description: Azure Batch 풀의 각 노드에 대해 더 적은 수의 계산 노드를 사용 하 고 작업을 병렬로 실행 하 여 효율성을 높이고 비용을 절감할 수 있습니다.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102968"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>동시에 태스크를 실행하여 Batch 컴퓨팅 노드의 사용량 극대화

각 노드에서 두 개 이상의 작업을 동시에 실행 하 여 풀의 계산 노드에서 리소스 사용량을 최대화할 수 있습니다.

일부 시나리오는 단일 작업 전용 노드의 모든 리소스에서 가장 잘 작동 하지만, 여러 작업에서 해당 리소스를 공유 하는 경우 특정 워크 로드에 더 짧은 작업 시간이 표시 되 고 비용이 절감 될 수 있습니다.

- 데이터를 공유할 수 있는 태스크에 대 한 **데이터 전송을 최소화** 합니다. 공유 데이터를 더 적은 수의 노드로 복사 하 고 각 노드에서 병렬로 태스크를 실행 하 여 데이터 전송 요금을 크게 줄일 수 있습니다. 특히 각 노드로 복사할 데이터를 지역 간에 전송해야 하는 경우 적용됩니다.
- 많은 양의 메모리를 필요로 하는 태스크에 대 한 **메모리 사용을 최대화** 합니다. 단, 짧은 기간 동안에만 실행 중에 변수를 사용 합니다. 이러한 급증을 효율적으로 처리하기 위해 많은 메모리를 가진 적지만 크기가 큰 컴퓨팅 노드를 사용할 수 있습니다. 이러한 노드에는 각 노드에서 병렬로 실행되는 여러 작업이 있지만 각 작업은 시간을 두고 노드의 풍부한 메모리를 활용합니다.
- 풀 내에서 노드 간 통신이 필요한 경우 **노드 번호 제한을 완화** 합니다. 현재 노드 간 통신에 대해 구성된 풀은 컴퓨팅 노드가 50개로 제한됩니다. 이러한 풀의 각 노드가 병렬로 태스크를 실행할 수 있으면 더 많은 수의 태스크를 동시에 실행할 수 있습니다.
- 먼저 계산 환경을 Azure로 이동 하는 경우와 같이 **온-프레미스 계산 클러스터를 복제**합니다. 현재 온-프레미스 솔루션이 컴퓨팅 노드당 여러 태스크를 실행하는 경우 최대 노드 태스크 수를 늘려 해당 구성을 보다 자세히 미러링할 수 있습니다.

## <a name="example-scenario"></a>예제 시나리오

예를 들어, CPU 및 메모리 요구 사항이 포함 된 작업 응용 프로그램에서 [표준 \_ D1](../cloud-services/cloud-services-sizes-specs.md) 노드가 충분 하다 고 가정 합니다. 그러나 필요한 시간 내에 작업을 완료 하기 위해 이러한 노드의 1000이 필요 합니다.

1개 CPU 코어가 있는 Standard\_D1 노드를 사용하는 대신 각각 16개 코어가 있는 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) 노드를 사용하여 병렬 태스크 실행을 구현할 수 있습니다. 즉, 1000 노드 대신 *16 배 작은 노드* 를 사용할 수 있습니다. 63만 필요 합니다. 각 노드에 규모가 많은 응용 프로그램 파일 또는 참조 데이터가 필요한 경우 데이터가 63 노드에만 복사 되기 때문에 작업 기간 및 효율성은 다시 개선 됩니다.

## <a name="enable-parallel-task-execution"></a>병렬 작업 실행 사용

풀 수준에서 병렬 작업 실행을 위해 컴퓨팅 노드를 구성합니다. Batch .NET 라이브러리를 사용 하 여 풀을 만들 때 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 설정 합니다. 일괄 처리 REST API 사용 하는 경우 풀을 만드는 동안 요청 본문에서 [taskSlotsPerNode](/rest/api/batchservice/pool/add) 요소를 설정 합니다.

> [!NOTE]
> `taskSlotsPerNode`풀을 만들 때만 요소 및 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 설정할 수 있습니다. 풀을 이미 만든 후에는 수정할 수 없습니다.

Azure Batch를 사용 하 여 노드당 작업 슬롯을 노드 코어 수 (최대 4x)로 설정할 수 있습니다. 예를 들어, 풀이 노드 크기 “Large”로 구성되었다면(4코어) `taskSlotsPerNode` 는 16으로 설정될 수 있습니다. 그러나 노드에 있는 코어 수에 관계 없이 노드당 작업 슬롯을 256 개 이상 포함할 수 없습니다. 각 노드 크기에 대한 코어 수에 대한 자세한 내용은 [Cloud Services에 적합한 크기](../cloud-services/cloud-services-sizes-specs.md)를 참조하세요. 서비스 제한에 대한 자세한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

> [!TIP]
> 풀에 [자동 크기 조정 수식](/rest/api/batchservice/pool/enableautoscale)을 구성할 때는 `taskSlotsPerNode` 값을 고려해야 합니다. 예를 들어, `$RunningTasks` 를 평가하는 수식은 노드당 작업 수 증가에 크게 영향을 받을 수 있습니다. 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조 하세요.

## <a name="specify-task-distribution"></a>작업 배포 지정

동시 태스크를 사용 하도록 설정할 때 풀의 노드 간에 작업을 분산 하는 방법을 지정 하는 것이 중요 합니다.

[CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 사용하여 풀의 모든 노드에서 태스크를 균등하게 할당하도록 지정할 수 있습니다("확산"). 또는 작업이 풀의 다른 노드로 할당되기 전에 최대한 많은 작업이 각 노드에 할당되도록 지정할 수 있습니다.("압축")

예를 들어, TaskSlotsPerNode 값 16으로 구성 된 [표준 \_ D14](../cloud-services/cloud-services-sizes-specs.md) 노드의 풀 (위 예제에서는)을 고려 합니다 [.](/dotnet/api/microsoft.azure.batch.cloudpool) [Cloudpool.taskschedulingpolicy](/dotnet/api/microsoft.azure.batch.cloudpool) 가 *Pack*의 [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) 로 구성 된 경우 각 노드의 16 개 코어를 모두 사용 하는 것이 최대화 되 고 자동 크기 조정 [풀이](batch-automatic-scaling.md) 풀에서 사용 되지 않는 노드 (태스크가 할당 되지 않은 노드)를 제거할 수 있습니다. 리소스 사용량을 최소화하고 비용을 절감합니다.

## <a name="define-variable-slots-per-task"></a>작업당 가변 슬롯 정의

작업은 클라우드 노드를 실행 하는 데 필요한 슬롯 수를 지정 하 여 [Cloudtask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) 속성으로 정의할 수 있습니다. 기본값은 1입니다. 계산 노드의 리소스 사용량과 관련 하 여 작업의 가중치가 다른 경우 가변 작업 슬롯을 설정할 수 있습니다. 이렇게 하면 CPU 또는 메모리와 같은 과도 한 시스템 리소스 없이 각 계산 노드에 적절 한 수의 동시 실행 태스크가 있을 수 있습니다.

예를 들어 속성이 있는 풀의 경우에는를 `taskSlotsPerNode = 8` 사용 하 여 다중 코어 필수 CPU 집약적 작업을 전송할 수 `requiredSlots = 8` 있지만 다른 작업은로 설정할 수 있습니다 `requiredSlots = 1` . 이 혼합 된 워크 로드가 예약 되 면 CPU를 많이 사용 하는 작업이 해당 계산 노드에서 독점적으로 실행 되지만 다른 태스크는 동시에 (한 번에 최대 8 개의 작업) 다른 노드에서 실행 될 수 있습니다. 이를 통해 계산 노드에서 워크 로드의 균형을 조정 하 고 리소스 사용 효율성을 향상 시킬 수 있습니다.

> [!TIP]
> 변수 작업 슬롯을 사용 하는 경우 일부 노드에 여전히 유휴 슬롯이 있는 경우에도 모든 계산 노드에서 충분 한 슬롯을 사용할 수 없기 때문에 더 많은 필수 슬롯을 가진 대량 작업을 일시적으로 예약 하지 못할 수 있습니다. 이러한 작업에 대 한 작업 우선 순위를 높여 노드의 사용 가능한 슬롯에 대해 경쟁 하는 기회를 높일 수 있습니다.
>
> 일괄 처리 서비스는 실행할 작업을 예약 하지 못할 때 [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) 을 내보내고, 필수 슬롯을 사용할 수 있을 때까지 일정을 다시 시도 합니다. 이 이벤트를 수신 하 여 잠재적인 작업 예약 문제를 감지 하 고 적절 하 게 완화할 수 있습니다.

> [!NOTE]
> 작업의를 `requiredSlots` 풀의 보다 크게 지정 하지 마십시오 `taskSlotsPerNode` . 이렇게 하면 작업을 실행할 수 없습니다. 일괄 처리 서비스는 작업을 제출할 때 작업을 제출할 때이 충돌의 유효성을 검사 하지 않습니다. 즉, 작업에 전송 시간에 바인딩된 풀이 없거나 사용 하지 않도록 설정 하거나 다시 사용 하도록 설정 하 여 다른 풀로 변경할 수 있습니다.

## <a name="batch-net-example"></a>Batch .NET 예

다음 [Batch .Net](/dotnet/api/microsoft.azure.batch) API 코드 조각은 노드당 여러 작업 슬롯과 함께 풀을 만드는 방법 및 필수 슬롯을 사용 하 여 작업을 제출 하는 방법을 보여 줍니다.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>노드당 여러 작업 슬롯을 사용 하 여 풀 만들기

이 코드 조각은 노드당 4 개의 작업 슬롯이 허용 된 4 개의 노드를 포함 하는 풀을 만드는 요청을 보여 줍니다. 풀의 다른 노드로 작업을 할당하기 전에 각 노드를 채울 정책을 예약하는 작업을 지정합니다.

Batch .NET API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations)을 참조하세요.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>필수 슬롯을 사용 하 여 작업 만들기

이 코드 조각은 기본값이 아닌 작업을 만듭니다 `requiredSlots` . 이 작업은 계산 노드에 사용할 수 있는 사용 가능한 슬롯이 충분 한 경우에만 실행 됩니다.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>작업 및 슬롯 실행 횟수를 포함 하는 계산 노드 나열

이 코드 조각은 풀의 모든 계산 노드를 나열 하 고 노드당 실행 중인 태스크 및 태스크 슬롯 수를 출력 합니다.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>작업의 작업 수를 나열 합니다.

이 코드 조각은 태스크에 대 한 태스크 수를 가져오며 태스크 상태 마다 태스크 및 태스크 슬롯 수를 모두 포함 합니다.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST 예

다음 [BATCH REST](/rest/api/batchservice/) API 코드 조각은 노드당 여러 개의 작업 슬롯과 함께 풀을 만드는 방법 및 필수 슬롯을 사용 하 여 작업을 제출 하는 방법을 보여 줍니다.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>노드당 여러 작업 슬롯을 사용 하 여 풀 만들기

이 코드 조각은 노드당 최대 4 개의 작업을 포함 하는 두 개의 대량 노드가 포함 된 풀을 만드는 요청을 보여 줍니다.

REST API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [계정에 풀 추가](/rest/api/batchservice/pool/add)를 참조하세요.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>필수 슬롯을 사용 하 여 작업 만들기

이 코드 조각은 기본값이 아닌 작업을 추가 하는 요청을 보여 줍니다 `requiredSlots` . 이 작업은 계산 노드에 사용할 수 있는 사용 가능한 슬롯이 충분 한 경우에만 실행 됩니다.

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

GitHub의 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 프로젝트에서는 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 사용 하는 방법을 보여 줍니다.

이 C# 콘솔 애플리케이션은 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리를 사용하여 하나 이상의 컴퓨팅 노드가 있는 풀을 만듭니다. 이러한 노드에서 구성 가능한 태스크를 실행 하 여 변수 부하를 시뮬레이션 합니다. 응용 프로그램의 출력에는 각 작업을 실행 한 노드가 표시 됩니다. 또한 애플리케이션은 작업 매개 변수 및 기간의 요약을 제공합니다.

예를 들어, 아래는 ParallelTasks 샘플 응용 프로그램의 두 가지 다른 실행에서 출력의 요약 부분입니다. 전송 시간에 계산 노드가 *유휴* 상태 였던 이전에 만든 풀로 각 작업을 제출 했으므로 여기에 표시 된 작업 기간에는 풀 생성 시간이 포함 되지 않습니다.

샘플 애플리케이션의 최초 실행에서는 풀 내 단일 노드 및 노드 당 작업의 기본값 설정을 보여주며 작업 길이는 30분이 넘습니다.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

샘플의 두 번째 실행은 작업 기간에서 크게 감소합니다. 이는 풀이 노드당 4 개의 작업으로 구성 되었기 때문입니다. 병렬 작업 실행이 거의 시간 동안 작업을 완료할 수 있습니다.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>다음 단계

- [Batch Explorer](https://azure.github.io/BatchExplorer/) 열 지도를 사용해 보세요. Batch Explorer는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 샘플 응용 프로그램을 실행 하는 경우 Batch Explorer 열 지도 기능을 사용 하면 각 노드에서 병렬 작업의 실행을 쉽게 시각화할 수 있습니다.
- [GitHub에서 Azure Batch 샘플을](https://github.com/Azure/azure-batch-samples)탐색 합니다.
- [Batch 태스크 종속성](batch-task-dependencies.md)에 대해 자세히 알아보세요.

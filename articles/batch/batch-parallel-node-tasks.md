<properties
   pageTitle="병렬 작업 사용으로 배치 노드 최대화 | Microsoft Azure"
   description="Azure Batch 풀의 각 노드에서 동시 작업을 실행하는 동안 더 적은 수의 계산 노드를 통해 효율성은 높이고 비용은 낮춤"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# 동시 노드 작업으로 Azure Batch 계산 리소스 사용 극대화

대규모 병렬 작업 실행은 Azure Batch의 핵심 기능으로, 작업을 실행하는 여러 계산 노드뿐 아니라 그러한 노드에서 실행 중인 동시 작업에까지 확대됩니다. 배치를 사용하면 병렬 작업 실행의 수직 및 수평 확장이 가능합니다.

풀의 계산 노드에서 동시 작업 실행을 활성화하면 풀 안에서 더 적은 수의 노드로 리소스 사용량을 극대화할 수 있습니다. 일부 시나리오에서는 단일 작업에 모든 노드 리소스를 할당 가능한 것이 장점으로 작용하지만 대부분의 상황에서는 여러 작업이 리소스를 공유하는 것이 유용합니다.

 - 작업이 데이터를 공유할 수 있을 때 **데이터 전송을 최소화**. 이 시나리오에서는 공유 데이터를 더 적은 수의 노드에 복사하고 각 노드에서 병렬로 작업을 실행하여 데이터 전송 요금을 상당히 줄일 수 있습니다. 특히 각 노드로 복사되는 데이터가 지리적으로 떨어진 지역 간에 전송되는 경우가 그렇습니다.

 - 실행 중 가변 시점에 단기간 동안 작업을 위해 대용량의 메모리가 필요한 경우 **메모리 사용량 최대화** 수는 더 적지만 규모는 더 크고 더 많은 메모리가 있는 노드 인스턴스를 사용하면, 서로 다른 시점에서 노드의 여유 메모리를 활용하여 각 노드에서 작업을 병렬 실행하여 이러한 스파이크를 효율적으로 처리할 수 있습니다.

 - 풀 안에서 노드 내 통신이 필요한 경우 **노드 숫자 제한 완화**. 현재 노드 간 통신에 구성된 풀은 50개 노드로 제한되어 있으므로, 이런 풀의 각 노드가 병렬로 작업을 실행할 수 있다면 더 많은 수의 작업을 동시에 실행할 수 있습니다.

 - 최초로 컴퓨팅 환경을 Azure로 이동하는 경우 등, **온-프레미스 계산 클러스터 복제**. 구성이 현재 계산 노드당 여러 작업을 실행하는 경우 최대 노드 작업 수를 늘리면 기존 물리적 구성을 더 긴밀하게 미러링할 수 있습니다.

## 예제 시나리오

병렬 작업 실행의 장점을 설명하기 위해 다음과 같은 작업 응용 프로그램의 CPU와 메모리 요구 사항을 가정해 보겠습니다. Standard\_D1 노드 크기는 적합하지만 필요한 시간에 작업을 실행하기 위해 이런 노드가 1000개 필요합니다. Standard\_D1 노드를 사용하는 대신 16코어 Standard\_D14 노드를 통해 병렬 작업 실행을 구현할 수 있습니다. 이 경우 *사용되는 노드 수가 1/16로 줄기 때문에* 필요한 노드 수는 1000개가 아니라 63개입니다. 이렇게 하면 각 노드에서 대규모 응용 프로그램 파일이나 참조 데이터가 필요할 때 작업 실행 시간과 효율성을 크게 높일 수 있습니다.

## 병렬 작업 실행 사용

병렬 작업 실행을 위한 배치 솔룻에서의 계산 노드 구성은 풀 수준에서 실행됩니다. Batch .NET API 작업 시 풀을 만들 때 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 속성이 설정됩니다. Batch REST API에서는 풀을 만들 때 요청 본문에서 [maxTasksPerNode][maxtasks_rest] 요소가 설정됩니다.

Azure Batch를 사용하면 노드 코어의 최대 4배수의 노드 설정마다 최대 작업이 가능합니다. 예를 들어, 풀이 노드 크기 “Large”로 구성되었다면(4코어) `maxTasksPerNode` 는 16으로 설정할 수 있습니다. 각 노드 크기의 코어 수에 대한 자세한 내용은 [가상 컴퓨터에 대한 크기](../virtual-machines/virtual-machines-size-specs.md)에, 서비스 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 제공합니다.

> [AZURE.TIP]풀에 [자동 크기 조정 수식][enable_autoscaling]을 구성할 때는 `maxTasksPerNode` 값을 고려해야 합니다. 예를 들어, `$RunningTasks`를 평가하는 수식은 노드당 작업 수 증가에 크게 영향을 받을 수 있습니다. 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

## 작업 배분

풀 내의 계산 노드가 작업을 동시에 실행할 수 있는 경우 풀 내 노드에서의 작업 배분 방법을 지정하는 것이 중요합니다.

[CloudPool.TaskSchedulingPolicy][task_schedule] 속성을 통해 풀 내 모든 노드에서 고르게 작업 할당("분배") 또는 작업을 풀의 다른 노드에 할당하기 전에 각 노드에 가능한 많은 수의 작업 할당("압축")을 지정할 수 있습니다.

이 기능이 얼마나 중요한지 확인하기 위해 위의 예에서 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 값이 16으로 구성된 Standard\_D14 노드 풀을 고려해 보겠습니다. [CloudPool.TaskSchedulingPolicy][task_schedule]가 *Pack*인 [ComputeNodeFillType][fill_type]으로 구성된 경우, 각 노드의 모든 16개 코어의 사용량을 최대화하며 [풀 자동 크기 조정](./batch-automatic-scaling.md)을 통해 풀에서 사용되지 않는 노드(작업이 할당되지 않은 노드)를 솎아냄으로써 리소스 사용량을 최소화하고 비용을 절감합니다.

## Batch .NET 예

이 [Batch.NET][api_net] API 코드 스니펫은 노드당 최대 4개의 작업이 있는 4개의 대규모 노드를 포함하며, 작업을 풀 내 다른 노드에 할당하기 전에 각 노드마다 작업을 가득 채우는 작업 예약 정책을 지정하는 풀을 만드는 요청을 나타냅니다. Batch .NET API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [BatchClient.PoolOperations.CreatePool][poolcreate_net]을 참조하세요.

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Batch REST 예

이 [Batch REST][api_rest] API 스니펫은 노드당 최대 4개의 작업이 있는 두 대규모 노드를 포함하는 풀을 만드는 요청을 나타냅니다. REST API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [풀을 계정에 추가][maxtasks_rest]를 참조하세요.

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]`maxTasksPerNode` 요소 및 [MaxTasksPerComputeNode][maxtasks_net] 속성은 풀을 만들 때만 설정할 수 있습니다. 풀이 이미 만들어진 후에는 수정할 수 없습니다.

## 샘플 프로젝트 탐색

[CloudPool.MaxTasksPerComputeNode][maxtasks_net]의 사용을 보여주는 작업 코드 예인 GitHub의 [ParallelNodeTasks][parallel_tasks_sample] 프로젝트를 확인해 보세요. 이 C# 콘솔 응용 프로그램에서는 [Batch .NET][api_net] 라이브러리를 사용하여 하나 이상의 계산 노드가 있는 풀을 만들고 해당 노드에서 구성 가능한 작업 수를 실행하여 가변 부하를 시뮬레이션합니다. 어떤 노드가 어떤 작업을 실행했는지에 대한 응용 프로그램 상세 정보와, 작업 매개 변수 및 길이 등을 출력합니다. 아래는 예제 응용 프로그램의 두 가지 다른 실행에서의 출력을 요약하는 부분입니다.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

예제 응용 프로그램의 최초 실행에서는 노드당 한 작업을 기본값으로 사용하는 풀 내 단일 노드를 나타내며 작업 길이가 30분입니다.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

예제의 두번째 실행에서는 풀이 노드당 4개의 작업으로 구성되어 병렬 작업 실행이 가능하므로 거의 1/4시간에 작업을 완료할 수 있어 작업 시간이 크게 단축되었음을 보여줍니다.

> [AZURE.NOTE]위 요약의 작업 기간에는 풀 생성 시간이 포함되지 않았습니다. 위의 각 작업은 이전에 만든 풀에 제출되며, 제출 시점에 계산 노드는 *Idle* 상태입니다.

## 배치 탐색기 열 지도

Azure Batch [예제 응용 프로그램][github_samples] 중 하나인 [배치 탐색기][batch_explorer]에는 풀 내의 노드 코어 사용량을 가상화하여 제공하는 *열 지도* 기능이 있습니다. [ParallelTasks][parallel_tasks_sample] 예제 응용 프로그램을 실행할 때는 열 지도 기능을 사용하여 노드 코어 활동을 간편하게 가상화합니다.

![배치 탐색기 열 지도][1]

*각각 4개 코어가 있는 4개 노드를 나타내는 배치 탐색기 열 지도, 각 코어가 현재 작업을 실행 중임*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/ko-kr/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO1-->
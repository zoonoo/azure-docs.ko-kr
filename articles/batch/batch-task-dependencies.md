<properties
	pageTitle="Azure 배치의 태스크 종속성| Microsoft Azure"
	description="Azure 배치에서 MapReduce 스타일과 비슷한 빅 데이터 워크로드를 처리하기 위해 다른 태스크를 성공적으로 완료하는 데 종속된 태스크를 만듭니다."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/29/2016"
	ms.author="marsma" />

# Azure 배치의 태스크 종속성

클라우드에서 MapReduce 스타일의 컴퓨팅 워크로드를 처리하려는 경우 DAG(방향성 비순환 그래프)으로 표현할 수 있는 태스크의 데이터 처리 작업 또는 다운스트림 태스크가 업스트림 태스크의 출력에 따라 달라지는 다른 작업이 있습니다. 이 경우에 Azure 배치의 태스크 종속성은 솔루션이 될 수 있습니다.

이 기능을 사용하면 하나 이상의 다른 태스크를 성공적으로 완료한 후에 계산 노드에서 실행하기 위해 예약된 태스크를 만들 수 있습니다. 예를 들어, 별도의 병렬 태스크를 포함한 3D 동영상의 각 프레임을 렌더링하는 작업을 만들 수 있습니다. 해당 최종 작업인 "병합 태스크"는 전체 프레임이 성공적으로 렌더링된 경우에만 렌더링된 프레임을 완전한 영화로 함께 병합합니다.

일대일 또는 일대다 관계에서 다른 태스크에 따라 달라지는 태스크 또는 작업 ID의 특정 범위 내에서 작업 그룹이 완료되는 데 따라 태스크가 달라지는 범위 종속성을 만들 수 있습니다. 다대다 관계를 만들기 위해 다음 세 가지 기본 시나리오를 결합할 수 있습니다.

## 배치 .NET을 사용한 태스크 종속성

이 문서에서 [배치 .NET][net_msdn] 라이브러리를 사용하여 태스크 종속성을 구성하는 방법을 설명합니다. 먼저는 작업에서 [태스크 종속성을 사용](#enable-task-dependency)하는 방법을 보여 주고 [종속성을 사용하여 태스크를 구성](#create-dependent-tasks)하는 방법을 간략하게 설명합니다. 마지막으로 배치에서 지원되는 [종속성 시나리오](#dependency-scenarios)를 설명합니다.

## 태스크 종속성 사용

배치 응용 프로그램에서 태스크 종속성을 사용하려면 먼저 배치 서비스에 작업이 태스크 종속성을 사용한다고 알려야 합니다. 배치 .NET에서 해당 [UsesTaskDependencies][net_usestaskdependencies] 속성을 `true`으로 설정하여 [CloudJob][net_cloudjob]에서 이를 사용합니다.

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

위의 코드 조각에서 "batchClient"는 [BatchClient][net_batchclient] 클래스의 인스턴스입니다.

## 종속성 태스크 만들기

하나 이상의 태스크를 성공적으로 완료하는 데 종속된 태스크를 만들기 위해 배치에 태스크가 다른 태스크"에 종속"되었다고 알립니다. 배치 .NET에서 [TaskDependencies][net_taskdependencies] 클래스의 인스턴스를 사용하여 [CloudTask][net_cloudtask].[DependsOn][net_dependson] 속성을 구성합니다.

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

이 코드 조각은 "비"와 "태양"의 ID를 가진 태스크가 성공적으로 완료된 후에 계산 노드에서 실행되도록 예약된 "꽃"의 ID를 가진 태스크를 만듭니다.

 > [AZURE.NOTE] 태스크가 **완료** 상태이고 해당 **종료 코드**가 `0`인 경우 성공적으로 완료되었다고 간주됩니다. 즉, 배치 .NET에서 `Completed`의 [CloudTask][net_cloudtask].[State][net_taskstate] 속성 값 및 CloudTask의 [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] 속성 값은 `0`입니다.

## 종속성 시나리오

Azure 배치에서 사용할 수 있는 세 가지 기본 태스크 종속성 시나리오는 일대일, 일대다 및 태스크 ID 범위 종속성입니다. 네 번째 시나리오인 다대다를 제공하도록 결합될 수 있습니다.

 시나리오&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 예 | |
 :-------------------: | ------------------- | -------------------
 [일대일](#one-to-one) | *taskB*가 *taskA*에 종속됨 <p/> *taskB*는 *taskA*가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 | ![다이어그램: 일대일 태스크 종속성][1]
 [일대다](#one-to-many) | *taskC*가 *taskA* 및 *taskB* 모두에 종속됨 <p/> *taskC*는 *taskA* 및 *taskB*가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 | ![다이어그램: 일대다 태스크 종속성][2]
 [태스크 ID 범위](#task-id-range) | *taskD*가 태스크의 범위에 종속됨 <p/> *taskD*는 ID *1*-*10*을 가진 태스크가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 | ![다이어그램: 태스크 ID 범위 종속성][3]

>[AZURE.TIP] 태스크 C, D, E 및 F는 각각 태스크 A 및 B에 종속되는 경우 **다대다** 관계를 만들 수 있습니다. 예를 들어, 다운스트림 태스크가 여러 업스트림 태스크의 출력에 따라 달라지는 병렬화된 전처리 시나리오에서에서 유용합니다.

## 일대일

다른 하나의 태스크를 성공적으로 완료하는 데 종속성을 가진 태스크를 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 단일 태스크 ID를 [TaskDependencies][net_taskdependencies].[OnId][net_onid] 정적 메서드에 제공합니다.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

## 일대다

여러 태스크를 성공적으로 완료하는 데 종속성을 가진 태스크를 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 태스크 ID의 컬렉션을 [TaskDependencies][net_taskdependencies].[OnIds][net_onids] 정적 메서드에 제공합니다.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

## 태스크 ID 범위

ID가 범위 내에 있는 태스크 그룹을 성공적으로 완료하는 데 종속성을 가진 태스크를 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 범위의 첫 번째와 마지막 태스크 ID를 [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] 정적 메서드에 제공합니다.

>[AZURE.IMPORTANT] 종속성에 대한 태스크 ID 범위를 사용하는 경우 범위의 태스크 ID는 **정수 값**의 **문자열 표시***여야* 합니다. 또한 **범위의 모든 태스크**는 종속 태스크의 실행을 예약하도록 성공적으로 완료되어야 합니다.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## 코드 샘플

[TaskDependencies][github_taskdependencies] 샘플 프로젝트는 GitHub의 [Azure 배치 코드 샘플][github_samples] 중 하나입니다. 이 Visual Studio 2015 솔루션에서는 작업에 태스크 종속성을 사용하고 다른 태스크에 종속된 태스크를 만들며 계산 노드의 풀에서 해당 태스크를 실행하는 방법을 보여 줍니다.

## 다음 단계

### 응용 프로그램 배포

배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 계산 노드에서 태스크를 실행하는 응용 프로그램을 배포하고 버전을 관리하는 쉬운 방법을 제공합니다.

### 응용 프로그램 설치 및 데이터 준비

태스크를 실행하기 위해 노드를 준비하는 다양한 방법의 개요는 Azure 배치 포럼에서 [배치 계산 노드에서 응용 프로그램 설치 및 데이터 준비][forum_post] 게시물을 확인합니다. Azure 배치 팀 멤버 중 하나가 작성한 이 게시물은 계산 노드에 (응용 프로그램 및 태스크 입력 데이터를 모두 포함한) 파일을 가져오는 다른 방법에 대한 좋은 기초이며 각 메서드에 고려해야 할 특별한 고려 사항입니다.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "다이어그램: 일대일 종속성"
[2]: ./media/batch-task-dependency/02_one_to_many.png "다이어그램: 일대다 종속성"
[3]: ./media/batch-task-dependency/03_task_id_range.png "다이어그램: 태스크 ID 범위 종속성"

<!---HONumber=AcomDC_0629_2016-->
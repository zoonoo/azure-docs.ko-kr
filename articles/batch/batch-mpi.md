<properties
	pageTitle="다중 인스턴스 작업을 사용하여 Azure 배치에서 MPI 응용 프로그램 실행 | Microsoft Azure"
	description="Azure 배치에서 다중 인스턴스 작업 유형을 사용하여 MPI(메시지 전달 인터페이스) 응용 프로그램을 실행하는 방법에 대해 알아봅니다."
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
	ms.date="02/19/2016"
	ms.author="marsma" />

# 다중 인스턴스 작업을 사용하여 Azure 배치에서 MPI(메시지 전달 인터페이스) 응용 프로그램 실행

다중 인스턴스 작업과 함께 MPI(메시지 전달 인터페이스) 응용 프로그램과 같은 고성능 컴퓨팅 시나리오를 사용하도록 설정하는 동시에 여러 계산 노드에서 Azure 배치 작업을 실행할 수 있습니다. 이 문서에서 [배치 .NET][api_net] 라이브러리를 사용하여 다중 인스턴스 작업을 실행하는 방법을 알아봅니다.

## 다중 인스턴스 작업 개요

배치에서 각 태스크는 일반적으로 단일 계산 노드에서 실행됩니다. 작업에 여러 태스크를 제출하고 배치 서비스는 노드에서 실행을 위해 각 태스크를 예약합니다. 그러나 작업의 **다중 인스턴스 설정**을 구성하여 여러 노드에서 실행에 대해 작업을 하위 작업으로 분할하도록 배치에 지시할 수 있습니다.

![다중 인스턴스 작업 개요][1]

작업에 다중 인스턴스 설정을 사용하여 태스크를 제출할 때 배치는 다중 인스턴스 태스크에 고유한 몇 가지 단계를 수행합니다.

1. 배치 서비스는 작업을 하나의 **주** 및 많은 **하위 작업**으로 자동으로 분할합니다. 그런 다음 배치는 풀의 계산 노드에서 실행에 대한 주 및 하위 작업을 예약합니다.
2. 주 및 하위 작업의 이러한 작업은 다중 인스턴스 설정에서 지정하는 모든 **공용 리소스 파일**을 다운로드합니다.
3. 공용 리소스 파일을 다운로드한 후 다중 인스턴스 설정에 지정하는 **조정 명령**이 주 및 하위 작업에서 실행됩니다. 이 조정 명령은 일반적으로 백그라운드 서비스(예: [Microsoft MPI][msmpi_msdn]의 `smpd.exe`)를 시작하는 데 사용되고 노드가 노드 간 메시지를 처리할 준비가 되었는지 확인할 수도 있습니다.
4. 주 및 모든 하위 작업에서 조정 명령이 성공적으로 완료된 경우 다중 인스턴스 작업의 **명령줄**("응용 프로그램 명령")이 **주 작업**에서*만* 실행됩니다. 예를 들어 [MS-MPI][msmpi_msdn] 기반 솔루션에서 `mpiexec.exe`을(를) 사용하여 MPI 사용 응용 프로그램을 실행하는 위치입니다.

> [AZURE.NOTE] 기능적으로 고유하지만 “다중 인스턴스 작업"은 [StartTask][net_starttask] 또는 [JobPreparationTask][net_jobprep]과 같은 고유 작업 유형이 아닙니다. 다중 인스턴스 작업은 단순히 다중 인스턴스 설정이 구성된 표준 배치 작업(배치 .NET에서 [CloudTask][net_task])입니다. 이 문서에서는 이를 **다중 인스턴스 작업**이라고 합니다.

## 다중 인스턴스 작업에 대한 요구 사항

다중 인스턴스 작업은 **노드 간 통신이 활성화**되고 **동시 작업 실행이 비활성화**된 풀이 필요합니다. 노드 간 통신이 비활성화되었거나 *maxTasksPerNode* 값이 1보다 큰 풀에서 다중 인스턴스 작업을 실행하려는 경우 작업은 예약되지 않습니다. 무기한으로 "활성" 상태로 유지됩니다. 이 코드 조각은 배치 .NET 라이브러리를 사용하여 이러한 풀의 생성을 보여 줍니다.

```
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		osFamily: "4",
		virtualMachineSize: "small",
		targetDedicated: 3);

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

또한 다중 인스턴스 작업은 **2015년 12월 14일 이후에 만든 풀**의 노드에서*만* 실행합니다.

> [AZURE.TIP] 배치 풀에서 [A8 또는 A9 계산 노드](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) 크기를 사용하는 경우 MPI 응용 프로그램은 Azure의 고성능, 지연율이 낮은 RDMA(원격 직접 메모리 액세스) 네트워크를 활용할 수 있습니다. [클라우드 서비스에 적합한 크기](./../cloud-services/cloud-services-sizes-specs.md)에서 배치 풀에 사용할 수 있는 계산 노드 크기의 전체 목록을 볼 수 있습니다.

### MPI 응용 프로그램 설치에 StartTask 사용

다중 인스턴스 작업으로 MPI 응용 프로그램을 실행하려면 먼저 풀의 계산 노드에 MPI 소프트웨어를 가져와야 합니다. 노드가 풀에 연결되거나 다시 시작될 때마다 실행하는 [StartTask][net_starttask]를 사용하는 것이 좋습니다. 이 코드 조각은 [리소스 파일][net_resourcefile] 및 리소스 파일이 노드에 다운로드된 후 실행되는 명령줄로 MS-MPI 설치 패키지를 지정하는 StartTask를 만듭니다.

```
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] 배치에서 다중 인스턴스 작업을 사용하여 MPI 솔루션을 구현하는 경우 MS-MPI 사용에 제한되지 않습니다. 풀의 계산 노드에 대해 지정한 운영 체제와 호환되는 MPI 표준의 구현을 사용할 수 있습니다.

## 배치 .NET을 사용하여 다중 인스턴스 작업 만들기

이제 풀 요구 사항 및 MPI 패키지 설치를 다루었으며 다중 인스턴스 작업을 만들어 보겠습니다. 이 코드 조각에서 표준 [CloudTask][net_task]를 만든 다음 해당 [MultiInstanceSettings][net_multiinstance_prop] 속성을 구성합니다. 위에서 설명했듯이 다중 인스턴스 작업은 고유한 작업 유형이 아니지만 다중 인스턴스 설정으로 구성된 표준 배치 작업입니다.

```
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## 주 작업 및 하위 작업

작업에 대한 다중 인스턴스 설정을 만드는 경우 작업을 실행하는 계산 노드 수의 수를 지정합니다. 작업에 태스크를 제출하는 경우 배치 서비스는 지정한 노드 수와 일치하는 하나의 **주** 작업과 충분한 **하위 작업**을 만듭니다.

이러한 작업은 0에서 *numberOfInstances - 1*의 범위로 정수 ID가 할당됩니다. ID가 0인 작업은 주 작업이며 모든 다른 ID는 하위 작업입니다. 예를 들어 작업에 대한 다음과 같은 다중 인스턴스 설정을 만드는 경우 주 작업은 0의 ID를 가지며 하위 작업은 1~9의 ID를 가집니다.

```
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## 조정 및 응용 프로그램 명령

**조정 명령**은 주 및 하위 작업에서 실행됩니다. 주 작업 및 모든 하위 작업이 조정 명령 실행을 마치면 다중 인스턴스 작업의 명령줄이 주 작업에서*만* 실행됩니다. 이 명령줄을 조정 명령과 구분하도록 **응용 프로그램 명령**이라고 합니다.

조정 명령의 호출을 차단합니다. 배치는 조정 명령이 모든 하위 작업에 대해 성공적으로 반환될 때까지 응용 프로그램 명령을 실행하지 않습니다. 따라서 조정 명령은 모든 필요한 백그라운드 서비스를 시작하고 사용할 준비가 되었는지 확인한 다음 종료해야 합니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 이 조정 명령은 노드에서 SMPD 서비스를 시작한 다음 종료합니다.

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

이 조정 명령에서 `start`을(를) 사용합니다. `smpd.exe` 응용 프로그램은 실행 후 즉시 반환하지 않으므로 필요합니다. [시작][cmd_start] 명령을 사용하지 않고 이 조정 명령은 반환하지 않으며 따라서 응용 프로그램 명령의 실행을 차단합니다.

**응용 프로그램 명령**, 다중 인스턴스 작업에 대해 지정된 명령줄은 주 작업에서*만* 실행됩니다. MS-MPI 응용 프로그램의 경우 `mpiexec.exe`을(를) 사용하는 MPI 사용 응용 프로그램의 실행이 됩니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 응용 프로그램 명령은 다음과 같습니다.

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

## 리소스 파일

다중 인스턴스 작업에 대해 고려해야 할 리소스 파일의 두 집합: *모든* 작업(주 및 하위 작업)이 다운로드하는 **공용 리소스 파일** 및 *주 작업에서만* 다운로드하는 다중 인스턴스 작업 자체에 지정된 **리소스 파일**입니다.

작업에 대해 다중 인스턴스 설정에서 하나 이상의 **공용 리소스 파일**을 지정할 수 있습니다. 이러한 공용 리소스 파일은 주 및 모든 하위 작업에 의해 [Azure 저장소](./../storage/storage-introduction.md)에서 각 노드의 작업 공유 디렉터리로 다운로드됩니다. `AZ_BATCH_TASK_SHARED_DIR` 환경 변수를 사용하여 응용 프로그램 및 조정 명령줄에서 작업 공유 디렉터리에 액세스할 수 있습니다.

다중 인스턴스 작업 자체에 대해 지정한 리소스 파일은 주 작업에 의해서*만* 작업의 작업 디렉터리, `AZ_BATCH_TASK_WORKING_DIR`에 다운로드됩니다. 하위 작업은 다중 인스턴스 작업에 대해 지정된 리소스 파일을 다운로드하지 않습니다.

`AZ_BATCH_TASK_SHARED_DIR`의 콘텐츠는 노드에서 실행하는 주 및 모든 하위 작업에서 액세스할 수 있습니다. 예제 작업 공유 디렉터리는 `tasks/mybatchjob/job-1/mymultiinstancetask/`입니다. 주 및 각 하위 작업은 또한 해당 작업에서만 액세스할 수 있고 환경 변수 `AZ_BATCH_TASK_WORKING_DIR`을(를) 사용하여 액세스하는 작업 디렉터리를 가집니다.

이 문서의 코드 샘플에서는 다중 인스턴스 작업 자체에 대한, 풀의 StartTask에 대해서만, 다중 인스턴스 설정의 [CommonResourceFiles][net_multiinsance_commonresfiles]에 대한 리소스 파일을 지정하지 않습니다.

> [AZURE.IMPORTANT] 명령줄에서 이러한 디렉터리를 나타내는 환경 변수 `AZ_BATCH_TASK_SHARED_DIR` 및 `AZ_BATCH_TASK_WORKING_DIR`을(를) 항상 사용합니다. 경로를 수동으로 구성하지 마세요.

## 작업 수명

주 작업의 수명은 전체 다중 인스턴스 작업의 수명을 제어합니다. 주 작업이 종료될 때 모든 하위 작업이 종료됩니다. 주 작업의 종료 코드는 작업의 종료 코드이며 따라서 재시도 목적에 대한 작업의 성공 여부를 결정하는 데 사용됩니다.

하위 작업 중 하나라도 실패하는 경우(예: 0이 아닌 반환 코드와 함께 종료) 전체 다중 인스턴스 작업이 실패합니다. 다중 인스턴스 작업은 종료되고 해당 다시 시도 한계까지 다시 시도됩니다.

다중 인스턴스 작업을 삭제하는 경우 주 및 모든 하위 작업도 배치 서비스에서 삭제됩니다. 모든 하위 작업 디렉터리 및 해당 파일은 표준 작업의 경우처럼 계산 노드에서 삭제됩니다.

[MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] 및 [RetentionTime][net_taskconstraint_retention] 속성과 같은 다중 인스턴스 작업에 대한 [TaskConstraints][net_taskconstraints]는 표준 작업에 대한 것이므로 적용되고 주 및 모든 하위 작업에 적용됩니다. 그러나 다중 인스턴스 태스크를 작업에 추가한 후 [RetentionTime][net_taskconstraint_retention] 속성을 변경하는 경우 이 변경은 주 작업에만 적용됩니다. 모든 하위 작업은 원래 [RetentionTime][net_taskconstraint_retention]을 계속해서 사용합니다.

계산 노드의 최근 작업 목록은 최근 작업이 다중 인스턴스 작업의 일부일 경우 하위 작업의 ID를 반영합니다.

## 하위 작업에 대한 정보 가져오기

배치 .NET 라이브러리를 사용하여 하위 작업에 대한 정보를 가져오려면 [CloudTask.ListSubtasks][net_task_listsubtasks] 메서드를 호출합니다. 이 메서드는 모든 하위 작업에 대한 정보 및 작업을 실행하는 계산 노드에 대한 정보를 반환합니다. 이 정보로부터 각 하위 작업의 루트 디렉터리, 풀 ID, 현재 상태, 종료 코드 등을 확인할 수 있습니다. [PoolOperations.GetNodeFile][poolops_getnodefile] 메서드와 함께 이 정보를 사용하여 하위 작업의 파일을 가져올 수 있습니다. 이 메서드는 주 작업(ID 0)에 대한 정보를 반환하지 않습니다.

> [AZURE.NOTE] 별도로 언급하지 않는 한 다중 인스턴스 [CloudTask][net_task] 자체에서 작동하는 배치 .NET 메서드는 주 작업에*만* 적용됩니다. 예를 들어 다중 인스턴스 작업에서 [CloudTask.ListNodeFiles][net_task_listnodefiles] 메서드를 호출하는 경우 주 작업의 파일만 반환됩니다.

다음 코드 조각은 하위 작업 정보를 얻는 방법 뿐만 아니라 실행되는 노드에서 파일 콘텐츠를 요청하는 방법을 보여 줍니다.

```
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## 다음 단계

- 배치에서 다중 인스턴스 작업을 테스트하는 동안 사용하도록 간단한 MS-MPI 응용 프로그램을 빌드할 수 있습니다. Microsoft HPC 및 Azure 배치 팀 블로그 기사, [간단한 MS-MPI 프로그램을 컴파일 및 실행하는 방법][msmpi_howto]은 MS-MPI를 사용하여 간단한 MPI 응용 프로그램을 만들기 위한 단계를 포함합니다.

- MSDN의 [Microsoft MPI][msmpi_msdn] 페이지에서 MS-MPI에 대한 최신 정보를 확인하세요.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "다중 인스턴스 개요"

<!---HONumber=AcomDC_0323_2016-->
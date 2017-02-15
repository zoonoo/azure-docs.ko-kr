---
title: "Azure 배치의 작업 및 태스크 지속성 | Microsoft Docs"
description: "Azure Storage를 배치 태스크 및 작업 출력의 영구 저장소로 사용하고 이 보관된 출력을 Azure 포털에서 표시하도록 설정하는 방법을 알아봅니다."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/05/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dfcf1e1d54a0c04cacffb50eca4afd39c6f6a1b1
ms.openlocfilehash: 1ae4ee2e8728ac8bcbc1dc528eb76d11a2f3d8a3


---
# <a name="persist-azure-batch-job-and-task-output"></a>Azure 배치 작업 및 태스크 출력 보관
배치에서 실행하는 태스크는 일반적으로 저장한 후 나중에 작업의 다른 태스크, 작업을 실행한 클라이언트 응용 프로그램 또는 둘 다에서 검색해야 하는 출력을 생성합니다. 이 출력은 입력 데이터 또는 태스크 실행과 관련된 로그 파일을 처리하여 생성되는 파일일 수 있습니다. 이 문서에서는 규칙 기반 기술을 사용하여 이러한 태스크 출력을 Azure Blob 저장소에 보관함으로써 풀, 작업 및 계산 노드를 삭제한 후에도 사용할 수 있도록 하는 .NET 클래스 라이브러리를 소개합니다.

이 문서의 기술을 사용하면 [Azure Portal][portal]에서 **저장된 출력 파일** 및 **저장된 로그**에서 태스크 출력을 볼 수도 있습니다.

![포털의 저장된 출력 파일 및 저장된 로그 선택기][1]

> [!NOTE]
> 이 문서에 설명된 [Azure 배치 파일 규칙][nuget_package] .NET 클래스 라이브러리는 현재 미리 보기 상태입니다. 여기서 설명하는 기능의 몇 가지는 일반 공급 전에 변경될 수 있습니다.
> 
> 

## <a name="task-output-considerations"></a>태스크 출력 고려 사항
배치 솔루션을 디자인할 때 작업 및 태스크 출력과 관련된 몇 가지 요소를 고려해야 합니다.

* **계산 노드 수명**: 계산 노드는 특히 자동 크기 조정 가능한 풀에서 일시적인 경우가 많습니다. 노드에서 실행되는 태스크의 출력은 노드가 존재하는 동안 해당 태스크에 대해 설정한 파일 보존 기간 내에서만 사용할 수 있습니다. 따라서 태스크 출력을 보관하려면 태스크에서 해당 출력 파일을 영구 저장소(예: Azure Storage)에 업로드해야 합니다.
* **출력 저장**: 태스크 출력 데이터를 영구 저장소에 보관하려면 태스크 코드에서 [Azure Storage SDK](../storage/storage-dotnet-how-to-use-blobs.md) 를 사용하여 태스크 출력을 Blob 저장소 컨테이너에 업로드하면 됩니다. 컨테이너 및 파일 명명 규칙을 구현한 경우 클라이언트 응용 프로그램 또는 작업의 다른 태스크에서 규칙에 따라 이 출력을 찾아 다운로드할 수 있습니다.
* **출력 검색**: Azure Storage(태스크에서 출력을 보관하는 경우) 또는 풀의 계산 노드에서 직접 태스크 출력을 검색할 수 있습니다. 태스크의 출력을 계산 노드에서 직접 검색하려면 파일 이름과 노드에서의 해당 출력 위치가 필요합니다. Azure Storage에 출력을 보관하는 경우 다운스트림 태스크 또는 클라이언트 응용 프로그램에서 Azure Storage SDK를 사용하여 Azure Storage의 파일을 다운로드하려면 이 파일의 전체 경로를 알아야 합니다.
* **출력 보기**: Azure Portal에서 배치 태스크로 이동하여 **노드의 파일**을 선택한 경우 관심 있는 출력 파일만 표시되는 것이 아니라 태스크와 연관된 모든 파일이 표시됩니다. 계산 노드의 파일은 노드가 존재하는 동안 해당 태스크에 대해 설정한 파일 보존 기간 내에서만 사용할 수 있습니다. 포털 또는 [Azure Storage 탐색기][storage_explorer]와 같은 응용 프로그램에서 Azure Storage에 보관한 태스크 출력을 보려면 해당 위치를 알아야 하며 파일로 직접 이동해야 합니다.

## <a name="help-for-persisted-output"></a>보관된 출력에 대한 도움말
작업 및 태스크 출력을 보다 쉽게 보관할 수 있도록 도와주기 위해 배치 팀은 배치 응용 프로그램에서 사용할 수 있는 [Azure 배치 파일 규칙][nuget_package] 라이브러리인 .NET 클래스 라이브러리 외에 명명 규칙 집합을 정의하고 구현했습니다. 또한 Azure 포털에서 이러한 명명 규칙을 알고 있으므로 라이브러리를 사용하여 저장한 파일을 쉽게 찾을 수 있습니다.

## <a name="using-the-file-conventions-library"></a>파일 규칙 라이브러리 사용
[Azure 배치 파일 규칙][nuget_package]은 배치 .NET 응용 프로그램에서 Azure Storage에 태스크 출력을 쉽게 저장하고 검색하는 데 사용할 수 있는 .NET 클래스 라이브러리입니다. 이 규칙은 태스크 코드와 클라이언트 코드 모두에서 사용할 수 있습니다. 즉, 태스크 코드에서는 파일을 보관하고, 클라이언트 코드에서는 파일을 나열 및 검색할 수 있습니다. 또한 태스크에서 라이브러리를 사용하여 [태스크 종속성](batch-task-dependencies.md) 시나리오 등에서 업스트림 태스크의 출력을 검색할 수 있습니다.

규칙 라이브러리는 저장소 컨테이너 및 태스크 출력 파일이 규칙에 따라 명명되고 Azure Storage에 보관될 때 올바른 위치에 업로드되도록 합니다. 출력을 검색할 때 파일 이름이나 저장소 내 위치를 몰라도 ID 및 용도별로 출력을 나열하거나 검색하여 지정된 작업 또는 태스크의 출력을 쉽게 찾을 수 있습니다.

예를 들어 파일 이름이나 저장소 계정 내의 위치를 몰라도 라이브러리를 사용하여 "태스크 7의 모든 중간 파일을 나열"하거나 " *mymovie*작업에 대한 미리 보기를 표시"할 수 있습니다.

### <a name="get-the-library"></a>라이브러리 가져오기
[NuGet][nuget_package]에서 새 클래스를 포함하고 [CloudJob][net_cloudjob] 및 [CloudTask][net_cloudtask] 클래스를 새 메서드로 확장하는 라이브러리를 가져올 수 있습니다. [NuGet 라이브러리 패키지 관리자][nuget_manager]를 사용하여 Visual Studio 프로젝트에 이를 추가할 수 있습니다.

> [!TIP]
> Azure 배치 파일 규칙 라이브러리의 [소스 코드][github_file_conventions]는 GitHub의 Microsoft Azure SDK for .NET 리포지토리에 있습니다.
> 
> 

## <a name="requirement-linked-storage-account"></a>요구 사항: 연결된 저장소 계정
파일 규칙 라이브러리를 사용하여 영구 저장소에 출력을 저장하고 Azure 포털에서 이를 보려면 배치 계정에 [Azure Storage 계정을 연결](batch-application-packages.md#link-a-storage-account) 해야 합니다. 아직 연결하지 않은 경우 Azure 포털을 사용하여 저장소 계정을 배치 계정에 연결합니다.

**배치 계정** 블레이드 > **설정** > **저장소 계정** > **저장소 계정**(없음) > 구독에서 저장소 계정을 선택합니다.

저장소 계정 연결에 대한 자세한 단계별 설명은 [Azure 배치 응용 프로그램 패키지를 사용하여 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.

## <a name="persist-output"></a>출력 보관
파일 규칙 라이브러리를 사용하여 작업 및 태스크 출력을 저장할 때 수행할 기본 동작에는 저장소 컨테이너 만들기와 컨테이너에 출력 저장이라는 두 가지 동작이 있습니다.

> [!WARNING]
> 모든 작업 및 태스크 출력은 동일한 컨테이너에 저장되므로 많은 수의 태스크에서 동시에 파일을 보관하려는 경우 [저장소 제한 한도](../storage/storage-performance-checklist.md#blobs) 가 적용될 수 있습니다.
> 
> 

### <a name="create-storage-container"></a>저장소 컨테이너 만들기
태스크에서 저장소에 출력을 보관하려면 먼저 출력을 업로드할 Blob 저장소 컨테이너를 만들어야 합니다. [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]를 호출하여 이 작업을 수행합니다. 이 확장 메서드는 [CloudStorageAccount][net_cloudstorageaccount] 개체를 매개 변수로 가져와 이 문서의 뒷부분에 설명된 검색 방법 및 Azure Portal에서 해당 콘텐츠를 검색할 수 있는 방식으로 명명된 컨테이너를 만듭니다.

일반적으로 클라이언트 응용 프로그램, 즉 풀, 작업 및 태스크를 만드는 응용 프로그램에 이 코드를 배치합니다.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>태스크 출력 저장
Blob Storage에 컨테이너를 준비했으므로 이제 태스크에서 파일 규칙 라이브러리에 있는 [TaskOutputStorage][net_taskoutputstorage] 클래스를 사용하여 컨테이너에 출력을 저장할 수 있습니다.

태스크 코드에서 먼저 [TaskOutputStorage][net_taskoutputstorage] 개체를 만든 다음 태스크가 해당 작업을 완료하면 [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] 메서드를 호출하여 Azure Storage에 출력을 저장합니다.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

"output kind" 매개 변수는 보관된 파일을 분류합니다. [TaskOutputKind][net_taskoutputkind] 유형에는 "TaskOutput", "TaskPreview", "TaskLog" 및 "TaskIntermediate"라는 미리 정의된 네 가지 형식이 있습니다. 워크플로에서 유용한 경우 사용자 지정 종류를 정의할 수도 있습니다.

이러한 출력 형식을 사용하면 나중에 지정된 태스크의 보관된 출력에 대해 배치를 쿼리할 때 나열한 출력 형식을 지정할 수 있습니다. 즉, 태스크에 대한 출력을 나열할 때 출력 형식 중 하나로 목록을 필터링할 수 있습니다. 예를 들어 "Give me the *preview* output for task *109*"와 같이 지정할 수 있습니다. 출력 나열 및 검색에 대한 보다 자세한 내용은 이 문서의 뒷부분에 있는 [출력 검색](#retrieve-output) 에서 확인할 수 있습니다.

> [!TIP]
> 출력 종류는 Azure Portal에서 특정 파일이 표시되는 위치도 지정합니다. 즉, *TaskOutput* 분류 파일은 "태스크 출력 파일"에 나타나고, *TaskLog* 파일은 "태스크 로그"에 나타납니다.
> 
> 

### <a name="store-job-outputs"></a>작업 출력 저장
태스크 출력 외에 전체 작업과 연관된 출력도 저장할 수 있습니다. 예를 들어 영화 렌더링 작업의 병합 태스크에서 완전히 렌더링된 영화를 작업 출력으로 보관할 수 있습니다. 작업이 완료되면 클라이언트 응용 프로그램에서 개별 태스크를 쿼리할 필요 없이 작업의 출력을 나열하고 검색할 수 있습니다.

[JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] 메서드를 호출하여 작업 출력을 저장하고 [JobOutputKind][net_joboutputkind] 및 파일 이름을 지정합니다.

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

태스크 출력에 대한 TaskOutputKind와 마찬가지로 [JobOutputKind][net_joboutputkind] 매개 변수를 사용하여 작업의 보관된 파일을 분류할 수 있습니다. 이 매개 변수를 통해 나중에 특정 출력 형식(목록)을 쿼리할 수 있습니다. JobOutputKind는 출력 및 미리 보기 형식을 둘 다 포함하며, 사용자 지정 형식 만들기를 지원합니다.

### <a name="store-task-logs"></a>태스크 로그 저장
태스크 또는 작업이 완료된 경우 영구 저장소에 파일을 보관하는 것 외에 태스크를 실행하는 동안 업데이트된 파일(예: 로그 파일 또는 `stdout.txt` 및 `stderr.txt`)을 보관해야 할 수도 있습니다. 이를 위해 Azure 배치 파일 규칙 라이브러리에서는 [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync] 메서드를 제공합니다. [SaveTrackedAsync][net_savetrackedasync]를 사용하여 노드에서 파일 업데이트를 추적(지정한 간격으로)하고 이러한 업데이트를 Azure Storage에 보관할 수 있습니다.

다음 코드 조각에서는 [SaveTrackedAsync][net_savetrackedasync]를 사용하여 태스크가 실행되는 동안 15초마다 Azure Storage에서 `stdout.txt`를 업데이트합니다.

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)` 은 태스크에서 일반적으로 수행하는 코드에 대한 자리 표시자입니다. 예를 들어 Azure Storage에서 데이터를 다운로드하고 변환 또는 계산을 수행하는 코드가 있을 수 있습니다. 이 조각의 중요한 부분은 [SaveTrackedAsync][net_savetrackedasync]를 통해 `using` 블록의 이러한 코드를 래핑하여 파일을 주기적으로 업데이트할 수 있는 방법을 보여 주고 있습니다.

노드 에이전트가 표준 출력의 내용을 노드의 stdout.txt 파일로 플러시하는 시간을 확보하도록 하기 위해 이 `using` 블록 끝에 `Task.Delay`가 필요합니다(노드 에이전트는 풀의 각 노드에서 실행되는 프로그램이며 노드와 배치 서비스 간에 명령 및 컨트롤 인터페이스를 제공합니다). 이 지연 시간이 없다면 마지막 몇 초의 출력을 놓칠 수 있습니다. 이 지연 시간은 일부 파일에만 필요할 수 있습니다.

> [!NOTE]
> SaveTrackedAsync를 사용하여 파일 추적을 사용하도록 설정한 경우 추적된 파일의 *추가 항목*만 Azure Storage에 보관됩니다. 이 방법은 순환하지 않는 로그 파일 또는 추가된 다른 파일을 추적하는 데에만 사용해야 합니다. 즉, 데이터는 업데이트 시 파일 끝에만 추가됩니다.
> 
> 

## <a name="retrieve-output"></a>출력 검색
Azure 배치 파일 규칙 라이브러리를 사용하여 보관된 출력을 검색할 때는 태스크 및 작업 중심 방식으로 검색합니다. Blob 저장소 내 경로 또는 파일 이름을 몰라도 지정된 태스크 또는 작업의 출력을 요청할 수 있습니다. "Give me the output files for task *109*"라고 지정하면 됩니다.

다음 코드 조각은 모든 작업의 태스크를 반복하여 태스크의 출력 파일에 대한 일부 정보를 인쇄한 다음 Storage에서 해당 파일을 다운로드합니다.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>태스크 출력 및 Azure 포털
Azure Portal에서는 [Azure 배치 파일 규칙 추가 정보][github_file_conventions_readme]에 있는 명명 규칙을 사용하여 연결된 Azure Storage 계정에 보관된 태스크 출력 및 로그를 표시합니다. 이러한 규칙을 원하는 언어로 직접 구현하거나, .NET 응용 프로그램의 파일 규칙 라이브러리를 사용할 수 있습니다.

### <a name="enable-portal-display"></a>포털 표시 사용
포털에서 출력을 표시하려면 다음 요구 사항을 충족해야 합니다.

1. [Link an Azure Storage account](#requirement-linked-storage-account) 해야 합니다.
2. 출력을 보관할 때 저장소 컨테이너 및 파일에 대한 미리 정의된 명명 규칙을 따릅니다. 파일 규칙 라이브러리 [추가 정보][github_file_conventions_readme]에서 이러한 규칙에 대한 정의를 확인할 수 있습니다. [Azure 배치 파일 규칙][nuget_package] 라이브러리를 사용하여 출력을 보관하는 경우에는 이 요구 사항이 충족됩니다.

### <a name="view-outputs-in-the-portal"></a>포털에서 출력 보기
Azure Portal에서 태스크 출력 및 로그를 보려면 관심 있는 출력의 태스크로 이동하여 **저장된 출력 파일** 또는 **저장된 로그**를 클릭합니다. 이 이미지는 ID가 "007"인 태스크에 대한 **저장된 출력 파일** 을 보여 줍니다.

![Azure Portal의 태스크 출력 블레이드][2]

## <a name="code-sample"></a>코드 샘플
[PersistOutputs][github_persistoutputs] 샘플 프로젝트는 GitHub의 [Azure 배치 코드 샘플][github_samples] 중 하나입니다. 이 Visual Studio 2015 솔루션은 Azure 배치 파일 규칙 라이브러리를 사용하여 영구 저장소에 태스크 출력을 보관하는 방법을 보여 줍니다. 샘플을 실행하려면 다음 단계를 수행합니다.

1. **Visual Studio 2015**에서 프로젝트를 엽니다.
2. Microsoft.Azure.Batch.Samples.Common 프로젝트에서 배치 및 저장소 **계정 자격 증명**을 **AccountSettings.settings**에 추가합니다.
3. **빌드** 합니다(하지만 실행하지 않음). 메시지가 표시되면 모든 NuGet 패키지를 복원합니다.
4. Azure 포털을 사용하여 [PersistOutputsTask](batch-application-packages.md) 에 대한 **응용 프로그램 패키지**를 업로드합니다. `PersistOutputsTask.exe` 및 종속 어셈블리를 .zip 패키지에 포함하고, 응용 프로그램 ID를 "PersistOutputsTask"로, 응용 프로그램 패키지 버전을 "1.0"으로 설정합니다.
5. **PersistOutputs** 프로젝트를 **시작**(실행)합니다.

## <a name="next-steps"></a>다음 단계
### <a name="application-deployment"></a>응용 프로그램 배포
배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 계산 노드에서 태스크를 실행하는 응용 프로그램을 배포하고 버전을 관리하는 쉬운 방법을 제공합니다.

### <a name="installing-applications-and-staging-data"></a>응용 프로그램 설치 및 데이터 준비
태스크를 실행하기 위해 노드를 준비하는 다양한 방법의 개요는 Azure 배치 포럼에서 [배치 계산 노드에서 응용 프로그램 설치 및 데이터 스테이징][forum_post] 게시물을 확인합니다. Azure 배치 팀 멤버 중 하나가 작성한 이 게시물은 계산 노드에 (응용 프로그램 및 태스크 입력 데이터를 모두 포함한) 파일을 가져오는 다른 방법에 대한 좋은 기초이며 각 메서드에 대한 특별한 고려 사항입니다.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "포털의 저장된 출력 파일 및 저장된 로그 선택기"
[2]: ./media/batch-task-output/task-output-02.png "Azure Portal의 태스크 출력 블레이드"



<!--HONumber=Dec16_HO2-->



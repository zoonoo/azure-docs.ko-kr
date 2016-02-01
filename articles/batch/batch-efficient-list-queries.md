<properties
	pageTitle="Azure 일괄 처리의 효율적인 목록 쿼리 | Microsoft Azure"
	description="풀, 작업, 태스크, 계산 노드 등과 같은 Azure 배치 엔터티를 쿼리할 때 반환되는 데이터의 규모를 줄이면 성능이 향상됩니다."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# 효율적인 Azure 배치 서비스 쿼리

이 문서에서는 [배치 .NET][api_net] API를 사용하여 작업, 태스크, 계산 노드 등의 목록을 얻기 위해 Azure 배치 서비스를 쿼리할 때 반환되는 데이터의 규모와 항목 수를 줄이는 방법을 학습합니다.

Azure 배치는 큰 계산 기능을 제공하며 프로덕션 환경에서 작업, 태스크 및 계산 노드 같은 엔터티가 수천 개 있을 수 있습니다. 이러한 항목에 대한 정보를 얻으면 각 쿼리에 대해 전송해야 하는 많은 양의 데이터가 생성될 수 있습니다. 항목의 수와 각 항목에 대해 반환되는 정보의 형식을 제한하여 쿼리의 속도 및 응용 프로그램의 성능을 높일 수 있습니다.

작업, 태스크, 계산 노드 목록은 Azure 배치를 사용하는 거의 모든 응용 프로그램이 종종 수행해야 하는 작업의 예입니다. 모니터링은 일반적인 사용 사례입니다. 예를 들어, 풀의 용량 및 상태를 확인하려면 해당 풀의 모든 계산 노드를 쿼리할 수 있어야 합니다. 또 다른 예는 작업의 태스크가 여전히 큐에 대기 중인지 확인하기 위해 작업의 태스크에 대해 쿼리하는 것입니다.

이 [배치 .NET][api_net] API 코드 조각은 해당 태스크의 모든 속성 집합과 함께 작업과 관련한 모든 태스크를 검색합니다.

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

그러나 훨씬 더 효율적인 목록 쿼리를 수행할 수 있습니다. [JobOperations.ListTasks][net_list_tasks] 메서드에 [ODATADetailLevel][odata] 개체를 제공하여 이를 수행할 수 있습니다. 이 코드 조각은 완료된 태스크의 ID, 명령줄 및 계산 노드 정보 속성만 반환합니다.

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

위의 예제 시나리오에서 작업에 수천 개의 태스크가 있다면 두 번째 쿼리의 결과는 보통 첫 번째보다 더 빠르게 반환됩니다. 배치 .NET API를 사용하여 항목을 나열할 때 ODATADetailLevel 사용에 대한 자세한 내용은 아래에 포함되어 있습니다.

> [AZURE.IMPORTANT]응용 프로그램의 최고 효율성과 성능을 위해 항상 .NET API 목록 호출에 ODATADetailLevel 개체를 제공하는 것이 좋습니다. 세부 정보 수준을 지정하여 배치 서비스 응답 시간을 낮추고, 네트워크 사용률을 개선하며 클라이언트 응용 프로그램의 메모리 사용을 최소화하는 데 도움이 될 수 있습니다.

## 효율적인 쿼리를 위한 도구

[배치 .NET][api_net] 및 [Batch REST][api_rest] API는 목록에 반환되는 항목 수와 각각에 대해 반환되는 정보의 크기를 줄일 수 있습니다. 목록 쿼리를 수행할 때 filter, select 및 expand 문자열을 지정하여 API를 통해 이를 수행할 수 있습니다.

- **Filter**--filter 문자열은 반환되는 항목 수를 줄이는 식입니다. 예를 들어, 한 작업에 대해 실행 중인 태스크만 나열하거나 태스크를 실행할 준비가 된 계산 노드만 나열할 수 있습니다.
  - filter 문자열은 하나 이상의 식으로 구성된 문자열로 구성됩니다. 식은 속성 이름, 연산자, 값으로 이루어져 있습니다. 각 속성에 대해 지원되는 연산자의 경우처럼 지정할 수 있는 속성은 API 호출 유형마다 고유합니다.
  - 논리 연산자 `and` 및 `or`를 사용하여 여러 식을 결합할 수 있습니다.
  - 실행 중인 렌더링 태스크만 나열하는 filter 문자열 예: `startswith(id, 'renderTask') and (state eq 'running')`
- **Select**--select 문자열은 각 항목에 대해 반환되는 속성 값을 제한합니다. 항목에 대한 속성 목록을 select 문자열에 지정할 수 있습니다. 그런 다음 목록 쿼리 결과의 각 항목에 대해 해당 속성 값만 반환됩니다.
  - select 문자열은 속성 이름을 쉼표로 구분한 목록으로 구성됩니다. 목록 작업에서 반환된 항목의 모든 속성은 지정할 수 있습니다.
  - 각 태스크에 대해 3가지 속성만 반환하도록 지정하는 select 문자열 예: `id, state, stateTransitionTime`
- **Expand**--expand 문자열은 특정 정보를 얻는 데 필요한 API 호출 수를 줄입니다. 목록을 얻은 다음 목록의 각 항목에 대한 호출을 수행하는 것과는 달리, 단일 목록 API 호출을 통해 각 목록 항목에 대한 자세한 정보를 얻을 수 있습니다.
  - Select 문자열과 마찬가지로 Expand 문자열은 목록 쿼리 결과에서 특정 데이터의 포함 여부를 제어합니다.
  - expand 문자열은 작업 목록, 작업 일정, 태스크 및 풀에서 사용되는 경우에만 지원됩니다. 현재 통계 정보만 지원합니다.
  - 각 항목에 대해 반환할 통계 정보를 지정하는 expand 문자열 예: `stats`
  - 모든 속성이 필요하며 Select 문자열을 지정하지 않은 경우, 통계 정보를 가져오려면 Expand 문자열을 사용해야 합니다. select 문자열을 사용하여 속성 하위 집합을 가져올 경우 select 문자열에서 `stats`를 지정할 수 있으며 expand 문자열을 지정하지 않아도 됩니다.

> [AZURE.NOTE]3가지 쿼리 문자열 유형(filter, select, expand) 중 하나를 구성할 때는 해당 REST API 요소의 항목과 속성 이름과 대소문자가 일치해야 합니다. 예를 들어 .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) 클래스로 작업하는 경우 .NET 속성이 [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state)이더라도 **State** 대신 **state**를 지정해야 합니다. .NET 및 REST API 간의 속성 매핑은 아래 표를 참조하세요.

### Filter, Select, Expand 문자열 사양

- filter, select, expand 문자열에서 지정한 속성은 [Batch REST][api_rest] API에 표시되는 속성 이름과 일치하며 [배치 .NET][api_net] 라이브러리를 사용하는 경우에도 마찬가지입니다.
- 모든 속성 이름은 대/소문자를 구분하지만, 속성 값은 대/소문자를 구분하지 않습니다.
- 날짜/시간 문자열은 두 형식 중 하나가 될 수 있으며 `DateTime`으로 시작해야 합니다.
  - W3C-DTF 형식 예: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 형식 예: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- 부울 문자열은 `true` 또는 `false`입니다.
- 잘못된 속성이나 연산자를 지정한 경우 `400 (Bad Request)` 오류가 발생합니다.

## 배치 .NET에서 효율적인 쿼리

[배치 .NET][api_net] API 안에서 [ODATADetailLevel][odata] 클래스를 filter, select 및 expand 문자열에 사용하여 작업을 나열합니다. ODataDetailLevel 개체에는 생성자 내에 지정되어 있거나 직접 설정된 3개의 공용 문자열 속성이 있습니다. 이 개체는 [ListPools][net_list_pools], [ListJobs][net_list_jobs] 및 [ListTasks][net_list_tasks]와 같은 다양한 목록 작업에 매개 변수로 전달됩니다.

- [ODATADetailLevel.FilterClause][odata_filter]–-반환되는 항목 수를 제한합니다.
- [ODATADetailLevel.SelectClause][odata_select]-–각 항목에 반환된 속성 값의 하위 집합을 지정합니다.
- [ODATADetailLevel.ExpandClause][odata_expand]–-각각에 대한 호출을 실행하는 대신 단일 API 호출에서 항목 데이터를 검색합니다.

다음 코드 조각에서는 풀의 특정 집합에 대한 통계에 대해 배치 서비스를 효율적으로 쿼리하기 위해 배치 .NET API를 사용합니다. 이 시나리오에서 배치 사용자는 테스트 및 프로덕션 풀을 가집니다. 테스트 풀 ID는 "test"를 접두사로 사용하고 프로덕션 풀 ID는 "prod"를 접두사로 사용합니다. 이 코드 조각에서 myBatchClient는 다음과 같은 [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) 인스턴스를 적절하게 초기화합니다.

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties that are returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data that is returned by specifying the
	// detail level that we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Select 및 Expand 절로 구성된 [ODATADetailLevel][odata]의 인스턴스는 반환되는 데이터의 양을 제한하기 위해 [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)과 같은 적절한 Get 메서드에 전달할 수도 있습니다.

## 배치 REST를 .NET API에 매핑

filter, select 및 expand 문자열의 속성 이름은 이름과 대소문자 모두 해당 REST API 항목을 반영해야 합니다. 다음 표는 .NET과 REST API 간의 매핑을 제공합니다.

### 필터 문자열에 대한 매핑

- **.NET 목록 메서드**--이 열의 각 .NET API 메서드는 [ODATADetailLevel][odata] 개체를 매개 변수 형태로 수락합니다.
- **REST 목록 요청**--이 열에 연결된 각 REST API 페이지에는 filter 문자열에서 허용되는 속성과 연산을 지정하는 테이블이 들어 있습니다. [ODATADetailLevel.FilterClause][odata_filter] 문자열을 구성할 때 이 속성 이름과 작업을 사용합니다.

| .NET 목록 메서드 | REST 목록 요청 |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [계정에 인증서 나열][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [태스크와 연관된 파일 나열][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [작업 준비 및 작업에 대한 작업 릴리스 태스크의 상태 나열][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [계정에 작업 나열][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [노드에 파일 나열][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [작업과 연관된 태스크 나열][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [계정에 작업 일정 나열][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [작업 일정과 연관된 작업 나열][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [풀에 계산 노드 나열][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [계정에 풀 나열][rest_list_pools]

### Select 문자열에 대한 매핑

- **배치 .NET 형식**--배치 .NET API 형식.
- **REST API 엔터티**--이 열의 각 페이지에는 형식에 대한 REST API 속성 이름을 나열하는 하나 이상의 표가 들어 있습니다. 이러한 속성 이름은 select 문자열을 구성할 때 사용됩니다. [ODATADetailLevel.SelectClause][odata_select] 문자열을 구성할 때 이와 동일한 속성을 사용합니다.

| 배치 .NET 형식 | REST API 엔터티 |
|---|---|
| [인증서][net_cert] | [인증서 정보 가져오기][rest_get_cert] |
| [CloudJob][net_job] | [작업 정보 가져오기][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [작업 일정 정보 가져오기][rest_get_schedule] |
| [ComputeNode][net_node] | [노드 정보 가져오기][rest_get_node] |
| [CloudPool][net_pool] | [풀 정보 가져오기][rest_get_pool] |
| [CloudTask][net_task] | [태스크 정보 가져오기][rest_get_task] |

### 예: Filter 문자열 구성

[ODATADetailLevel.FilterClause][odata_filter]에 대한 필터 문자열을 구성할 때는 "필터 문자열에 대한 매핑"에서 위의 표를 참조하여 수행하려는 목록 작업에 해당하는 REST API 설명서 페이지를 찾을 수 있습니다. 해당 페이지의 첫 번째 다중 행 표에 필터링 가능한 속성과 지원되는 연산자가 있습니다. 예를 들어, 종료 코드가 0이 아닌 모든 태스크를 검색하려는 경우 [작업과 연결된 태스크 목록][rest_list_tasks]의 이 행은 적용 가능한 속성 문자열과 허용 가능한 연산자를 지정합니다.

| 속성 | 허용되는 연산 | 형식 |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

따라서 종료 코드가 0이 아닌 모든 작업을 나열하기 위한 filter 문자열은 다음과 같게 됩니다.

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### 예: Select 문자열 구성

[ODATADetailLevel.SelectClause][odata_select]를 구성하려면 "select 문자열에 대한 매핑"에서 위의 표를 참조하고 나열하는 엔터티 형식에 해당하는 REST API 페이지로 이동합니다. 해당 페이지의 첫 번째 다중 행 표에 선택 가능한 속성과 지원되는 연산자가 있습니다. 목록의 각 태스크에 대해 ID와 명령줄만 검색하려면 [태스크 관련 정보 가져오기][rest_get_task]의 해당하는 표에서 이 행을 찾을 수 있습니다.

| 속성 | 형식 | 참고 |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

목록의 각 태스크와 함께 ID와 명령줄만 포함하기 위한 Select 문자열은 다음과 같게 됩니다.

`id, commandLine`

## 다음 단계

GitHub의 [EfficientListQueries][efficient_query_sample] 샘플 프로젝트를 확인하여 응용 프로그램에서 효율적인 목록 쿼리가 성능에 미치는 영향을 확인할 수 있습니다. 이 C# 콘솔 응용 프로그램은 많은 작업을 만들고 또 작업에 추가합니다. 그런 다음 [JobOperations.ListTasks][net_list_tasks] 메서드에 대한 여러 호출을 만들고 다른 속성 값으로 구성된 [ODATADetailLevel][odata] 개체를 전달하여 반환될 데이터의 크기를 다양화합니다. 다음과 유사한 출력을 생성합니다.

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

경과된 시간 정보에서 보는 것처럼 반환되는 항목 수와 속성을 제한하여 쿼리 응답 시간을 크게 낮출 수 있습니다. 이 샘플 프로젝트와 다른 샘플 프로젝트가 GitHub의 [azure-batch-samples][github_samples] 리포지토리에 있습니다.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=AcomDC_0121_2016-->
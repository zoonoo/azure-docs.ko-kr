<properties
	pageTitle="Azure 일괄 처리의 효율적인 목록 쿼리 | Microsoft Azure"
	description="반환되는 데이터의 양을 줄이고 Azure 배치 풀, 작업, 태스크, 계산 노드 등을 쿼리할 때 성능을 향상시킬 방법을 설명합니다."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/24/2015"
	ms.author="davidmu;v-marsma"/>

# 효율적인 배치 목록 쿼리

Azure 배치는 큰 계산이고 프로덕션 환경에서 작업, 태스크 및 계산 노드 같은 엔터티가 수천 개 있을 수 있습니다. 이러한 항목에 대한 정보를 얻으면 각 쿼리에 대해 전송해야 하는 많은 양의 데이터가 생성될 수 있습니다. 항목의 수와 각 항목에 대해 반환되는 정보의 형식을 제한하면 쿼리의 속도 및 응용 프로그램의 성능이 증가합니다.

다음 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) API 메서드는 Azure 배치를 사용하는 거의 모든 응용 프로그램이 종종 수행해야 하는 작업의 예입니다.

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

모니터링은 일반적인 사용 사례입니다. 예를 들어, 풀의 용량 및 상태를 확인하려면 풀의 모든 계산 노드(VM)를 쿼리할 수 있어야 합니다. 또 다른 예는 그러한 태스크가 큐에 대기 중인지 확인하기 위해 작업의 태스크에 대해 쿼리하는 것입니다. 일부 경우 많은 데이터 세트가 필요하지만, 다른 경우에는 총 항목 수 또는 특정 상태에 있는 항목 컬렉션이 필요합니다.

반환된 항목 수와 그러한 항목을 나타내는 데 필요한 데이터의 양이 매우 클 수 있다는 것을 알아 두는 것이 중요합니다. 단순히 많은 응답이 발생하는 많은 수의 항목에 대해 쿼리하는 것은 다음과 같이 많은 문제를 발생시킬 수 있습니다.

- 배치 API 응답 시간이 너무 느려질 수 있습니다. 항목 수가 많을수록 배치 서비스에서 필요한 쿼리 시간도 길어집니다. 많은 수의 항목을 청크로 나누어야 하므로, 클라이언트 라이브러리에서 하나의 목록에 대한 모든 항목을 얻기 위해 여러 API 호출을 수행해야 할 수 있습니다.
- 응용 프로그램 호출 배치에 의한 API 처리는 여러 항목을 처리하는 경우보다 시간이 오래 걸립니다.
- 많은 항목 및/또는 큰 항목이 있는 경우 응용 프로그램 호출 배치가 더 많은 메모리를 사용합니다.
- 많은 항목 및/또는 큰 항목은 네트워크 트래픽 증가로 이어질 수 있습니다. 이렇게 되면 전송 시간이 오래 걸리며, 응용 프로그램 아키텍처에 따라 배치 계정의 지역 외부에서 전송되는 데이터에 대해 네트워크 요금이 증가할 수 있습니다.

> [AZURE.IMPORTANT]응용 프로그램에 대한 최대의 효율성과 성능을 보장하려면 목록 API 호출에 *항상* 필터 및 Select 절을 사용하는 것이 *아주* 좋습니다. 이러한 절 및 용도는 아래에 나와 있습니다.

모든 배치 API에 대해 다음이 적용됩니다.

- 각 속성 이름은 개체의 속성에 매핑되는 문자열입니다.
- 모든 속성 이름은 대/소문자를 구분하지만, 속성 값은 대/소문자를 구분하지 않습니다.
- 속성 이름과 대/소문자 구분은 요소로서 배치 REST API에 표시됩니다.
- 날짜/시간 문자열은 두 형식 중 하나로 지정될 수 있으며 DateTime으로 시작해야 합니다.
	- W3CDTF(예: *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123(예: *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- 부울 문자열은 "true" 또는 "false"입니다.
- 잘못된 속성 또는 연산자를 지정하면 "400 (잘못 된 요청)" 오류가 발생합니다.

## 배치 .NET에서 효율적인 쿼리

배치 .NET API는 쿼리의 [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx)을 지정함으로써 목록에서 반환되는 항목의 수와 각 항목에 대해 반환되는 정보의 양을 모두 줄이는 기능을 제공합니다. DetailLevel은 추상 기본 클래스이며 실제로 [ODATADetailLevel][odata] 개체를 만들어 매개 변수로 적절한 메서드에 전달해야 합니다.

ODataDetailLevel 개체에는 생성자 내에 지정되어 있거나 직접 설정된 3개의 공용 문자열 속성이 있습니다.

- [FilterClause](#filter) – 필터링하여 반환되는 항목의 수를 줄일 수 있음
- [SelectClause](#select) – 각 항목에 대해 반환될 속성 값의 하위 집합을 지정하여 항목 및 응답 크기를 줄임
- [ExpandClause](#expand) – 다수의 호출과는 달리 한 번의 호출로 필요한 모든 데이터를 반환

> [AZURE.TIP]Select 및 Expand 절로 구성된 DetailLevel의 인스턴스는 반환되는 데이터의 양을 제한하기 위해 [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)과 같은 적절한 Get 메서드에 전달할 수도 있습니다.

### <a id="filter"></a> FilterClause

필터 문자열에 의해 반환되는 항목 수를 줄일 수 있습니다. 한정자와 함께 하나 이상의 속성 값을 쿼리에 관련된 항목만 반환되도록 지정할 수 있습니다. 예를 들어, 한 작업에 대해 실행 중인 태스크만 나열하거나 태스크를 실행할 준비된 계산 노드만 나열할 수 있습니다.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx)는 하나 이상의 식으로 구성된 문자열로, 이 식은 *속성 이름*, *연산자* 및 *값*으로 이루어져 있습니다. 각 속성에 대해 지원되는 연산자의 경우처럼 지정할 수 있는 속성은 API 호출마다 고유합니다. 논리 연산자 **And** 및 **Or**를 사용하여 여러 식을 결합할 수 있습니다.

예를 들어 이 필터 문자열은 "MyTask"로 시작하는 *displayName*을 가진 실행 태스크만 반환합니다.

	startswith(displayName, 'MyTask') and (state eq 'Running')

아래 각 배치 REST API 문서에는 다른 목록 작업의 해당 속성에서 지원되는 속성과 작업을 지정하는 테이블이 있습니다.

- [계정에 풀 나열](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [풀에 계산 노드 나열](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [계정에 작업 나열](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [작업 준비 및 작업에 대한 작업 릴리스 태스크의 상태 나열](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [계정에 작업 일정 나열](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [작업 일정과 연관된 작업 나열](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [작업과 연관된 태스크 나열](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [태스크와 연관된 파일 나열](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [계정에 인증서 나열](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [노드에 파일 나열](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]3개의 절 형식 중 하나로 속성을 지정하는 경우, 속성 이름과 대/소문자 구분이 대응하는 배치 REST API 요소의 것과 일치하는지 확인합니다. 예를 들어 .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask)로 작업하는 경우 .NET 속성이 [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state)이더라도 **State** 대신 **state**를 지정해야 합니다. 예를 들어 **state** 속성에 대해 적절한 이름과 대/소문자 구분을 확인하려면 배치 REST API 설명서의 [태스크에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn820133.aspx)에서 요소 이름을 확인하는 것이 좋습니다.

### <a id="select"></a> SelectClause

Select 문자열을 사용하여 각 항목에 대해 반환되는 속성 값을 제한할 수 있습니다. 항목에 대한 속성 목록을 지정하면 해당 속성 값만 반환됩니다.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx)는 속성 이름을 쉼표로 구분한 목록으로 구성된 문자열입니다. 목록 작업에 의해 반환된 항목에 대해 사용할 수 있는 속성의 조합을 지정할 수 있습니다.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

확장 절로 API 호출 수를 줄일 수 있습니다. 먼저 목록을 얻은 다음 목록에 대해 반복하는 것과는 달리, 단일 API 호출을 통해 각 목록 항목에 대한 자세한 정보를 얻을 수 있으며, 각 항목에 대한 호출을 목록으로 만듭니다.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx)는 특정 데이터를 결과에 반환할지 여부를 제어한다는 점에서 Select 절과 유사합니다. Expand 절은 작업 목록, 태스크 목록, 풀 목록에 대해서만 지원되며, 현재는 통계 정보만 지원합니다. 모든 속성이 필요하고 Select 절이 지정되지 않은 경우, Expand 절은 통계 정보를 얻는 데 사용되어야 합니다. Select 절이 속성의 하위 집합을 얻는 데 사용되는 경우 "stats"를 Select 절에 지정하고 Expand 절은 null 상태로 남겨 둘 수도 있습니다.

## 효율적인 쿼리 예

아래에서는 풀의 특정 집합에 대한 통계에 대해 배치 서비스를 효율적으로 쿼리하기 위해 배치 .NET API를 사용하는 코드 조각을 찾을 수 있습니다. 이 시나리오에서 배치 사용자는 "test"를 접두사로 사용하는 테스트 풀 ID와 "prod"를 접두사로 사용하는 프로덕션 풀 ID가 있는 테스트 및 프로덕션 풀을 가집니다. 조각에서 *myBatchClient*는 [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient)의 적절하게 초기화된 인스턴스입니다.

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

## 샘플 프로젝트

GitHub의 [EfficientListQueries][efficient_query_sample] 샘플 프로젝트를 확인하여 응용 프로그램에서 효율적인 목록 쿼리가 성능에 미치는 영향을 확인할 수 있습니다. 이 C# 콘솔 응용 프로그램은 많은 작업을 만들고 또 작업에 추가하며 다른 [ODATADetailLevel][odata] 사양을 사용하여 배치 서비스를 쿼리하여 다음과 유사한 출력을 표시합니다.

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

경과된 시간 정보에서 보는 것처럼 반환되는 항목 수와 속성을 제한하면 쿼리 응답 시간을 크게 낮출 수 있습니다. 이 샘플 프로젝트와 다른 샘플 프로젝트가 GitHub의 [azure-batch-samples][github_samples] 리포지토리에 있습니다.

## 다음 단계

1. 프로그램 개발 시나리오와 관련된 배치 API 설명서를 확인하지 않았으면 확인합니다.
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. GitHub에서 [Azure 배치 샘플](https://github.com/Azure/azure-batch-samples)에 대해 살펴보고 코드에 대해 자세히 알아봅니다.

[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx

<!---HONumber=Oct15_HO1-->
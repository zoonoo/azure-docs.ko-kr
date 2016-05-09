<properties 
	pageTitle="Azure Data Factory 파이프라인 모니터링 및 관리" 
	description="Azure 포털과 Azure PowerShell을 사용하여 사용자가 만든 Azure Data Factory와 파이프라인을 모니터링하고 관리하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="spelluru"/>


# Azure Data Factory 파이프라인 모니터링 및 관리
> [AZURE.SELECTOR]
- [Azure 포털/Azure PowerShell 사용](data-factory-monitor-manage-pipelines.md)
- [모니터링 및 관리 앱 사용](data-factory-monitor-manage-app.md)

데이터 팩터리 서비스는 저장소, 처리 및 데이터 이동 서비스의 안정적이고 완전한 뷰를 제공합니다. 이 서비스는 신속하게 종단 간 데이터 파이프라인 상태를 평가하고 문제를 파악하여 필요한 경우 수정 동작을 수행할 수 있도록 도와줍니다. 또한 모든 원본에서 데이터 간의 관계 및 데이터 연결을 시각적으로 추적하고 단일 모니터링 대시보드에서 작업 실행, 시스템 상태 및 종속성의 전체 기록을 확인할 수도 있습니다.

이 문서는 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 경고를 생성하고 오류에 대한 알림을 받는 방법에 대한 정보도 제공합니다.

## 파이프라인 및 작업 상태 이해
Azure 포털을 사용하면 데이터 팩터리를 다이어그램으로 볼 수 있고 활동을 파이프라인에서 볼 수 있고 입력 및 출력 데이터 집합 등을 볼 수 있습니다. 이 섹션은 조각이 하나의 상태에서 다른 상태로 전환되는 방식을 제공합니다.

### 데이터 팩터리로 이동
1.	[Azure 포털](https://portal.azure.com)에 로그인합니다.
2.	**모두 찾아보기**를 클릭하고 **데이터 팩터리**를 선택합니다.
	
	![모두 찾아보기 -> 데이터 팩터리](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	**데이터 팩터리** 블레이드에 모든 데이터 팩터리가 표시됩니다. 
4. 데이터 팩터리 블레이드에서 원하는 데이터 팩터리를 선택하면 데이터 팩터리의 홈 페이지(**데이터 팩터리** 블레이드) 가 표시됩니다.

	![데이터 팩터리 블레이드](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### 데이터 팩터리의 다이어그램 뷰
데이터 팩터리의 다이어그램 뷰에는 데이터 팩터리와 그 자산을 모니터링하고 관리하기 위한 단일 돋보기 창이 제공됩니다.

위쪽의 데이터 팩터리 홈 페이지에서 **다이어그램**을 클릭하여 데이터 팩터리의 다이어그램 뷰를 표시합니다.

![다이어그램 뷰](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

확대, 축소, 크기에 맞게, 100% 확대, 다이어그램 레이아웃 고정, 파이프라인과 테이블 자동 배치, 계보 표시(선택한 항목의 업스트림 및 다운스트림 항목 표시)가 가능합니다.
 

### 파이프라인 내부의 활동 
1. 파이프라인을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기**를 클릭하면 파이프라인 내부의 모든 활동과 활동에 대한 입력 및 출력 데이터 집합이 표시됩니다. 파이프라인이 둘 이상의 작업으로 구성된 경우 단일 파이프라인의 운영 계보를 이해하고자 할 때 유용합니다.

	![파이프라인 열기 메뉴](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. 다음 예에서는 파이프라인 내에 두 개의 활동과 입력 및 출력이 표시됩니다. HDInsight Hive 작업 유형인 **JoinData** 작업과 복사 작업 유형인 **EgressDataAzure** 작업이 샘플 파이프라인에 있습니다. 
	
	![파이프라인 내부의 활동](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. 왼쪽 위 모서리의 이동 경로 탐색에서 데이터 팩터리 링크를 클릭하면 데이터 팩터리 홈 페이지로 되돌아갈 수 있습니다.

	![데이터 팩터리로 돌아가기](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### 파이프라인 내부의 각 작업 상태 보기
작업에 의해 생성되는 데이터 집합의 상태를 보면 작업의 현재 상태를 볼 수 있습니다.

예를 들어 다음 예에서 **BlobPartitionHiveActivity**가 성공적으로 실행되었고 **PartitionedProductsUsageTable**이라는 데이터 집합이 생성되었으며 그 상태는 **Ready**입니다.

![파이프라인 상태](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

다이어그램 뷰에서 **PartitionedProductsUsageTable**을 두 번 클릭하면 파이프라인 내부에서 실행되는 다양한 작업에 의해 생성되는 모든 조각이 소개됩니다. **BlobPartitionHiveActivity**가 지난 8개월간 매달 성공적으로 실행되었고 **Ready** 상태의 조각을 생성했다는 것이 표시됩니다.

데이터 팩터리의 데이터 집합 조각의 상태는 다음 중 하나입니다.

<table>
<tr>
	<th align="left">시스템 상태</th><th align="left">하위 상태</th><th align="left">설명</th>
</tr>
<tr>
	<td rowspan="8">대기</td><td>ScheduleTime</td><td>아직 조각이 실행할 시간이 되지 않습니다.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>업스트림 종속성이 준비되지 않습니다.</td>
</tr>
<tr>
<td>ComputeResources</td><td>계산 리소스를 사용할 수 없습니다.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>모든 활동 인스턴스는 다른 조각을 실행하고 있습니다.</td>
</tr>
<tr>
<td>ActivityResume</td><td>작업은 일시 중지되어 재개될 때까지 조각을 실행할 수 없습니다.</td>
</tr>
<tr>
<td>Retry</td><td>활동 실행을 다시 시도합니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사를 아직 시작되지 않았습니다.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>유효성 검사를 다시 시도하기를 기다리고 있습니다.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>유효성 검사 중</td><td>유효성 검사가 진행 중입니다.</td>
</tr>
<td></td>
<td>조각이 처리되고 있습니다.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>실행이 작업에서 허용하는 것보다 오래 걸렸습니다.</td>
</tr>
<tr>
<td>Canceled</td><td>사용자 동작으로 취소합니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 실패했습니다.</td>
</tr>
<tr>
<td></td><td>조각을 생성 및/또는 유효성을 검사하지 못했습니다.</td>
</tr>
<td>Ready</td><td></td><td>조각을 사용할 준비가 되었습니다.</td>
</tr>
<tr>
<td>생략</td><td></td><td>조각이 처리되지 않았습니다.</td>
</tr>
<tr>
<td>없음</td><td></td><td>다른 상태와 함께 존재하기 위해 사용되는 조각이지만, 재설정되지 않았습니다.</td>
</tr>
</table>



**최근에 업데이트된 조각** 블레이드에서 조각 항목을 클릭하면 조각에 대한 세부 사항을 볼 수 있습니다.

![조각 세부 정보](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
조각이 여러 번 실행된 경우 **활동 실행** 목록에 여러 행이 표시됩니다.

![조각에 대한 작업 실행](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

**활동 실행** 목록에서 실행 항목을 클릭하면 활동 실행에 대한 세부 사항을 볼 수 있습니다. 모든 로그 파일과 함께 오류 메시지가(있다면) 소개됩니다. 데이터 팩터리를 벗어나지 않고 로그를 보면서 디버그하기에 매우 유용합니다.

![작업 실행 세부 정보](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

조각이 **준비** 상태가 아닌 경우 **준비되지 않은 업스트림 슬라이스입니다** 목록에서 준비 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다. 조각이 **대기 중** 상태일 때 조각이 기다리고 있는 업스트림 종속성을 이해하려는 경우에 유용합니다.

![준비되지 않은 업스트림 조각](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### 데이터 집합 상태 다이어그램
데이터 팩터리를 배포하고 파이프라인의 활성 기간이 유효한 경우 데이터 집합 조각은 하나의 상태에서 다른 상태로 전환됩니다. 현재 조각 상태는 다음과 같은 상태 다이어그램을 따릅니다.

![상태 다이어그램](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

데이터 팩터리의 데이터 집합 상태 전환 흐름에는 Waiting(대기)-> In-Progress/In-Progress (Validating)(진행 중/진행 중(확인 중)) -> Ready/Failed(준비/실패)가 포함됩니다.

조각은 실행 전에 사전 조건이 충족되도록 **대기 중** 상태에서 시작됩니다. 그 후 작업은 실행을 시작하고 조각은 **진행 중** 상태가 됩니다. 활동 실행은 성공하거나 실패할 수 있으며 그에 따라 조각은 **준비** 또는 **실패** 상태가 됩니다.

사용자는 **준비** 또는 **실패** 상태에서 **대기 중** 상태로 조각을 재설정할 수 있습니다. 사용자는 조각 상태를 **Skip**(건너뛰기)로 표시할 수도 있으며, 이렇게 하면 작업이 실행되지 않고 조각이 처리되지 않습니다.


## 파이프라인 관리
Azure PowerShell을 사용하여 파이프라인을 관리할 수 있습니다. 예를 들어 Azure PowerShell cmdlet을 실행하여 파이프라인을 일시 중지하거나 다시 시작할 수 있습니다.

### 파이프라인 일시 중지 및 다시 시작
**Suspend-AzureRmDataFactoryPipeline** Powershell cmdlet을 사용하여 파이프라인을 일시 중지/일시 중단할 수 있습니다. 데이터에 문제가 있다는 것을 파악한 후에 문제가 수정될 때까지 데이터 처리를 위해 파이프라인을 실행하지 않으려는 경우에 유용합니다.

예를 들어 아래 스크린샷에 **productrecgamalbox1dev** 데이터 팩터리의 **PartitionProductsUsagePipeline**에서 문제가 확인되어 파이프라인을 일시 중단하려고 합니다.

![일시 중단할 파이프라인](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

**PartitionProductsUsagePipeline**을 일시 중단하기 위해 다음과 같은 PowerShell 명령을 실행합니다.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

예:

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

**PartitionProductsUsagePipeline**의 문제가 해결되고 나면 다음과 같은 PowerShell 명령을 실행하여 일시 중지한 파이프라인을 다시 시작합니다.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

예:

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## 파이프라인 디버깅
Azure Data Factory는 Azure 포털 및 Azure PowerShell을 통해 파이프라인 디버그와 문제 해결을 위한 다양한 기능을 제공합니다.

### 파이프라인에서 오류 찾기
파이프라인에서 작업이 실패하면 파이프라인에 의해 생성된 데이터 집합은 실패로 인한 오류 상태가 됩니다. 다음과 같은 메커니즘을 사용하여 Azure Data Factory에서 오류의 문제를 해결하고 디버그할 수 있습니다.

#### Azure 포털을 사용한 오류 디버그:

1.	데이터 팩터리 홈페이지의 **데이터 집합** 타일에서 **With errors(오류 있음)**를 클릭합니다.
	
	![오류가 있는 데이터 집합 타일](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	**Datasets with errors**(오류가 있는 데이터 집합) 블레이드에서 원하는 테이블을 클릭합니다.

	![오류가 있는 데이터 집합 블레이드](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	**테이블** 블레이드에서 **상태**가 **Failed**(실패)로 설정된 문제 조각을 클릭합니다.

	![문제 조각이 있는 테이블 블레이드](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	**데이터 조각** 블레이드에서 실패한 활동 실행을 클릭합니다.
	
	![오류가 있는 데이터 조각](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	**작업 실행 세부 정보** 블레이드에서 HDInsight 처리와 관련된 파일을 다운로드할 수 있습니다. Status/stderr에 대한 다운로드를 클릭하여 오류에 대한 세부 정보를 포함하는 오류 로그 파일을 다운로드합니다.

	![오류가 있는 작업 실행 세부 정보 블레이드](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### PowerShell을 사용한 오류 디버그
1.	**Azure PowerShell**을 시작합니다.
3.	**Get-AzureRmDataFactorySlice** 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 **실패**로 표시됩니다.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	예:


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	**StartDateTime**을 Set-AzureRmDataFactoryPipelineActivePeriod에 대해 지정한 StartDateTime 값으로 바꿉니다.
4. 이제 **Get-AzureRmDataFactoryRun** cmdlet를 실행하여 조각의 작업 실행에 대한 세부 정보를 가져옵니다.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	예:


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	StartDateTime 값은 이전 단계에서 기록한 오류/문제 조각의 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
5. 	출력에 오류에 대한 세부 정보가 다음과 같이 표시됩니다.

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	위의 출력에 표시된 ID 값을 사용하여 **Save-AzureRmDataFactoryLog** cmdlet를 실행하고 cmdlet에 **-DownloadLogsoption** 옵션을 사용하여 로그 파일을 다운로드할 수 있습니다.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## 파이프라인에서 실패한 항목을 다시 실행합니다.

### Azure 포털 사용

파이프라인에서 실패에 대한 문제를 해결하고 디버그한 후에는 오류 조각으로 이동하고 명령 모음의 **실행** 단추를 클릭하여 실패한 항목을 다시 실행할 수 있습니다.

![실패한 조각 다시 실행](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

정책 오류(예: 데이터를 사용할 수 없음)로 인해 조각 유효성 검사에 실패한 경우에는 명령 모음의 **유효성 검사** 단추를 클릭하여 오류를 해결하고 유효성 검사를 다시 할 수 있습니다.![오류 수정 및 유효성 검사](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Azure PowerShell 사용

Set-AzureRmDataFactorySliceStatus cmdlet을 사용하여 실패를 다시 실행할 수 있습니다. cmdlet에 대한 구문 및 기타 세부 정보는 [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) 항목을 참조하세요.

**예제:** 다음 예제에서는 Azure Data Factory 'WikiADF'에서 'DAWikiAggregatedData' 테이블의 모든 조각 상태를 'Waiting'으로 설정합니다.

**참고:** UpdateType이 UpstreamInPipeline으로 설정되며 이것은 파이프라인에서 작업에 대한 입력 테이블로 사용되는 테이블과 모든 종속(업스트림) 테이블에 대한 각 조각의 상태를 "Waiting"으로 설정합니다. 이 매개 변수에 사용할 수 있는 다른 값은 "Individual"입니다.

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## 경고 만들기
Azure는 Azure 리소스(예: 데이터 팩터리)가 생성, 업데이트 또는 삭제될 때 사용자 이벤트를 기록합니다. 이러한 이벤트에 경고를 만들 수 있습니다. 데이터 팩터리를 사용하면 다양한 메트릭을 캡처하고 메트릭에 대한 경고를 만들 수 있습니다. 실시간 모니터링을 위한 이벤트와 기록을 목적으로 하는 메트릭을 사용하는 것이 좋습니다.

### 이벤트에 대한 경고
Azure 이벤트는 Azure 리소스에서 일어나는 일에 대한 유용한 통찰력을 제공합니다. Azure는 Azure 리소스(예: 데이터 팩터리)가 생성, 업데이트 또는 삭제될 때 사용자 이벤트를 기록합니다. Azure Data Factory를 사용하면 다음의 경우에 이벤트가 생성됩니다.

- Azure Data Factory가 생성/업데이트/삭제됩니다.
- 데이터 처리(실행이라고 하는)가 시작/완료되었습니다.
- 주문형 HDInsight 클러스터가 생성되고 제거되는 경우.

이러한 사용자 이벤트에 대한 경고를 만들고 구독의 관리자 및 공동 관리자에게 메일 알림을 보내도록 구성할 수 있습니다. 또한 조건이 충족되는 경우 전자 메일 알림을 수신해야 하는 사용자의 추가 메일 주소를 지정할 수 있습니다. 데이터 팩터리를 지속적으로 모니터링하지 않고 오류에 대한 알림을 받으려는 경우에 유용합니다.

> [AZURE.NOTE] 현재는 이벤트에 대한 경고가 포털에 표시되지 않습니다. 모든 경고를 보려면 [모니터링 및 관리 앱](data-factory-monitor-manage-app.md)을 사용하세요.

#### 경고 정의 지정:
경고 정의를 지정하려면, 경고를 표시하려는 작업을 설명하는 JSON 파일로 만들 수 있습니다. 아래 예에서 경고는 RunFinished 작업에 대해 전자 메일 알림을 보냅니다. 구체적으로 데이터 팩터리에서 실행이 완료되고 실행이 실패한 경우(상태 = FailedExecution) 전자 메일 알림이 전송됩니다.

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                        "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

특정 오류에 대한 경고를 표시하지 않으려면 위의 JSON 정의에서 **subStatus**를 제거할 수 있습니다.

위의 예제에서는 구독의 모든 데이터 팩터리에 대해 경고를 설정합니다. 특정 데이터 팩터리에 대해 경고를 설정하려는 경우 아래와 같이 **dataSource** 블록에서 데이터 팩터리 **resourceUri**를 지정할 수 있습니다.

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

다음 테이블은 사용 가능한 작업 및 상태(및 하위 상태) 목록을 제공합니다.

작업 이름 | 상태 | 하위 상태
-------------- | ------ | ----------
RunStarted | Started | Starting
RunFinished | Failed / Succeeded | FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned
OnDemandClusterCreateStarted | 시작
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

위의 예에 사용되는 JSON 요소에 대한 내용은 [경고 규칙 만들기](https://msdn.microsoft.com/library/azure/dn510366.aspx)를 참조하세요.

#### 경고 배포 
경고를 배포하려면 다음 예제와 같이 Azure PowerShell cmdlet인 **New-AzureRmResourceGroupDeployment**를 사용합니다.

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

리소스 그룹 배포가 성공적으로 완료되면 다음 메시지가 표시됩니다.

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

> [AZURE.NOTE] [경고 규칙 만들기](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST API를 사용하여 경고 규칙을 만들 수 있습니다. JSON 페이로드는 위에 지정된 JSON 예제와 비슷합니다.

#### Azure 리소스 그룹 배포의 목록 검색
배포된 Azure 리소스 그룹 배포의 목록을 검색하려면 다음 예제와 같이 **Get-AzureRmResourceGroupDeployment** cmdlet을 사용합니다.

	Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### 사용자 이벤트 문제 해결


- **작업** 타일을 클릭한 후에 생성된 모든 이벤트가 표시되며 **이벤트** 블레이드에 표시되는 모든 작업에 대해 경고를 설정할 수 있습니다.

	![작업](./media/data-factory-monitor-manage-pipelines/operations.png)


- 경고를 추가/가져오기/제거하기 위해 사용할 수 있는 PowerShell cmdlet은 [Azure Insight Cmdlet](https://msdn.microsoft.com/library/mt282452.aspx) 문서를 참조하세요. 다음은 **Get-AlertRule** cmdlet을 사용하는 몇 가지 예입니다.


		PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
			
				Properties :
		        Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
		        Condition   :
				DataSource :
				EventName             :
				Category              :
				Level                 :
				OperationName         : RunFinished
				ResourceGroupName     :
				ResourceProviderName  :
				ResourceId            :
				Status                : Failed
				SubStatus             : FailedExecution
				Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
		        Condition  	:
				Description : One or more of the data slices for the Azure Data Factory has failed processing.
				Status      : Enabled
				Name:       : ADFAlertsSlice
				Tags       :
				$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
				Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
				Location   : West US
				Name       : ADFAlertsSlice
		
		PS C:\> Get-AlertRule -res $resourceGroup
	
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
				Location   : West US
				Name       : FailedExecutionRunsWest3
	
		PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0

	Get-AlertRule cmdlet에 대한 세부 정보 및 예를 보려면 다음 get-help 명령을 실행합니다.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- 포털 블레이드에 경고 생성 이벤트가 표시되는데 전자 메일 알림을 수신하지 못한다면 지정된 전자 메일 주소가 외부의 전자 메일을 수신하도록 설정되어 있는지 확인합니다. 경고 전자 메일이 사용자의 전자 메일 설정에서 차단되어 있을 수 있습니다.

### 메트릭에 대한 경고
데이터 팩터리를 사용하면 다양한 메트릭을 캡처하고 메트릭에 대한 경고를 만들 수 있습니다. 데이터 팩터리의 조각의 다음과 같은 메트릭에 대해 경고를 생성하고 모니터링할 수 있습니다.
 
- 실패한 실행
- 성공한 실행

이 메트릭은 매우 유용하며 사용자의 데이터 팩터리에서 실패한 실행과 성공한 실행 모두에 대한 개요를 볼 수 있습니다. 메트릭은 조각을 실행할 때마다 내보내집니다. 이 메트릭은 정시마다 집계되어 사용자의 저장소 계정으로 푸시됩니다. 따라서 메트릭을 사용하도록 하려면 저장소 계정을 설정해야 합니다.

#### 메트릭 사용:
메트릭을 사용할 수 있도록 하려면 데이터 팩터리 블레이드에서 다음 항목을 클릭합니다.

**모니터링** -> **메트릭** -> **진단 설정** -> **진단**

**진단** 블레이드에서 **켜기**를 클릭하고 저장소 계정을 선택한 다음 저장합니다.

![메트릭 사용](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

메트릭 집계가 매시간 발생하기 때문에 저장 후에 모니터링 블레이드에 메트릭이 표시되기까지 최대 한 시간이 걸릴 수 있습니다.


### 메트릭에 대한 경고 설정:

메트릭에 대한 경고를 설정하려면 데이터 팩터리 블레이드에서 **모니터링** -> **메트릭** -> **경고 추가** -> **경고 규칙 추가**를 클릭합니다.

경고 규칙에 대한 세부 정보를 입력하고 메일을 지정한 다음 **확인**을 클릭합니다.


![메트릭에 대한 경고 설정](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

완료되면 다음과 같이 경고 규칙 타일에 새로 설정된 경고 규칙이 표시됩니다.

![활성화된 경고 규칙](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

축하합니다. 메트릭에 첫 번째 경고가 설정되었습니다. 정해진 기간 동안 경고 규칙이 일치할 때마다 알림을 수신하게 됩니다.

### 경고 알림:
설정 규칙이 조건과 일치하면 경고 활성화 전자 메일이 전송됩니다. 문제가 해결되고 경고 조건이 더 이상 일치하지 않으면 경고 해지 전자 메일이 전송됩니다.

이 동작은 경고 규칙을 충족하는 모든 오류에 대해 알림이 전송되는 이벤트와 다릅니다.

### PowerShell을 사용하여 경고 배포
이벤트와 같은 방식으로 메트릭에 대한 경고를 배포할 수 있습니다.

**경고 정의:**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
위 샘플의 subscriptionId, resourceGroupName, dataFactoryName을 적절한 값으로 변경합니다.

*metricName*은 현재 두 가지 값을 지원합니다.
- FailedRuns
- SuccessfulRuns

**경고 배포:**

경고를 배포하려면 다음 예제와 같이 Azure PowerShell cmdlet인 **New-AzureRmResourceGroupDeployment**를 사용합니다.

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

배포가 완료되면 다음과 같은 메시지가 표시됩니다.

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           


**Add-AlertRule** cmdlet을 사용하여 경고 규칙을 배포할 수 있습니다. 세부 정보 및 예제는 [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) 항목을 참조하세요.

## 다른 리소스 그룹 또는 구독으로 데이터 팩터리 이동
Data Factory의 홈 페이지에서 **이동** 명령 모음 단추를 사용하여 다른 리소스 그룹이나 다른 구독으로 데이터 팩터리를 이동할 수 있습니다.

![데이터 팩터리 이동](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

데이터 팩터리와 함께 관련된 모든 리소스(예: 데이터 팩터리와 관련된 경고)를 이동할 수도 있습니다.

![리소스 이동 대화 상자](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

<!---HONumber=AcomDC_0427_2016-->
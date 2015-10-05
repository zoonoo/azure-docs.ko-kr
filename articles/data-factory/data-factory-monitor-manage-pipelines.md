<properties 
	pageTitle="Azure Data Factory 파이프라인 모니터링 및 관리" 
	description="Azure 관리 포털과 Azure PowerShell을 사용하여 사용자가 만든 Azure Data Factory와 파이프라인을 모니터링하고 관리하는 방법에 대해 알아봅니다." 
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
	ms.date="07/27/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 파이프라인 모니터링 및 관리
데이터 팩터리 서비스는 저장소, 처리 및 데이터 이동 서비스의 안정적이고 완전한 뷰를 제공합니다. 이 서비스는 신속하게 종단 간 데이터 파이프라인 상태를 평가하고 문제를 파악하여 필요한 경우 수정 동작을 수행할 수 있도록 도와줍니다. 또한 모든 원본에서 데이터 간의 관계 및 데이터 연결을 시각적으로 추적하고 단일 모니터링 대시보드에서 작업 실행, 시스템 상태 및 종속성의 전체 기록을 확인할 수도 있습니다.

이 문서는 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 경고를 생성하고 오류에 대한 알림을 받는 방법에 대한 정보도 제공합니다.

## 파이프라인 및 작업 상태 이해
Azure Preview 포털을 사용하면 데이터 팩터리를 다이어그램으로 볼 수 있고 활동을 파이프라인에서 볼 수 있고 입력 및 출력 데이터 집합 등을 볼 수 있습니다. 이 섹션은 조각이 하나의 상태에서 다른 상태로 전환되는 방식을 제공합니다.

### 데이터 팩터리로 이동
1.	[Azure Preview 포털](http://portal.azure.com)에 로그인합니다.
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

상태 | 하위 상태 | 설명
------ | ---------- | -----------
대기 | ScheduledTime<br/>DatasetDependencies<br/>ComputeResources<br/>ConcurrencyLimit<br/>ActivityResume<br/>Retry<br/>Validation<br/>ValidationRetry | 실행 전에 사전 조건이 충족되기를 기다립니다. 하위 상태를 참조하여 조각에 무엇이 필요한지 파악합니다.
In-Progress(진행 중) | Starting(시작 중)<br/>Configuring(구성 중)<br/>Allocating Resources(리소스 할당 중)<br/>Running(실행 중)<br/>Validating(유효성 검사 중) | 현재 작업이 실행 중이며 특정 조각에 대한 데이터를 생성/확인 중입니다.
Failed | | 조각 처리에 실패했습니다. 오류 로그를 참조하여 실패의 원인을 파악합니다.
Ready | | 조각 처리에 성공했습니다. 조각을 사용할 준비가 되었습니다.
Skip | | 이 조각을 처리하지 않습니다.

**Recently updated slices**(최근에 업데이트된 조각) 블레이드에서 조각 항목을 클릭하면 조각에 대한 세부 사항을 볼 수 있습니다.

![조각 세부 정보](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
조각이 여러 번 실행된 경우 **작업 실행** 목록에 여러 행이 표시됩니다.

![조각에 대한 작업 실행](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

**작업 실행** 목록에서 실행 항목을 클릭하면 작업 실행에 대한 세부 사항을 볼 수 있습니다. 모든 로그 파일과 함께 오류 메시지가(있다면) 소개됩니다. 데이터 팩터리를 벗어나지 않고 로그를 보면서 디버그하기에 매우 유용합니다.

![작업 실행 세부 정보](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다. 조각이 **Waiting**(대기) 상태일 때 조각이 기다리고 있는 업스트림 종속성을 이해하려는 경우에 유용합니다.

![준비되지 않은 업스트림 조각](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### 데이터 집합 상태 다이어그램
데이터 팩터리를 배포하고 파이프라인의 활성 기간이 유효한 경우 데이터 집합 조각은 하나의 상태에서 다른 상태로 전환됩니다. 현재 조각 상태는 다음과 같은 상태 다이어그램을 따릅니다.

![상태 다이어그램](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

데이터 팩터리의 데이터 집합 상태 전환 흐름에는 Waiting(대기)-> In-Progress/In-Progress (Validating)(진행 중/진행 중(확인 중)) -> Ready/Failed(준비/실패)가 포함됩니다.

조각은 실행 전에 사전 조건이 충족되도록 **Waiting**(대기) 상태에서 시작됩니다. 그 후 작업은 실행을 시작하고 조각은 **In-Progress**(진행 중) 상태가 됩니다. 작업 실행은 성공하거나 실패할 수 있으며 그에 따라 조각은 **Ready**(준비) 또는 **Failed**(실패) 상태가 됩니다.

사용자는 **Ready**(준비) 또는 **Failed**(실패) 상태에서 **Waiting**(대기) 상태로 조각을 재설정할 수 있습니다. 사용자는 조각 상태를 **Skip**(건너뛰기)로 표시할 수도 있으며, 이렇게 하면 작업이 실행되지 않고 조각이 처리되지 않습니다.


## 파이프라인 관리
Azure PowerShell을 사용하여 파이프라인을 관리할 수 있습니다. 예를 들어 Azure PowerShell cmdlet을 실행하여 파이프라인을 일시 중지하거나 다시 시작할 수 있습니다.

### 파이프라인 일시 중지 및 다시 시작
**Suspend-AzureDataFactoryPipeline** Powershell cmdlet을 사용하여 파이프라인을 일시 중시/다시 시작할 수 있습니다. 데이터에 문제가 있다는 것을 파악한 후에 문제가 수정될 때까지 데이터 처리를 위해 파이프라인을 실행하지 않으려는 경우에 유용합니다.

예를 들어 아래 스크린샷에 **productrecgamalbox1dev** 데이터 팩터리의 **PartitionProductsUsagePipeline**에서 문제가 확인되어 파이프라인을 일시 중지하려고 합니다.

![일시 중단할 파이프라인](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

**PartitionProductsUsagePipeline**을 일시 중지하기 위해 다음과 같은 PowerShell 명령을 실행합니다.

	Suspend-AzureDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

예:

	Suspend-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

**PartitionProductsUsagePipeline**의 문제가 해결되고 나면 다음과 같은 PowerShell 명령을 실행하여 일시 중지한 파이프라인을 다시 시작합니다.

	Resume-AzureDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

예:

	Resume-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## 파이프라인 디버깅
Azure Data Factory는 Azure 포털 및 Azure PowerShell을 통해 파이프라인 디버그와 문제 해결을 위한 다양한 기능을 제공합니다.

### 파이프라인에서 오류 찾기
파이프라인에서 작업이 실패하면 파이프라인에 의해 생성된 데이터 집합은 실패로 인한 오류 상태가 됩니다. 다음과 같은 메커니즘을 사용하여 Azure Data Factory에서 오류의 문제를 해결하고 디버그할 수 있습니다.

#### Azure 포털을 사용한 오류 디버그:

1.	데이터 팩터리 홈페이지의 **데이터 집합** 타일에서 **With errors(오류 있음)**를 클릭합니다.
	
	![오류가 있는 데이터 집합 타일](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	**Datasets with errors(오류가 있는 데이터 집합)** 블레이드에서 원하는 테이블을 클릭합니다.

	![오류가 있는 데이터 집합 블레이드](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	**테이블** 블레이드에서 **상태**가 **Failed**(실패)로 설정된 문제 조각을 클릭합니다.

	![문제 조각이 있는 테이블 블레이드](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	**데이터 조각** 블레이드에서 실패한 작업 실행을 클릭합니다.
	
	![오류가 있는 데이터 조각](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	**작업 실행 세부 정보** 블레이드에서 HDInsight 처리와 관련된 파일을 다운로드할 수 있습니다. Status/stderr에 대한 다운로드를 클릭하여 오류에 대한 세부 정보를 포함하는 오류 로그 파일을 다운로드합니다.

	![오류가 있는 작업 실행 세부 정보 블레이드](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### PowerShell을 사용한 오류 디버그
1.	**Azure PowerShell**을 시작합니다.
2.	**AzureResourceManager** 모드로 전환합니다. 데이터 팩터리 cmdlet은 이 모드에서만 사용할 수 있습니다.

		switch-azuremode AzureResourceManager
3.	**Get-AzureDataFactorySlice** 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 **Failed**(실패)로 표시됩니다.

		Get-AzureDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	예:


		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	**StartDateTime**을 Set-AzureDataFactoryPipelineActivePeriod에서 지정한 StartDateTime 값으로 바꿉니다.
4. 이제 **Get-AzureDataFactoryRun** cmdlet을 실행하여 조각의 작업 실행에 대한 세부 정보를 가져옵니다.

		Get-AzureDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	예:


		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

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
	
	
6. 	위의 출력에 표시된 ID 값을 사용하여 **Save-AzureDataFactoryLog** cmdlet을 실행하고 cmdlet에 **-DownloadLogsoption** 옵션을 사용하여 로그 파일을 다운로드할 수 있습니다.

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## 파이프라인에서 실패한 항목을 다시 실행합니다.

### Azure 포털 사용

파이프라인에서 실패에 대한 문제를 해결하고 디버그한 후에는 오류 조각으로 이동하고 명령 모음의 **실행** 단추를 클릭하여 실패한 항목을 다시 실행할 수 있습니다.

![실패한 조각 다시 실행](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

정책 오류(예: 데이터를 사용할 수 없음)로 인해 조각 유효성 검사에 실패한 경우에는 명령 모음의 **유효성 검사** 단추를 클릭하여 오류를 해결하고 유효성 검사를 다시 할 수 있습니다.![오류 수정 및 유효성 검사](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Azure PowerShell 사용

‘Set-AzureDataFactorySliceStatus’ cmdlet을 사용하여 실패 항목을 다시 실행할 수 있습니다.

	Set-AzureDataFactorySliceStatus [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Status] <String> [[-UpdateType] <String> ] [-Profile <AzureProfile> ] [ <CommonParameters>]

**예:** 다음 예에서는 Azure Data Factory 'WikiADF'에서 'DAWikiAggregatedData' 테이블의 모든 조각 상태를 'PendingExecution'으로 설정합니다.

**참고:** UpdateType이 UpstreamInPipeline으로 설정되며 이것은 파이프라인에서 작업의 입력 테이블로 사용되는 테이블과 모든 종속(업스트림) 테이블에 대한 각 조각의 상태를 "PendingExecution"으로 설정합니다. 이 매개 변수에 사용할 수 있는 다른 값은 "Individual"입니다.

	Set-AzureDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status PendingExecution -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## 경고 만들기
Azure는 Azure 리소스(예: 데이터 팩터리)가 생성, 업데이트 또는 삭제될 때 사용자 이벤트를 기록합니다. 이러한 이벤트에 경고를 만들 수 있습니다. 데이터 팩터리를 사용하면 다양한 메트릭을 캡처하고 메트릭에 대한 경고를 만들 수 있습니다. 실시간 모니터링을 위한 이벤트와 기록을 목적으로 하는 메트릭을 사용하는 것이 좋습니다.

### 이벤트에 대한 경고
Azure 이벤트는 Azure 리소스에서 일어나는 일에 대한 유용한 통찰력을 제공합니다. Azure는 Azure 리소스(예: 데이터 팩터리)가 생성, 업데이트 또는 삭제될 때 사용자 이벤트를 기록합니다. Azure Data Factory를 사용하면 다음의 경우에 이벤트가 생성됩니다.

- Azure Data Factory가 생성/업데이트/삭제됩니다.
- 데이터 처리(실행이라고 하는)가 시작/완료되었습니다.
- 주문형 HDInsight 클러스터가 생성되고 제거되는 경우.

이러한 사용자 이벤트에 대한 경고를 만들고 구독의 관리자 및 공동 관리자에게 메일 알림을 보내도록 구성할 수 있습니다. 또한 조건이 충족되는 경우 전자 메일 알림을 수신해야 하는 사용자의 추가 메일 주소를 지정할 수 있습니다. 데이터 팩터리를 지속적으로 모니터링하지 않고 오류에 대한 알림을 받으려는 경우에 유용합니다.

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
RunFinished | Failed / Succeeded | <p>FailedResourceAllocation</p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p><Canceled/p><p>FailedValidation</p><p>Abandoned</p>
SliceOnTime | In Progress | Ontime
SliceDelayed | In Progress | Late
OnDemandClusterCreateStarted | 시작
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

#### 경고 배포 
경고를 배포하려면 다음 예제와 같이 Azure PowerShell cmdlet, **New-AzureResourceGroupDeployment**를 사용합니다.

	New-AzureResourceGroupDeployment -ResourceGroupName adf     -TemplateFile .\ADFAlertFailedSlice.json  

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

#### Azure 리소스 그룹 배포의 목록 검색
배포된 Azure 리소스 그룹 배포의 목록을 검색하려면 다음 예와 같이 cmdlet, **Get-AzureResourceGroupDeployment**를 사용합니다.

	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### 사용자 이벤트 문제 해결
**작업** 타일을 클릭한 후에 생성된 모든 이벤트가 표시되며 **이벤트** 블레이드에 표시되는 모든 작업에 대해 경고를 설정할 수 있습니다.

![작업](./media/data-factory-monitor-manage-pipelines/operations.png)

Powershell을 사용하여 경고 설정을 보려면 다음과 같은 명령을 실행한 후 생성된 모든 경고를 확인합니다. 리소스 종류를 **microsoft.insights/alertrules**로 지정하여 메트릭과 이벤트에 대해 설정된 경고가 모두 표시됩니다.

	Get-AzureResourceGroup -Name $resourceGroupName

	ResourceGroupName : mdwevent
	Location          : westus
	ProvisioningState : Succeeded
	Resources         :
                    Name                  Type                                 Location
                    ====================  ===================================  ========
                    abhieventtest1        Microsoft.DataFactory/dataFactories  westus
                    abhieventtest2        Microsoft.DataFactory/dataFactories  westus
                    FailedValidationRuns  microsoft.insights/alertrules        eastus


포털 블레이드에 경고 생성 이벤트가 표시되는데 전자 메일 알림을 수신하지 못한다면 지정된 전자 메일 주소가 외부의 전자 메일을 수신하도록 설정되어 있는지 확인합니다. 경고 전자 메일이 사용자의 전자 메일 설정에서 차단되어 있을 수 있습니다.

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

현재 *metricName*은 FailedRuns 및 SuccessfulRuns의 두 가지 값을 지원합니다.

**경고 배포:**

경고를 배포하려면 다음 예제와 같이 Azure PowerShell cmdlet, **New-AzureResourceGroupDeployment**를 사용합니다.

	New-AzureResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

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


## 피드백 보내기
이 문서에 대한 의견을 보내주시면 감사하겠습니다. 몇 분 정도 시간을 할애해서 [메일](mailto:adfdocfeedback@microsoft.com?subject=data-factory-monitor-manage-pipelines.md)을 통해 의견을 보내주세요.

<!---HONumber=Sept15_HO4-->
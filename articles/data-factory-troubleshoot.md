<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Azure 데이터 팩터리 문제 해결" description="Azure 데이터 팩터리 사용과 관련된 문제를 해결하는 방법에 대해 알아봅니다." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 데이터 팩터리 문제 해결
Azure 포털 또는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리 문제를 해결할 수 있습니다. 이 항목의 연습에서는 데이터 팩터리에서 발생하는 오류를 Azure 포털을 사용하여 신속하게 해결하는 방법을 보여 줍니다. 

## 이 문서에서는 다음을 수행합니다.

- [연습: 데이터 복사 관련 오류 해결](#copywalkthrough)
- [연습: Hive/Pig 처리 관련 오류 해결](#pighivewalkthrough)

## <a name="copywalkthrough"></a>연습: 데이터 복사 관련 오류 해결
이 연습에서는 데이터 팩터리 시작 문서의 자습서에서 오류가 일어나게 하고 Azure 포털을 사용하여 이 오류를 해결하는 방법을 알아봅니다.

### 필수 조건
1. [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 완료합니다.
2. **ADFTutorialDataFactory**가 Azure SQL 데이터베이스의 **emp** 테이블에 데이터를 생성하는지 확인합니다.  
3. 이제 Azure SQL 데이터베이스에서 **emp** 테이블을 삭제합니다(**drop table emp**). 그러면 오류가 발생합니다.
4. 다음 명령을 **Azure PowerShell**에서 실행하여 파이프라인의 활성 기간을 업데이트합니다. 그러면 파이프라인에서 더 이상 없는 **emp** 테이블에 데이터를 쓰려고 시도합니다.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] <b>StartDateTime</b> 값을 현재 날짜로, <b>EndDateTime</b> 값을 다음 날짜로 바꿉니다. 


### Azure 미리 보기 포털을 사용하여 오류 해결

1.	[Azure 미리 보기 포털][azure-preview-portal]에 로그인합니다. 
2.	**시작 보드 에서 **ADFTutorialDataFactory**를 **클릭합니다. 데이터 팩터리 링크가 **시작 보드**에 표시되지 않는 경우 **찾아보기** 허브를 클릭하고 **Everything**을 클릭합니다. **찾아보기** 블레이드에서 **데이터 팩터리...**를 클릭하고 **ADFTutorialDataFactory**를 클릭합니다.
3.	**데이터 집합** 타일에 **With errors**가 표시됩니다. **With errors**를 클릭합니다. **Datasets with errors** 블레이드가 표시됩니다.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. **Datasets** with errors 블레이드에서 **EmpSQLTable**을 클릭하여 **테이블** 블레이드를 표시합니다.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. **테이블** 블레이드에서는 문제 조각 즉, 오류가 있는 조각이 아래쪽의 **Problem slices** 목록에 표시됩니다. 또한 오류가 있는 최근 조각은 **Recent slices** 목록에 표시됩니다. **Problem slices** 목록에서 조각을 클릭합니다. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	특정 문제가 아니라 **Problem slices**를 클릭하면 **데이터 조각** 블레이드가 표시되며 여기서 **특정 문제 조각**을 클릭하여 선택한 데이터 조각에 대한 **데이터 조각** 슬라이드를 표시할 수 있습니다.

6. **EmpSQLTable**에 대한 **데이터 조각** 블레이드에서는 조각에 대한 모든 **작업 실행**이 아래쪽 목록에 표시됩니다. 목록에서 실패한 **작업 실행**을 클릭합니다.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 선택한 작업 실행에 대한 **Activity Run Details** 블레이드에 오류에 대한 세부 정보가 표시됩니다. 이 시나리오에서는 **개체 이름 'emp'가 잘못되었습니다.**가 표시됩니다.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

이 문제를 해결하려면 **emp** 테이블을 [데이터 팩터리 시작][adfgetstarted] 문서의 SQL 스크립트를 사용하여 만듭니다.


### Azure PowerShell cmdlet을 사용하여 오류 해결
1.	**Azure PowerShell**을 시작합니다. 
2.	**AzureResourceManager** 모드로 전환합니다. 데이터 팩터리 cmdlet은 이 모드에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 Failed로 표시됩니다.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] **StartDateTime**을 **Set-AzureDataFactoryPipelineActivePeriod**에서 지정한 StartDateTime 값으로 바꿉니다. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	출력에서 문제 조각(**Status**가 **Failed**로 설정된 조각)에 대한 **Start** 시간을 기록합니다. 
4. 이제 **Get-AzureDataFactoryRun** cmdlet을 실행하여 조각의 작업 실행에 대한 세부 정보를 가져옵니다.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	**StartDateTime** 값은 이전 단계에서 기록한 오류/문제 조각의 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
5. 출력에 오류에 대한 세부 정보가 다음과 같이 표시됩니다.

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighavewalkthrough"></a>연습: Hive/Pig 처리 관련 오류 해결
이 연습에서는 Hive/Pig 처리 관련 오류를 Azure 미리 보기 포털과 Azure PowerShell을 모두 사용하여 해결하는 단계를 제공합니다. 


### 연습: Azure 포털을 사용하여 Pig/Hive 처리 관련 오류 해결
이 시나리오에서는 HDInsight 클러스터의 Hive 처리가 실패하여 데이터 집합이 오류 상태입니다.

1. **데이터 팩터리** 홈페이지의 **데이터 집합** 타일에서 **With errors**를 클릭합니다.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. **Datasets with errors** 블레이드에서 관심 있는 **테이블**을 클릭합니다.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. **테이블** 블레이드에서 **문제 조각** 즉, **상태**가 **실패**인 조각을 클릭합니다.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. **데이터 조각** 블레이드에서 실패한 **작업 실행**을 클릭합니다.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. **ACTIVITY RUN DETAILS** 블레이드에서 HDInsight 처리와 관련된 파일을 다운로드할 수 있습니다. **Status/stderr**에 대한 **다운로드**를 클릭하여 오류에 대한 세부 정보를 포함하는 오류 로그 파일을 다운로드합니다.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### 연습: Azure PowerShell을 사용하여 Pig/Hive 처리 관련 오류 해결
1.	**Azure PowerShell**을 시작합니다. 
2.	**AzureResourceManager** 모드로 전환합니다. 데이터 팩터리 cmdlet은 이 모드에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 Failed로 표시됩니다.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] **StartDateTime**을 **Set-AzureDataFactoryPipelineActivePeriod**에서 지정한 StartDateTime 값으로 바꿉니다. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	출력에서 문제 조각(**Status**가 **Failed**로 설정된 조각)에 대한 **Start** 시간을 기록합니다. 
4. 이제 **Get-AzureDataFactoryRun** cmdlet을 실행하여 조각의 작업 실행에 대한 세부 정보를 가져옵니다.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	**StartDateTime** 값은 이전 단계에서 기록한 오류/문제 조각의 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
5. 출력에 오류에 대한 세부 정보가 다음과 같이 표시됩니다.

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. 위의 출력에 표시된 ID 값을 사용하여 **Save-AzureDataFactoryLog** cmdlet을 실행하고 cmdlet에 **-DownloadLogs** 옵션을 사용하여 로그 파일을 다운로드할 수 있습니다.

## 문제 해결 팁

### 온-프레미스 SQL Server에 연결하지 못함 
게이트웨이가 설치된 컴퓨터에서 SQL Server에 연결할 수 있는지 확인합니다.


1. SQL Server가 설치된 컴퓨터를 ping합니다.
2. 게이트웨이가 설치된 컴퓨터에서 SQL Server 인스턴스에 연결해 봅니다. 이때 Azure 포털에서 SSMS(SQL Server Management Studio)를 사용하여 지정한 자격 증명을 사용합니다.

### 복사 작업 실패
1. 게이트웨이가 설치된 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자를 시작합니다. **게이트웨이 이름**이 **Azure 포털**의 논리 게이트웨이 이름으로 설정되어 있고 **Gateway key status**가 **등록됨**이고 **서비스 상태**가 **시작됨**인지 확인합니다. 
2. **이벤트 뷰어**를 시작합니다. **응용 프로그램 및 서비스 로그**를 확장하고 **데이터 관리 게이트웨이**를 클릭합니다. 데이터 관리 게이트웨이 관련 오류가 있는지 확인합니다. 



## 참고 항목

문서 | 설명
------ | ---------------
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources] | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 완전한 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities] | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell] | 이 문서에서는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리를 모니터링하는 방법에 대해 설명합니다. 
[데이터 팩터리 문제 해결][troubleshoot] | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발\자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 
[Azure 데이터 팩터리 Cmdlet 참조][cmdlet-reference] | 이 참조 콘텐츠에는 모든 **데이터 팩터리 cmdlet**에 대한 내용이 자세히 나와 있습니다.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

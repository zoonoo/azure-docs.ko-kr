<properties 
	pageTitle="Azure PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리" 
	description="Azure PowerShell을 사용하여 사용자가 만든 Azure 데이터 팩터리를 모니터링하고 관리하는 방법에 대해 알아봅니다." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리
다음 표에는 Azure PowerShell을 사용하여 Azure 데이터 팩터리를 모니터링 및 관리하는 데 사용할 수 있는 cmdlet이 나와 있습니다. 

> [WACOM.NOTE] 데이터 팩터리 cmdlet에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조][cmdlet-reference](영문)를 참조하세요. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
지정된 리소스 그룹 내의 Azure 구독에서 특정 데이터 팩터리 또는 모든 데이터 팩터리에 대한 정보를 가져옵니다.
 
###예제 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

이 명령은 리소스 그룹 ADFTutorialResourceGroup의 모든 데이터 팩터리를 반환합니다.
 
###예제 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

이 명령은 리소스 그룹 ADFTutorialResourceGroup의 ADFTutorialDataFactory 데이터 팩터리에 대한 세부 정보를 반환합니다. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
Get-AzureDataFactoryLinkedService cmdlet은 Azure 데이터 팩터리에서 특정 연결된 서비스 또는 모든 연결된 서비스에 대한 정보를 가져옵니다.

### 예제 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 모든 연결된 서비스에 대한 정보를 반환합니다.


DataFactoryName 및 ResourceGroupName 매개 변수 대신 -DataFactory 매개 변수를 사용할 수 있습니다. 이렇게 하면 리소스 그룹과 팩터리 이름을 한 번만 입력한 다음 ResourceGroupName 및 DataFactoryName 모두를 매개 변수로 사용하는 모든 cmdlet에서 데이터 팩터리 개체를 매개 변수로 사용할 수 있습니다.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### 예제 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

이 명령은 Azure 데이터 팩터리 factoryADFTutorialDataFactory에서 연결된 서비스 MyBlobStore에 대한 정보를 반환합니다. 

아래와 같이 -ResourceGroup 및 -DataFactoryName 매개 변수 대신 -DataFactory 매개 변수를 사용할 수 있습니다. 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Get-AzureDataFactoryTable cmdlet은 Azure 데이터 팩터리에서 특정 테이블 또는 모든 테이블에 대한 정보를 가져옵니다. 

### 예제 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 모든 테이블에 대한 정보를 반환합니다.

아래와 같이 -ResourceGroup 및 -DataFactoryName 매개 변수 대신 -DataFactory 매개 변수를 사용할 수 있습니다. 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### 예제 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 EmpTableFromBlob 테이블에 대한 정보를 반환합니다.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Get-AzureDataFactoryPipeline cmdlet은 Azure 데이터 팩터리에서 특정 파이프라인 또는 모든 파이프라인에 대한 정보를 가져옵니다.

### 예제 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 모든 파이프라인에 대한 정보를 반환합니다.

### 예제 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Azure 데이터 팩터리 ADFTutorialDataFactory에서 ADFTutorialPipeline 파이프라인에 대한 정보를 가져옵니다.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Get-AzureDataFactorySlice cmdlet은 StartDateTime에서 EndDateTime 사이에 생성된 Azure 데이터 팩터리의 테이블에 대한 모든 조각을 가져옵니다. Ready 상태인 데이터 조각은 종속 조각에서 사용할 수 있습니다.

다음 표에는 조각의 모든 상태와 해당 설명이 나와 있습니다.

<table border="1">	
	<tr>
		<th align="left">상태</th>
		<th align="left">설명</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>데이터 처리가 아직 시작되지 않았습니다.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>데이터 처리가 진행 중입니다.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>데이터 처리가 완료되었고 데이터 조각이 준비되었습니다.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>조각을 생성하는 실행을 수행하지 못했습니다.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>조각의 처리를 건너뜁니다.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>조각을 생성하는 실행을 다시 시도합니다.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>조각의 데이터 처리 시간이 초과되었습니다.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>데이터 조각을 처리하기 전에 유효성 검사 정책에 따라 유효성 검사가 완료될 때까지 기다리는 중입니다.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>조각의 유효성 검사를 다시 시도합니다.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>조각의 유효성 검사에 실패했습니다.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>JSON 테이블에서 LongRetry를 지정하고 조각에 대한 일반 재시도가 실패한 경우 조각은 이 상태가 됩니다.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>JSON 테이블에 정의된 정책에 따라 조각에 대한 유효성 검사를 수행하는 중입니다.</td>
	</tr>

</table>

각 조각에 대해 더 세부적으로 드릴다운하고 Get-AzureDataFactoryRun 및 Save-AzureDataFactoryLog cmdlet을 사용하여 조각을 생성하는 실행에 대한 추가 정보를 볼 수 있습니다.

### 예제

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

이 명령은 2014-05-20T10:00:00(GMT) 이후 생성된 Azure 데이터 팩터리 ADFTutorialDataFactory의 EmpSQLTable 테이블에 대한 모든 조각을 가져옵니다. 날짜-시간을 Set-AzureDataFactoryPipelineActivePeriod를 실행할 때 지정한 시작 날짜 시간으로 바꿉니다.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Get-AzureDataFactoryRun cmdlet은 Azure 데이터 팩터리에 있는 테이블의 데이터 조각에 대한 모든 실행을 가져옵니다.  Azure 데이터 팩터리의 테이블은 시간 축 위의 조각으로 구성됩니다. 조각의 너비는 일정(매시간/매일)에 따라 결정됩니다. 실행은 조각에 대한 처리 단위입니다. 다시 시도하는 경우나 오류가 발생하여 조각을 다시 실행하는 경우 조각에 대한 실행이 하나 이상 있을 수 있습니다. 조각은 시작 시간으로 식별합니다. 따라서 Get-AzureDataFactoryRun cmdlet의 경우 Get-AzureDataFactorySlice cmdlet의 결과에서 얻은 조각의 시작 시간을 전달해야 합니다.

예를 들어 다음 조각에 대한 실행을 가져오려면 2015-04-02T20:00:00을 사용합니다. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### 예제

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 EmpSQLTable 테이블의 조각에 대해 2014년 5월 21일 오후 4시(GMT)에 시작되는 실행을 모두 가져옵니다.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Save-AzureDataFactoryLog cmdlet은 Pig 또는 Hive 프로젝트의 Azure HDInsight 처리와 연관된 로그 파일이나 사용자 지정 작업에 대한 로그 파일을 로컬 하드 드라이브로 다운로드합니다. 먼저 Get-AzureDataFactoryRun cmdlet을 실행하여 데이터 조각에 대한 작업 실행의 ID를 가져온 다음 이 ID를 사용하여 HDInsight 클러스터와 연관된 BLOB(Binary Large Object)에서 로그 파일을 검색합니다. 

**-DownloadLogs** 매개 변수를 지정하지 않으면 이 cmdlet은 로그 파일의 위치만 반환합니다. 

출력 디렉터리(**-Output** 매개 변수)를 지정하지 않고 **-DownloadLogs** 매개 변수를 지정하면 로그 파일이 기본 **문서** 폴더에 다운로드됩니다. 

출력 폴더(**-Output**)와 함께 **-DownloadLogs** 매개 변수를 지정하면 로그 파일이 지정한 폴더에 다운로드됩니다. 


### 예제 1
이 명령은 ID가 841b77c9-d56c-48d1-99a3-8c16c3e77d39인 작업 실행에 대한 로그 파일을 저장합니다. 여기서 이 작업은 ADF라는 리소스 그룹의 LogProcessingFactory라는 데이터 팩터리에 있는 파이프라인에 속합니다. 로그 파일은 C:\Test 폴더에 저장됩니다. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### 예제 2
이 명령은 로그 파일을 문서 폴더(기본값)에 저장합니다.


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### 예제 3
이 명령은 로그 파일의 위치를 반환합니다. -DownloadLogs 매개 변수는 지정하지 않았습니다. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
Get-AzureDataFactoryGateway cmdlet은 Azure 데이터 팩터리에서 특정 게이트웨이 또는 모든 게이트웨이에 대한 정보를 가져옵니다. 온-프레미스 컴퓨터에 게이트웨이를 설치해야만 온-프레미스 SQL Server를 데이터 팩터리에 연결된 서비스로 추가할 수 있습니다.

### 예제 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 모든 게이트웨이에 대한 정보를 반환합니다.

### 예제 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 ADFTutorialGateway 게이트웨이에 대한 정보를 반환합니다.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
이 cmdlet은 파이프라인에서 처리되는 데이터 조각에 대한 활성 기간을 설정합니다. Set-AzureDataFactorySliceStatus를 사용하는 경우 조각 시작 날짜 및 종료 날짜가 파이프라인의 활성 기간 내에 있는지 확인해야 합니다.

파이프라인이 만들어지면 데이터 처리가 수행되는 기간을 지정할 수 있습니다. 파이프라인의 활성 기간을 지정하면 각 ADF 테이블에 대해 정의된 가용성 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.

### 예제

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

이 명령은 ADFTutoiralPipeline 파이프라인에서 처리되는 데이터 조각에 대한 활성 기간을 2014년 5월 21일 오후 4시(GMT)에서 2014년 5월 22일 오후 4시(GMT) 사이로 설정합니다.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
테이블의 조각에 대한 상태를 설정합니다. 조각 시작 날짜 및 종료 날짜는 파이프라인의 활성 기간 내에 있어야 합니다.

### 지원되는 상태 값
테이블의 각 데이터 조각은 한 여러 단계를 거칩니다. 유효성 검사 정책을 지정했는지 여부에 따라 이러한 단계가 약간 다릅니다.


- 유효성 검사 정책을 지정하지 않은 경우: PendingExecution -> InProgress -> Ready
- 유효성 검사 정책을 지정한 경우: PendingExecution -> Pending Validation -> InProgress -> Ready

다음 표에서는 조각의 가능한 상태에 대해 설명하고 Set-AzureDataFactorySliceStatus를 사용하여 설정할 수 있는지 여부를 알려줍니다.

<table border="1">	
	<tr>
		<th>상태</th>
		<th>설명</th>
		<th>cmdlet을 사용하여 설정할 수 있음</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>데이터 처리가 아직 시작되지 않았습니다.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>데이터 처리가 진행 중입니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>데이터 처리가 완료되었고 데이터 조각이 준비되었습니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>조각을 생성하는 실행을 수행하지 못했습니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>조각의 처리를 건너뜁니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>조각을 생성하는 실행을 다시 시도합니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>데이터 처리 시간이 초과되었습니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>데이터 조각을 처리하기 전에 유효성 검사 정책에 따라 유효성 검사가 완료될 때까지 기다리는 중입니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>조각의 유효성 검사를 다시 시도합니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>조각의 유효성 검사에 실패했습니다.</td>
		<td>N</td>
	</tr>
	</table>


### 지원되는 값 - 업데이트 유형
Azure 데이터 팩터리의 각 테이블에 대해 조각의 상태를 설정할 때 상태 업데이트가 테이블에만 적용되는지 또는 상태 업데이트가 영향받는 모든 조각으로 전파되는지를 지정해야 합니다.

<table border="1">	
	<tr>
		<th>업데이트 유형</th>
		<th>설명</th>
		<th>cmdlet을 사용하여 설정할 수 있음</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>지정된 시간 범위의 테이블에 대한 각 조각의 상태를 설정합니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>파이프라인에서 작업의 입력 테이블로 사용되는 테이블과 모든 종속(업스트림) 테이블에 대한 각 조각의 상태를 설정합니다.</td>
		<td>Y</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Suspend-AzureDataFactoryPipeline cmdlet은 Azure 데이터 팩터리에서 지정된 파이프라인을 일시 중단합니다. 나중에 Resume-AzureDataFactoryPipeline cmdlet을 사용하여 파이프라인을 다시 시작할 수 있습니다.

### 예제

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 ADFTutorialPipeline 파이프라인을 일시 중단합니다.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Resume-AzureDataFactoryPipeline cmdlet은 Azure 데이터 팩터리에서 현재 일시 중단되어 있는 지정된 파이프라인을 다시 시작합니다. 

### 예제

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

이 명령은 Azure 데이터 팩터리 ADFTutorialDataFactory에서 이전에 Suspend-AzureDataFactoryPipeline 명령을 사용하여 일시 중단한 ADFTutorialPipeline 파이프라인을 다시 시작합니다.

## 참고 항목

문서 | 설명
------ | ---------------
[Azure 미리 보기 포털을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-portal] | 이 문서에서는 Azure 미리 보기 포털을 사용하여 Azure 데이터 팩터리를 모니터링 및 관리하는 방법에 대해 설명합니다.
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문) | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial](영문) | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 종단 간 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities](영문) | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot](영문) | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference](영문) | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 
[Azure 데이터 팩터리 Cmdlet 참조][cmdlet-reference] | 이 참조 콘텐츠에는 모든 **데이터 팩터리 cmdlet**에 대한 내용이 자세히 나와 있습니다.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--HONumber=35.2-->

<!--HONumber=46--> 

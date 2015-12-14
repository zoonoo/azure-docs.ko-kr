<properties 
	pageTitle="Azure 데이터 팩터리 문제 해결" 
	description="Azure 데이터 팩터리 사용과 관련된 문제를 해결하는 방법에 대해 알아봅니다." 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# 데이터 팩터리 문제 해결
Azure 클래식 포털 또는 Azure PowerShell cmdlet을 사용하여 Azure Data Factory 문제를 해결할 수 있습니다. 이 항목의 연습에서는 Data Factory에서 발생하는 오류를 Azure 클래식 포털을 사용하여 신속하게 해결하는 방법을 보여 줍니다.

## 문제: 데이터 팩터리 cmdlet을 실행할 수 없음
이 문제를 해결하려면 Azure 모드를 **AzureResourceManager**로 전환합니다.

**Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager

## 문제: 데이터 팩터리 cmdlet을 실행할 때 권한 없음 오류 발생
Azure PowerShell에서 올바른 Azure 계정 또는 구독을 사용하고 있지 않습니다. 다음 cmdlet을 사용하여 Azure PowerShell에서 사용할 올바른 Azure 계정 및 구독을 선택합니다.

1. Add-AzureAccount - 올바른 사용자 ID 및 암호를 사용합니다.
2. Get-AzureSubscription - 계정의 모든 구독을 확인합니다. 
3. Select-azuresubscription <subscription name> - 올바른 구독을 선택합니다. Azure 포털에서 데이터 팩터리를 만드는 데 사용한 것과 동일한 구독을 사용합니다.

## 문제: Azure 클래식 포털에서 데이터 게이트웨이 빠른 설치를 시작하지 못함
데이터 게이트웨이 빠른 설치를 수행하려면 Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저가 필요합니다. 빠른 설치를 시작하지 못한 경우 다음을 수행할 수 있습니다.

1. 다른 브라우저에서 실패하는 경우 Internet Explorer로 전환합니다. 또는
2. 포털에서 동일한 블레이드에 표시된 "수동 설치" 링크를 사용하여 설치를 수행한 다음 화면에 제공된 키를 복사하고 데이터 관리 게이트웨이 구성이 준비되면 붙여넣습니다. 시작되지 않는 경우 "Microsoft 데이터 관리 게이트웨이"에 대한 시작 메뉴를 확인하고, 시작되면 키를 붙여넣습니다. 


## 문제: Azure 클래식 포털에서 자격 증명 관리자를 시작하지 못함
Azure 클래식 포털을 통해 SQL Server 연결된 서비스를 설치 또는 업데이트하는 경우 보안을 보장하기 위해 자격 증명 관리자 응용 프로그램이 시작됩니다. Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저가 필요합니다. 다른 브라우저에서 실패하는 경우 Internet Explorer로 전환할 수 있습니다.

## 문제: 온-프레미스 SQL Server에 연결하지 못함 
게이트웨이가 설치된 컴퓨터에서 SQL Server에 연결할 수 있는지 확인합니다. 게이트웨이가 설치된 컴퓨터에서 다음을 수행할 수 있습니다.

1. SQL Server가 설치된 컴퓨터를 ping합니다. 또는
2. SQL Server 인스턴스에 연결을 시도합니다. 이때 Azure 클래식 포털에서 SSMS(SQL Server Management Studio)를 사용하여 지정한 자격 증명을 사용합니다.


## 문제: 입력 조각이 무기한 PendingExecution 또는 PendingValidation 상태임

몇 가지 이유로 인해 조각이 **PendingExecution** 또는 **PendingValidation** 상태일 수 있습니다. 일반적인 이유 중 하나는 **external** 속성이 **true**로 설정되지 않은 것입니다. Azure 데이터 팩터리의 외부에서 생성된 데이터 집합은 **external** 속성으로 표시되어야 합니다. 이 속성은 데이터가 외부이며 데이터 팩터리 내의 파이프라인에서 지원되지 않음을 나타냅니다. 해당 저장소에서 데이터를 사용할 수 있으면 데이터 조각이 **Ready**로 표시됩니다.

**external** 속성의 사용 방법은 다음 예를 참조하십시오. external을 true로 설정할 경우 선택적으로 **externalData***를 지정할 수 있습니다.

이 속성에 대한 자세한 내용은 [JSON 스크립팅 참조][json-scripting-reference]의 테이블 항목을 참조하십시오.
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "dataDelay": "00:10:00",
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

 오류를 해결하려면 입력 테이블의 JSON 정의에 **external** 속성과 **externalData** 섹션을 추가하고 테이블을 다시 만듭니다.

## 문제: 하이브리드 복사 작업 실패
자세히 알아보려면 다음을 수행하십시오.

1. 게이트웨이가 설치된 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자를 시작합니다. **게이트웨이 이름**이 **Azure 클래식 포털**의 논리 게이트웨이 이름으로 설정되어 있고 **게이트웨이 키 상태**가 **등록됨**이고 **서비스 상태**가 **시작됨**인지 확인합니다. 
2. **이벤트 뷰어**를 시작합니다. **응용 프로그램 및 서비스 로그**를 확장하고 **데이터 관리 게이트웨이**를 클릭합니다. 데이터 관리 게이트웨이 관련 오류가 있는지 확인합니다. 

## 문제: 주문형 HDInsight 프로비저닝 실패(오류)

HDInsightOnDemandLinkedService 형식의 연결된 서비스를 사용하는 경우 Azure Blob 저장소를 가리키는 linkedServiceName을 지정해야 합니다. 이 저장소 계정은 주문형 HDInsight 클러스터에 대한 모든 로그 및 지원 파일을 복사하는 데 사용됩니다. 때로는 HDInsight에서 주문형 프로비저닝을 수행하는 작업이 다음 오류로 인해 실패할 수 있습니다.

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

이 오류는 일반적으로 linkedServiceName에 지정된 저장소 계정의 위치가 HDInsight 프로비저닝이 발생하는 위치와 동일한 데이터 센터 위치에 있지 않음을 나타냅니다. 예를 들어 Azure 데이터 팩터리 위치가 미국 서부이고 주문형 HDInsight 프로비저닝이 미국 서부에서 발생하지만 Azure Blob 저장소 계정 위치가 미국 동부로 설정된 경우 주문형 프로비저닝이 실패합니다.

또한 주문형 HDInsight에서 추가 저장소 계정을 지정할 수 있는 두 번째 JSON 속성 additionalLinkedServiceNames가 있습니다. 이러한 추가 연결된 저장소 계정은 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 그렇지 않으면 동일한 오류로 인해 실패합니다.



## 문제: 사용자 지정 작업 실패
Azure 데이터 팩터리에서 사용자 지정 작업(파이프라인 작업 유형 CustomActivity)을 사용하는 경우 사용자 지정 응용 프로그램은 HDInsight에 대해 지정된 연결된 서비스에서 맵 전용 스트리밍 MapReduce 작업으로 실행됩니다.

사용자 지정 작업이 실행될 때 Azure 데이터 팩터리는 HDInsight 클러스터에서 해당 출력을 캡처하고 Azure Blob 저장소 계정의 *adfjobs* 저장소 컨테이너에 저장합니다. 오류 발생 시 **stderr** 출력 텍스트 파일에서 텍스트를 읽을 수 있습니다. 웹 브라우저를 통해 Azure 클래식 포털 자체에서 파일을 액세스하고 읽을 수 있습니다. 또는 저장소 탐색기 도구를 사용하여 Azure Blob 저장소의 저장소 컨테이너에 저장된 파일에 액세스합니다.

특정 사용자 지정 작업에 대한 로그를 열거하고 읽으려면 이 페이지의 뒷부분에 설명된 연습 중 하나를 따를 수 있습니다. 요약하면 다음과 같습니다:

1.  Azure 클래식 포털에서 **찾아보기**를 통해 Data Factory를 찾습니다.
2.  **다이어그램** 단추를 사용하여 데이터 팩터리 다이어그램을 보고, 사용자 지정 작업이 있는 특정 **파이프라인**을 따르는 **데이터 집합** 테이블을 클릭합니다. 
3.  **테이블** 블레이드에서 조사할 시간 프레임의 **문제 조각**을 통해 관심 조각을 클릭합니다.
4.  자세한 **데이터 조각** 블레이드가 나타나고, 조각의 여러 **작업 실행**을 나열할 수 있습니다. 목록에서 **작업**을 클릭합니다. 
5.  **작업 실행 세부 정보** 블레이드가 표시됩니다. 블레이드 중간에 **오류 메시지**가 나열되고, 블레이드 맨 아래에 해당 작업 실행과 관련된 여러 **로그 파일**이 나열됩니다.
	- Logs/system-0.log
	- 상태
	- Status/exit
	- 상태/stderr
	- 상태/stdout

6. 목록에서 첫 번째 **로그 파일**을 클릭하면 새 블레이드에서 로그가 열리고 읽을 수 있도록 전체 텍스트가 표시됩니다. 각 로그를 클릭하여 로그의 텍스트를 검토합니다. 텍스트 뷰어 블레이드가 열립니다. **다운로드** 단추를 클릭하여 선택적 오프라인 보기에 사용할 텍스트 파일을 다운로드할 수 있습니다.

사용자 지정 작업에서 발생하는 **일반적인 오류** 중 하나는 패키지 실행 실패(종료 코드 '1')입니다. 자세한 내용은 'wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr'을 참조하십시오.

이러한 종류의 오류에 대한 자세한 내용을 보려면 **stderr** 파일을 엽니다. 여기에 표시되는 일반적인 오류 중 하나는 다음과 같습니다. INFO mapreduce.Job: Task Id : attempt\_1424212573646\_0168\_m\_000000\_0, Status : FAILED AttemptID:attempt\_1424212573646\_0168\_m\_000000\_0 Timed out after 600 secs

예를 들어 30분 이상 기간에 작업이 3회 다시 시도된 경우 이 동일한 오류가 여러 번 나타날 수도 있습니다.

이 시간 초과 오류는 600초(10분) 시간 초과가 발생했음을 나타냅니다. 이는 일반적으로 사용자 지정 .Net 응용 프로그램이 10분간 상태 업데이트를 실행하지 않았음을 의미합니다. 응용 프로그램이 너무 오랫동안 다른 작업을 기다리면서 작동 중지 또는 중단되는 경우 10분 시간 초과는 응용 프로그램이 무기한 대기하여 Azure 데이터 팩터리 파이프라인을 지연시키지 않도록 하는 보안 메커니즘입니다.

이 시간 초과는 사용자 지정 작업에 연결된 HDInsight 클러스터의 구성에서 만들어집니다. 설정은 **mapred.task.timeout**이며, http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml에서 Apache 기본 설정에 문서화된 대로 기본값은 600000밀리초입니다.

HDInsight 프로비저닝 클러스터를 프로비전할 때 기본값을 변경하여 이 기본값을 재정의할 수 있습니다. Azure 데이터 팩터리 및 **HDInsight 주문형** 연결된 서비스를 사용하는 경우 HDInsightOnDemandLinkedService JSON 속성 근처에 JSON 속성을 추가할 수 있습니다. 예를 들어 이 JSON 속성을 사용하여 값을 20분으로 늘릴 수 있습니다.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

이러한 Map Reduce 구성 속성을 편집하기 위한 JSON의 전체 예제 및 자세한 컨텍스트는 https://msdn.microsoft.com/library/azure/dn893526.aspx에서 MSDN 설명서의 예제 #3을 참조하십시오.

## 문제: 오류로 인한 PowerShell 요청 실패(오류 400 잘못된 요청 "등록된 리소스 공급자를 찾을 수 없습니다.")

2015년 3월 10일부터 Azure 데이터 팩터리 PowerShell 초기 비공개 미리 보기 버전인 2014-05-01-preview, 2014-07-01-preview 및 2014-08-01-preview는 사용이 중단됩니다. 이제 URL http://go.microsoft.com/?linkid=9811175&clcid=0x409의 다운로드와 같은 Azure PowerShell 다운로드에 포함된 최신 버전의 ADF cmdlet을 사용하는 것이 좋습니다.

사용이 중단된 버전의 Azure PowerShell SDK를 사용하는 경우 다음과 같은 오류가 표시될 수 있습니다.

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> 연습: 데이터 복사 관련 오류 해결
이 연습에서는 Data Factory 시작 문서의 자습서에서 오류를 발생시키고 Azure 클래식 포털을 사용하여 이 오류를 해결하는 방법을 알아봅니다.

### 필수 조건
1. [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 완료합니다.
2. **ADFTutorialDataFactory**가 Azure SQL 데이터베이스의 **emp** 테이블에 데이터를 생성하는지 확인합니다.  
3. 이제 Azure SQL 데이터베이스에서 **emp** 테이블을 삭제합니다(**drop table emp**). 그러면 오류가 발생합니다.
4. **Azure PowerShell**에서 다음 명령을 실행하여 파이프라인의 활성 기간을 업데이트합니다. 그러면 파이프라인에서 더 이상 없는 **emp** 테이블에 데이터를 쓰려고 합니다.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	**StartDateTime**을 현재 날짜로 바꾸고 **EndDateTime** 값을 다음 날로 바꿉니다.


### Azure 포털을 사용하여 오류 해결

1.	[Azure 포털][azure-portal]에 로그인합니다. 
2.	**시작 보드**에서 **ADFTutorialDataFactory**를 클릭합니다. **시작 보드**에 데이터 팩터리 링크가 표시되지 않는 경우 **찾아보기** 허브를 클릭한 다음 **모두**를 클릭합니다. **찾아보기** 블레이드에서 **데이터 팩터리…**를 클릭한 다음 **ADFTutorialDataFactory**를 클릭합니다.
3.	**데이터 집합** 타일에 **With errors(오류 있음)**가 표시됩니다. **With errors(오류 있음)**를 클릭합니다. **Datasets with errors(오류가 있는 데이터 집합)** 블레이드가 표시됩니다.

	![오류가 있는 데이터 팩터리 링크][image-data-factory-troubleshoot-with-error-link]

4. **Datasets with errors(오류가 있는 데이터 집합)** 블레이드에서 **EmpSQLTable**을 클릭하여 **테이블** 블레이드를 표시합니다.

	![오류가 있는 데이터 집합 블레이드][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. **테이블** 블레이드에서는 문제 조각, 즉 오류가 있는 조각이 맨 아래의 **Problem slices(문제 조각)** 목록에 표시됩니다. **Recent slices(최근 조각)** 목록에서 오류가 있는 최근 조각을 확인할 수도 있습니다. **Problem slices(문제 조각)** 목록에서 조각을 클릭합니다.

	![문제 조각이 있는 테이블 블레이드][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	특정 문제가 아니라 **Problem slices(문제 조각)**를 클릭하면 **데이터 조각** 블레이드가 표시되며, 여기서 **특정 문제 조각**을 클릭하여 선택한 데이터 조각에 대한 **데이터 조각** 슬라이드를 표시합니다.

6. **EmpSQLTable**의 **데이터 조각** 블레이드에서 조각에 대한 모든 **작업 실행**이 맨 아래 목록에 표시됩니다. 목록에서 실패한 **작업 실행**을 클릭합니다.

	![활성 실행이 있는 데이터 조각 블레이드][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 선택한 작업 실행의 **작업 실행 세부 정보** 블레이드에 오류에 대한 세부 정보가 표시됩니다. 이 시나리오에서는 **잘못된 개체 이름 'emp'**가 표시됩니다.

	![오류가 있는 작업 실행 세부 정보][image-data-factory-troubleshoot-activity-run-with-error]

이 문제를 해결하려면 [데이터 팩터리 시작][adfgetstarted] 문서의 SQL 스크립트를 사용하여 **emp** 테이블을 만듭니다.


### Azure PowerShell cmdlet을 사용하여 오류 해결
1.	**Azure PowerShell**을 시작합니다. 
2.	**AzureResourceManager** 모드로 전환합니다. 데이터 팩터리 cmdlet은 이 모드에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 Failed로 표시됩니다.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	**StartDateTime**을 **Set-AzureDataFactoryPipelineActivePeriod**에서 지정한 StartDateTime 값으로 바꿉니다.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	출력에서 문제 조각(**상태**가 **Failed**로 설정된 조각)에 대한 **시작** 시간을 기록합니다. 
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

 

## <a name="pighivewalkthrough"></a> 연습: Hive/Pig 처리 관련 오류 해결
이 연습에서는 Hive/Pig 처리 관련 오류를 Azure 포털과 Azure PowerShell을 모두 사용하여 해결하는 단계를 제공합니다.


### 연습: Azure 클래식 포털을 사용하여 Pig/Hive 처리 관련 오류 해결
이 시나리오에서는 HDInsight 클러스터의 Hive 처리가 실패하여 데이터 집합이 오류 상태입니다.

1. **데이터 팩터리** 홈페이지의 **데이터 집합** 타일에서 **With errors(오류 있음)**를 클릭합니다.

	![데이터 집합 타일의 오류 있음 링크][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. **Datasets with errors(오류가 있는 데이터 집합)** 블레이드에서 관심 있는 **테이블**을 클릭합니다.

	![오류가 있는 데이터 집합 블레이드][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. **테이블** 블레이드에서 **상태**가 **Failed**로 설정된 **문제 조각**을 클릭합니다.

	![문제 조각이 있는 테이블][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. **데이터 조각** 블레이드에서 실패한 **작업 실행**을 클릭합니다.

	![실패한 실행이 있는 데이터 조각][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. **작업 실행 세부 정보** 블레이드에서 HDInsight 처리와 관련된 파일을 다운로드할 수 있습니다. **Status/stderr**에 대한 **다운로드**를 클릭하여 오류에 대한 세부 정보를 포함하는 오류 로그 파일을 다운로드합니다.

	![다운로드 링크가 있는 작업 실행 세부 정보][image-data-factory-troubleshoot-activity-run-details]

    
### 연습: Azure PowerShell을 사용하여 Pig/Hive 처리 관련 오류 해결
1.	**Azure PowerShell**을 시작합니다. 
2.	**AzureResourceManager** 모드로 전환합니다. 데이터 팩터리 cmdlet은 이 모드에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 Failed로 표시됩니다.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	**StartDateTime**을 **Set-AzureDataFactoryPipelineActivePeriod**에서 지정한 StartDateTime 값으로 바꿉니다.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	출력에서 문제 조각(**상태**가 **Failed**로 설정된 조각)에 대한 **시작** 시간을 기록합니다. 
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



[adfgetstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_1203_2015-->
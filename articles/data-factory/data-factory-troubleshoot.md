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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 데이터 팩터리 문제 해결
Azure 포털 또는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리 문제를 해결할 수 있습니다. 이 항목의 연습에서는 데이터 팩터리에서 발생하는 오류를 Azure 포털을 사용하여 신속하게 해결하는 방법을 보여 줍니다.

## 문제: 데이터 팩터리 cmdlet을 실행 하는 수 없습니다.
이 문제를 해결 하려면 Azure 모드 전환 **AzureResourceManager**:

시작 **Azure PowerShell** 전환 하려면 다음 명령을 실행 하는 **AzureResourceManager** 모드입니다. 사용할 수 있는 Azure 데이터 팩터리 cmdlet는 **AzureResourceManager** 모드입니다.

         switch-azuremode AzureResourceManager

## 문제: 권한이 없음된 오류가 데이터 팩터리 cmdlet을 실행할 때
Azure PowerShell을 사용 하지 않을 오른쪽 Azure 계정 또는 구독을 사용 중인. 다음 cmdlet를 사용 하 여 오른쪽 Azure 계정 및 Azure powershell을 사용 하 여 구독을 선택 합니다.

1. 추가-AzureAccount-사용 하 여 올바른 사용자 ID 및 암호
2. Get-azuresubscription-계정에 대 한 모든 구독을 확인 합니다. 
3. Select-azuresubscription <subscription name> -오른쪽 구독을 선택 합니다. 동일한 Azure 미리 보기 포털에서 데이터 팩터리를 만들려면 사용 하나를 사용 합니다.

## 문제: Azure 포털에서 데이터 게이트웨이 Express 설치를 시작할 수 없습니다
Express 설치 프로그램에 대 한 데이터 게이트웨이 Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저에 필요합니다. Express 설치를 시작에 실패 하는 경우 다음을 할 수 있습니다.

1. 다른 브라우저와 함께 실패 하는 경우 Internet Explorer로 전환 합니다. 또는
2. 포털에서 동일한 블레이드에 표시 된 "수동 설치" 링크를 사용 하 여 하 여 설치를 수행 하 고 화면에서 제공 되는 키를 복사 하 고 데이터 관리 게이트웨이 구성 준비가 되었을 때를 붙여넣습니다. 를 시작 하지 않는 경우 "Microsoft 데이터 관리 게이트웨이"에 대 한 시작 메뉴를 확인 하 고 시작 하는 경우 키에 붙여넣습니다. 


## Azure 포털에서 자격 증명 관리자를 시작 하려면 문제: 실패
설정한 경우에 또는 보안을 보장 하기 위해 SQL Server 연결 된 서비스 자격 증명 관리자 응용 프로그램이 Azure 포털을 통해 업데이트를 시작 합니다. Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저 필요합니다. 다른 브라우저와 함께 실패 하는 경우 Internet Explorer로 전환할 수 있습니다.

## 문제: 온-프레미스 SQL Server에 연결할 수 없다 
게이트웨이가 설치된 컴퓨터에서 SQL Server에 연결할 수 있는지 확인합니다. 게이트웨이가 설치 된 컴퓨터에서 할 수 있습니다.

1. SQL Server를 설치한 컴퓨터를 ping 합니다. 또는
2. SQL Server Management Studio (SSMS)를 사용 하 여 Azure 포털에서 지정한 자격 증명을 사용 하 여 SQL Server 인스턴스에 연결 하십시오.


## 문제: 입력 조각은 PendingExecution 또는 PendingValidation 상태에 대 한 적이

조각에 있을 수 있습니다 **PendingExecution** 또는 **PendingValidation** 점은 다양 한 이유로 인해 일반적인 이유 중 하나는 상태는 **waitOnExternal** 속성에 지정 되지 않은 **가용성** 첫번째 테이블/데이터 집합의 파이프라인에서 섹션입니다. Azure 데이터 팩터리 범위 밖에 서 생성 되는 모든 데이터 집합으로 표시 해야 **waitOnExternal** 아래의 속성 **가용성** 섹션. 이 데이터가 외부 및 데이터 팩터리 내에서 모든 파이프라인에서 백업 되지 않음을 나타냅니다. 데이터 조각으로 표시 된 **준비** 해당 저장소에 데이터를 사용할 수 있습니다.

다음 예제에서 사용 하기 위해 참조는 **waitOnExternal** 속성입니다. 지정할 수 있습니다 **waitOnExternal {}** 기본값 사용 되도록 섹션에서 속성에 대 한 값을 설정 하지 않고 있습니다.

테이블의 항목을 참조 [JSON 스크립팅 참조][json-scripting-reference] 이 속성에 대 한 자세한 내용은 합니다.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 이 오류를 해결 하려면 추가 **waitOnExternal** 입력된 테이블의 JSON 정의에 섹션 및 테이블을 다시 만듭니다.

## 문제: 하이브리드 복사 작업이 실패
자세한 내용을 알려면:

1. 게이트웨이가 설치 된 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자를 시작 합니다. 확인는 **게이트웨이 이름** 에 논리적 게이트웨이 이름으로 설정 됩니다는 **Azure 포털**, **게이트웨이 키 상태** 은 **등록** 및 **서비스 상태** 은 **시작 됨**. 
2. 시작 **이벤트 뷰어**. 확장 하 고 **응용 프로그램 및 서비스 로그** 클릭 하 고 **데이터 관리 게이트웨이**. 데이터 관리 게이트웨이 관련 오류가 있는지 확인합니다. 

## 문제: HDInsight 프로 비전 오류와 함께 실패 요청 시에

HDInsightOnDemandLinkedService 형식의 연결 된 서비스를 사용할 때 Azure Blob 저장소를 가리키는 linkedServiceName를 지정 해야 합니다. 모든 로그 및 주문형 HDInsight 클러스터에 대 한 지원 파일을 복사 하려면이 저장소 계정 사용 됩니다. 때로는 HDInsight에서 주문형 프로비저닝 하지 않는 작업은 다음 오류와 함께 실패할 수 있습니다.

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

이 오류는 일반적으로 linkedServiceName에 지정 된 저장소 계정의 위치 HDInsight 프로 비전 이유가 무엇입니까 동일한 데이터 센터 위치에 있지 않음을 나타냅니다. 예 Azure 데이터 팩터리 위치가 미국 서 부 및에서 발생 하는 주문형 HDInsight 프로 비전 하는 경우 미국 서 부 있지만 Azure blob 저장소 계정 위치 미국 동부로 설정 된, 주문형 프로비저닝 실패 합니다.

또한 방법이 두번째 JSON 속성 additionalLinkedServiceNames 여기서 주문형 HDInsight에 추가 저장소 계정을 지정할 수 있습니다. 이러한 추가 연결 된 저장소 계정은 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 또는 동일한 오류와 함께 실패 합니다.



## 문제: 사용자 지정 작업 실패
Azure 데이터 팩터리 (파이프라인 활동 유형 CustomActivity)에서 사용자 지정 활동을 사용 하는 경우 사용자 지정 응용 프로그램으로 실행 됩니다 지정된 된 연결 된 서비스에서 HDInsight에 지도 MapReduce 작업을 스트리밍.

사용자 지정 활동을 실행 하는 경우 Azure 데이터 팩터리 수 HDInsight 클러스터에서 출력을 캡처 및 저장에 *adfjobs* Azure Blob 저장소 계정의 저장소 컨테이너입니다. 오류 발생 시에서 텍스트를 읽을 수 **stderr** 오류가 발생 한 후 텍스트 파일을 출력 합니다. 파일에 액세스할 수 있고 Azure 포털에서 자체 웹 브라우저에서 또는 Azure Blob 저장소에는 저장소 컨테이너에 직접 보관 파일에 액세스 하려면 저장소 탐색기 도구를 사용 하 여 읽을 수 있는 됩니다.

열거 하 고 특정 사용자 지정 활동에 대 한 로그를 읽을 수 중 하나에 따라 알 수 있듯이 연습 나중에이 페이지에서. 요약:

1.  Azure 포털에서 **찾아보기** 데이터 팩터리를 찾으려고 합니다.
2.  사용 하 여는 **다이어그램** 데이터 팩터리 다이어그램을 확인 하는 단추를 클릭할는 **Dataset** 특정 뒤에 오는 테이블 **파이프라인** 사용자 지정 활동이 있습니다. 
3.  에 **테이블** 블레이드를 클릭에 대 한 관심의 조각에는 **문제 조각** 조사 해야 시간 프레임에 대 한 합니다.
4.  자세한 **데이터 조각을** 블레이드 나타나고 배수를 나열할 수 **활동을 실행** 는 조각에 대 한 합니다. 클릭 된 **활동** 목록에서. 
5.   **작업 실행 세부 정보** 블레이드가 표시 됩니다. 나열 하는 **오류 메시지** 블레이드를 및 여러 중간에 **로그 파일** 관련 된 해당 활동을 실행 하 여 블레이드 맨 아래에 나열 합니다.
	- 로그/시스템-0.log
	- 상태
	- 상태/종료
	- 상태/stderr
	- 상태/stdout

6. 첫번째 클릭 **로그 파일** 목록과 로그에서 항목 읽기에 표시 되는 전체 텍스트에 새 블레이드가 열립니다. 각 하나를 클릭 하 여 각 로그의 텍스트를 검토 합니다. 텍스트 뷰어 블레이드가 열립니다. 클릭할 수는 **다운로드** 선택적 오프 라인 보기에 대 한 텍스트 파일을 다운로드 하는 단추입니다.

하나의 **일반적인 오류** 에서 사용자 지정 활동은 패키지 실행 실패 한 종료 코드 '1'. 참조 ' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/상태/stderr '에 대 한 자세한 내용은 합니다.

이러한 종류의 오류에 대 한 자세한 정보를 보려면 열은 **stderr** 파일입니다. 여기에 표시 하는 일반적인 오류 중 하나는 이와 같은 시간 초과 상태: 정보 mapreduce 합니다. 작업: 작업 Id: attempt_1424212573646_0168_m_000000_0, 상태: 실패 한 AttemptID:attempt_1424212573646_0168_m_000000_0 이후에 시간 초과 600 초

이 오류는 작업에 3 번 재시도 등 30 개 이상의 시간 (분) 동안 여러 번 나타날 수 있습니다.

이 시간 초과 오류 600 초 (10 분)을 나타내는 시간 초과 발생 했습니다. 일반적으로이 사용자 지정.Net 응용 프로그램에 10 분에 대 한 모든 상태 업데이트를 발급 되지 않으며 의미 합니다. 응용 프로그램 내어쓰기 되었거나 제한 시간은 무기한 대기 하 고 Azure 데이터 팩터리 파이프라인을 연기 하지 못하도록 하는 보안 메커니즘 너무 긴 10 분 기다리는 것에 중단 되었습니다.

이 시간 초과 사용자 지정 활동에 연결 된 HDInsight 클러스터의 구성에서 시작 됩니다. 설정이 **mapred.task.timeout**, Apache 기본 설정을 여기에 명시 된 대로 600000 밀리초 기본값: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

하면 재정의이 기본 HDInsight 프로 비전 클러스터를 프로 비전 시 기본값을 변경 하 여 됩니다. Azure 데이터 팩터리를 사용 하는 경우 및 **주문형 HDInsight** 서비스에 연결 하려면 HDInsightOnDemandLinkedService JSON 속성 근처 JSON 속성을 추가할 수 있습니다. 예를들어, 20 분이 JSON 속성을 사용 하 여 값을 늘릴 수 있습니다.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

자세한 컨텍스트 및 이러한 맵을 편집 하는 JSON의 전체 예제에 대 한 MSDN 설명서를 여기에서 구성 속성 참조 예제 #3 줄이기 https://msdn.microsoft.com/library/azure/dn893526.aspx

## 문제: PowerShell 요청 오류와 함께 실패 오류 400 잘못 된 요청 "등록 된 리소스 공급자 발견..."

2015 년 3 월 10 년을 기준으로 Azure 데이터 팩터리 PowerShell 초기 비공개 미리 보기 버전 버전 2014-05-01-미리 보기, 2014-07-01-미리 보기 및 2014-08-01-미리 보기 사용 중단 되지 것입니다. 이제는 Azure PowerShell 다운로드,이 URL에서 다운로드 하는 등의 구성 요소인 ADF cmdlet의 최신 버전을 사용 하는 것이 좋습니다. http://go.microsoft.com/?linkid=9811175&clcid=0x409

지원 되지않는 버전의 Azure PowerShell SDK를 사용 하는 경우에 다음과 같은 오류가 나타날 수 있습니다.

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


## <a name="copywalkthrough"></a> 연습: 데이터 복사와 오류 해결
이 연습에서는 데이터 팩터리 시작 문서의 자습서에서 오류가 일어나게 하고 Azure 포털을 사용하여 이 오류를 해결하는 방법을 알아봅니다.

### 필수 조건
1. 에 대 한 자습서를 완료는 [Azure 데이터 팩터리 시작][adfgetstarted] 문서입니다.
2. 확인 하는 **ADFTutorialDataFactory** 에서 데이터를 생성는 **emp** Azure SQL 데이터베이스의 테이블.  
3. 이제 삭제는 **emp** 테이블 (* * 놓기 테이블 emp * *)는 Azure SQL 데이터베이스에서. 그러면 오류가 발생합니다.
4. 다음 명령을 실행 하는 **Azure PowerShell** 파이프라인에 대 한 활성 기간을 업데이트 하 여 데이터를 쓰려고 시도 **emp** 더이상 존재 하지 않는 테이블입니다.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]대체 <b>시작 날짜 시간</b> 은 현재 날짜를 사용 하 여 값 및 <b>EndDateTime</b> 다음날을 사용 하 여 값입니다.


### Azure 미리 보기 포털을 사용하여 오류 해결

1.	로그인을 [Azure 미리 보기 포털][azure-preview-portal]. 
2.	클릭 하 여 **ADFTutorialDataFactory** 에서 **시작 보드**. 링크를 데이터 팩터리 보이지 않으면는 **시작 보드**, 를 클릭 하 여 **찾아보기** 허브 및 클릭 **모든**. 클릭 하 여 **데이터 팩터리...** 에 **찾아보기** 블레이드를 클릭 하 고 **ADFTutorialDataFactory**.
3.	표시 되 고 지 사항 **오류와 함께** 에 **데이터 집합** 바둑판식으로 배열 합니다. 클릭 하 여 **오류와 함께**. 표시 되어야 **오류와 함께 데이터 집합** 블레이드입니다.

	![오류 링크를 사용 하 여 데이터 팩터리][image-data-factory-troubleshoot-with-error-link]

4. 에 **데이터 집합** 오류 블레이드 클릭 **EmpSQLTable** 볼 수는 **테이블** 블레이드.

	![오류 블레이드와 데이터 집합][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. 에 **테이블** 블레이드에서 문제 조각, 즉, 조각에는 오류로 표시 됩니다는 **문제 조각** 아래쪽 목록입니다. 오류를 사용 하 여 모든 최근 슬라이스도 볼 수 있습니다는 **최근 조각** 목록입니다. 조각에서 클릭는 **문제 조각** 목록입니다.

	![문제 슬라이스로 테이블 블레이드][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	클릭 하면 **문제 조각** 표시 됩니다 (특정 문제의 경우)에 없는 **데이터 조각이** 블레이드 및 클릭 한 다음는 **특정 문제 조각** 볼 수는 **데이터 조각을** 슬라이드 선택한 데이터 조각에 대 한 합니다.

6. 에 **데이터 조각을** 에 대 한 블레이드 **EmpSQLTable**, 모든 표시 **활동 실행** 아래쪽 목록에서 조각에 대 한. 클릭할는 **활동 실행** 실패 하 고 목록에서.

	![활성 실행 된 데이터 조각 블레이드][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 에 **작업 실행 세부 정보** 사용자가 선택한 활동에 대 한 블레이드 실행, 오류에 대 한 세부 정보 표시 되어야 합니다. 이 시나리오에서는 참조: **잘못 된 개체 이름 'emp'**.

	![오류와 함께 세부 정보를 실행 하는 활동][image-data-factory-troubleshoot-activity-run-with-error]

이 문제를 해결 하려면 만들기의 **emp** SQL을 사용 하 여 테이블에서 스크립팅할 [데이터 팩터리 시작][adfgetstarted] 문서.


### Azure PowerShell cmdlet을 사용하여 오류 해결
1.	시작 **Azure PowerShell**. 
2.	전환 **AzureResourceManager** 모드 데이터 팩터리 cmdlet으로는이 모드 에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 상태와 함께 조각을 표시 되어야 합니다: 실패 했습니다.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]대체 **시작 날짜 시간** 에 지정한 값의 시작 날짜 시간으로는 **집합 AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	참고는 **시작** 문제 조각에 대 한 시간 (로 분할 영역 **상태** 로 설정 **실패**) 출력에서 합니다. 
4. 이제 실행는 **Get AzureDataFactoryRun** cmdlet는 조각에 대 한 실행 작업에 대 한 정보를 가져옵니다.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	값 **시작 날짜 시간** 은 이전 단계에서 기록해 놓은 오류/문제 조각에 대 한 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
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

 

## <a name="pighivewalkthrough"></a> 연습: Hive/Pig 처리 오류 해결
이 연습에서는 Hive/Pig 처리 관련 오류를 Azure 미리 보기 포털과 Azure PowerShell을 모두 사용하여 해결하는 단계를 제공합니다.


### 연습: 사용 하 여 Azure 포털 Pig/Hive 처리와 함께 오류 문제를 해결 하려면
이 시나리오에서는 HDInsight 클러스터의 Hive 처리가 실패하여 데이터 집합이 오류 상태입니다.

1. 클릭 하 여 **오류와 함께** 에 **데이터 집합** 타일에서 **데이터 팩터리** 홈페이지로 이동 합니다.

	![데이터 집합 타일 오류 링크와][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. 에 **오류가 있는 데이터 집합** 블레이드를 클릭 하 여는 **테이블** 에 관심이 있습니다.

	![오류 블레이드와 데이터 집합][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. 에 **테이블** 블레이드를 클릭 하십시오.는 **문제 조각** 와 **상태** 로 설정 **실패**.

	![문제 분할 된 테이블][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. 에 **데이터 조각을** 블레이드를 클릭 하 여는 **활동 실행** 실패 한 합니다.

	![실패 한 실행 된 데이터 조각][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. 에 **작업 실행 세부 정보** 블레이드를 HDInsight 처리와 연결 된 파일을 다운로드할 수 있습니다. 클릭 하 여 **다운로드** 에 대 한 **상태/stderr** 오류에 대 한 세부 정보를 포함 하는 오류 로그 파일을 다운로드 합니다.

	![다운로드 링크를 사용 하 여 세부 정보를 실행 하는 활동][image-data-factory-troubleshoot-activity-run-details]

    
### 연습: Pig/Hive 처리와 함께 오류 문제를 해결 하려면 Azure PowerShell 사용 하 여
1.	시작 **Azure PowerShell**. 
2.	전환 **AzureResourceManager** 모드 데이터 팩터리 cmdlet으로는이 모드 에서만 사용할 수 있습니다.

         
		switch-azuremode AzureResourceManager

3. Get-AzureDataFactorySlice 명령을 실행하여 조각과 해당 상태를 표시합니다. 상태와 함께 조각을 표시 되어야 합니다: 실패 했습니다.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]대체 **시작 날짜 시간** 에 지정한 값의 시작 날짜 시간으로는 **집합 AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	참고는 **시작** 문제 조각에 대 한 시간 (로 분할 영역 **상태** 로 설정 **실패**) 출력에서 합니다. 
4. 이제 실행는 **Get AzureDataFactoryRun** cmdlet는 조각에 대 한 실행 작업에 대 한 정보를 가져옵니다.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	값 **시작 날짜 시간** 은 이전 단계에서 기록해 놓은 오류/문제 조각에 대 한 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
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

6. 실행할 수 있습니다 **저장 AzureDataFactoryLog** 위의 출력에서 참조 하 고 로그를 다운로드 하는 Id 값을 사용 하 여 cmdlet를 사용 하 여 파일에서 **-DownloadLogs** cmdlet에 대 한 옵션입니다.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

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

<!---HONumber=GIT-SubDir--> 
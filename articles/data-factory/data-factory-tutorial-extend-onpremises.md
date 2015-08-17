<properties 
	pageTitle="출력 데이터를 자습서에서 온-프레미스 SQL Server 데이터베이스로 복사" 
	description="이 자습서의 연습은 데이터 팩터리 자습서를 확장하여 마케팅 캠페인 효율성 데이터를 온-프레미스 SQL Server 데이터베이스로 복사합니다."
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>


# 연습: 캠페인 효율성 데이터를 온-프레미스 SQL Server 데이터베이스로 복사 
이 연습에서는 파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 환경을 설정하는 방법을 배웁니다.
 
분할 -> 보강 -> 분석 워크플로를 사용한 첫 번째 연습에서 로그 처리 시나리오의 마지막 단계에 마케팅 캠페인 효과 출력이 Azure SQL 데이터베이스에 복사되었습니다. 이 데이터를 조직 내에서 분석하기 위해 온-프레미스 SQL Server로 이동할 수도 있습니다.
 
마케팅 캠페인 효과 데이터를 Azure Blob에서 온-프레미스 SQL Server로 복사하기 위해서는 첫 번째 연습에서 소개한 일련의 cmdlet을 동일하게 사용하여 연결된 서비스, 테이블 및 파이프라인을 추가로 만들어야 합니다.

## 사전 필수 구성 요소

[자습서의 연습을 수행**해야** 합니다. 이 문서에서 연습을 수행하기 전에 데이터 팩터리][datafactorytutorial]를 사용하여 로그 파일을 이동하고 처리합니다.

**(권장)** 온-프레미스 SQL Server에서 Azure Blob 저장소로 데이터를 이동하는 파이프라인을 만드는 연습은 [온-프레미스 데이터를 사용할 수 있도록 설정][useonpremisesdatasources] 문서의 연습을 검토하고 연습합니다.


이 연습에서는 다음 단계를 수행합니다.

1. [1단계: 데이터 관리 게이트웨이를 만듭니다.](#OnPremStep1) 데이터 관리 게이트웨이는 클라우드에서 조직의 온-프레미스 데이터 원본에 액세스할 수 있게 해주는 클라이언트 에이전트입니다. 이 게이트웨이를 사용하여 온-프레미스 SQL Server와 Azure 데이터 저장소 간에 데이터를 전송할 수 있습니다.	

	온-프레미스 SQL Server 데이터베이스를 Azure 데이터 팩터리에 연결된 서비스로 추가하기 전에 회사 환경에 게이트웨이를 2개 이상 설치하고 Azure 데이터 팩터리에 등록해야 합니다.

2. [2단계: 온-프레미스 SQL Server에 대한 연결 서비스를 만듭니다.](#OnPremStep2) 이 단계에서는 먼저 온-프레미스 SQL Server 컴퓨터에서 데이터베이스와 테이블을 만든 다음 연결된서비스 **OnPremSqlLinkedService**를 만듭니다.
3. [3단계: 테이블 및 파이프라인을 만듭니다.](#OnPremStep3) 이 단계에서는 **MarketingCampaignEffectivenessOnPremSQLTable** 테이블과 **EgressDataToOnPremPipeline** 파이프라인을 만듭니다. 

4. [4단계: 파이프라인을 모니터링하고 결과를 봅니다.](#OnPremStep4) 이 단계에서는 Azure 포털을 사용하여 파이프라인, 테이블 및 데이터 조각을 모니터링합니다.


## <a name="OnPremStep1"></a> 1단계: 데이터 관리 게이트웨이를 만듭니다.

데이터 관리 게이트웨이는 클라우드에서 조직의 온-프레미스 데이터 원본에 액세스할 수 있게 해주는 클라이언트 에이전트입니다. 이 게이트웨이를 사용하여 온-프레미스 SQL Server와 Azure 데이터 저장소 간에 데이터를 전송할 수 있습니다.
  
온-프레미스 SQL Server 데이터베이스를 Azure 데이터 팩터리에 연결된 서비스로 추가하기 전에 회사 환경에 게이트웨이를 2개 이상 설치하고 Azure 데이터 팩터리에 등록해야 합니다.

사용할 수 있는 기존 데이터 게이트웨이가 있는 경우 이 단계를 건너뜁니다.

1.	논리 데이터 게이트웨이를 만듭니다. **Azure 미리 보기 포털**에서, 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **연결 서비스**를 클릭합니다.
2.	명령 모음에서 **데이터 게이트웨이 추가(+)**를 클릭합니다.  
3.	**새 데이터 게이트웨이** 블레이드에서 **만들기**를 클릭합니다.
4.	**만들기** 블레이드에서, **MyGateway**를 데이터 게이트웨이 **이름**으로 입력하세요.
5.	**A 지역 선택**을 클릭하고 필요한 경우 변경합니다. 
6.	블레이드 **만들기**에서 **확인**을 클릭합니다. 
7.	**구성** 블레이드가 표시되어야 합니다. 
8.	블레이드 **구성**에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 그러면 게이트웨이가 컴퓨터에 다운로드되어 설치 및 구성되고 서비스에 등록됩니다. 포털의 이 논리 게이트웨이에 연결할 기존 게이트웨이가 컴퓨터에 설치되어 있는 경우 데이터 관리 게이트웨이 구성 관리자(미리 보기) 도구를 사용하여 이 블레이드에서 키를 사용하여 기존 게이트웨이를 다시 등록합니다.

	![데이터 관리 게이트웨이 구성 관리자][image-data-factory-datamanagementgateway-configuration-manager]

9. **확인**을 클릭하여 **구성** 블레이드를 닫고 **확인**을 클릭하여 블레이드 **만들기**를 닫습니다. **연결 서비스** 블레이드에서 **MyGateway**의 상태가 **GOOD**으로 변경될 때까지 기다립니다. **데이터 관리 게이트웨이 구성 관리자(미리 보기)** 도구를 시작하여 게이트웨이 이름이 포털의 이름과 일치하고 **상태**가 **Registered**인지 확인할 수도 있습니다. 연결된 서비스를 닫았다가 다시 열여야 최신 상태를 표시할 수 있습니다. 화면이 최신 상태로 새로 고쳐지려면 몇 분 정도 걸릴 수 있습니다.

## <a name="OnPremStep2"></a> 2단계: 온-프레미스 SQL Server에 대한 연결 서비스를 만듭니다.

이 단계에서는 먼저 온-프레미스 SQL Server 컴퓨터에서 필요한 데이터베이스와 테이블을 만든 다음 연결된 서비스를 만듭니다.

### 온-프레미스 데이터베이스 및 테이블 준비

먼저 SQL Server 데이터베이스, 테이블, 사용자 정의 형식 및 저장 프로시저를 만들어야 합니다. **MarketingCampaignEffectiveness** 결과를 Azure Blob에서 SQL Server 데이터베이스로 이동하는 데 사용됩니다.

1.	**Windows 탐색기**에서 **C:\\ADFWalkthrough**(또는 샘플의 압축을 푼 위치)의 **OnPremises** 하위 폴더로 이동합니다.
2.	즐겨 사용하는 편집기에서 **prepareOnPremDatabase&Table.ps1**을 열고, 강조 표시된 내용을 SQL Server 정보로 바꾼 다음 파일을 저장합니다. **SQL 인증** 세부 정보를 제공하세요. 이 자습서에서는 데이터베이스에 SQL 인증을 사용하도록 설정합니다. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. **Azure PowerShell**에서 **C:\\ADFWalkthrough\\OnPremises** 폴더로 이동합니다.
4.	**prepareOnPremDatabase&Table.ps1**을 실행합니다.**(큰따옴표의 & 또는 아래와 같이).**
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. 스크립트가 성공적으로 실행되면 다음이 표시됩니다.
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### 연결된 서비스 만들기

1.	**Azure 미리 보기 포털**에서 **LogProcessingFactory**의 **데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭합니다.
2.	**데이터 팩터리 편집기**의 도구 모음에서 **새로운 데이터 저장소**를 클릭하고 **온-프레미스 SQL Server 데이터베이스**를 선택합니다.
3.	JSON 스크립트에서 다음을 수행합니다. 
	1.	**<servername>**를 SQL Server 데이터베이스를 호스팅하는 서버의 이름으로 대체합니다.
	2.	**<databasename>**를 **MarketingCampaigns**로 대체합니다.
	3.	**SQL 인증**을 사용 중인 경우
		1.	**connectionString**에 **<username>** 및 **<password>**를 지정합니다.
		2.	마지막 두 행을 제거합니다(Windows 인증을 사용하는 경우에 **username** 및 **password** JSON 속성만 필요). 
		3.	**gatewayName** 행의 끝에서 **, (쉼표) **를 제거합니다.

		**Windows 인증을 사용하는 경우:** 1입니다. **통합 보안**의 값을 **connectionString**에서 **True**로 설정합니다. connectionString에서 "**User ID=<username>;Password=<password>;**"를 제거합니다. 2. **username** 속성에 대한 데이터베이스에 액세스할 수 있는 사용자의 이름을 지정합니다. 3. 사용자 계정으로 **password**를 지정합니다.   
	4. gatewayName 속성으로 게이트웨이의 이름(**MyGateway**)을 지정합니다. 		  	 
3.	도구 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다. 

## <a name="OnPremStep3"></a> 3단계: 테이블 및 파이프라인을 만듭니다.

### 온-프레미스 논리 테이블 만들기

1.	**데이터 팩터리 편집기**의 도구 모음에서 **새로운 데이터 저장소**를 클릭하고 **온-프레미스 SQL**을 선택합니다. 
2. **C:\\ADFWalkthrough\\OnPremises** 폴더의 **MarketingCampaignEffectivenessOnPremSQLTable.json** 파일에서 오른쪽 창의 JSON을 JSON 스크립트로 대체합니다.
3. 연결 서비스의 이름(**linkedServiceName** 속성)을 **OnPremSqlServerLinkedService**에서 **SqlServerLinkedService**로 변경합니다.
4. 도구 모음에서 **배포**를 클릭하여 테이블을 배포합니다. 
	 
#### Azure Blob에서 SQL Server로 데이터를 복사하는 파이프라인 만들기

1.	1. **데이터 팩터리 편집기**의 도구 모음에서 **새 파이프라인** 단추를 클릭합니다. 단추가 표시되지 않는 경우 도구 모음에서 **... (줄임표)**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.
2. **C:\\ADFWalkthrough\\OnPremises** 폴더의 **EgressDataToOnPremPipeline.json** 파일에서 오른쪽 창의 JSON을 JSON 스크립트로 대체합니다.
3. JSON의 **닫는 대괄호 (']')** 끝에 **쉼표 (',')**를 추가한 다음 닫는 대괄호 후 다음 세 줄을 추가합니다. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	이 연습에서 예제 데이터는 2014년 5월 1일부터 2014년 5월 5일까지이기 때문에 **시작** 시간과 **종료** 시간이 2014년 5월 1일부터 2014년 5월 5일까지 설정됩니다.
 
3. 도구 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다. 편집기의 제목 표시줄에 **파이프라인이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.
	
## <a name="OnPremStep4"></a> 4단계: 파이프라인을 모니터링하고 결과를 봅니다.

이제 [기본 자습서][datafactorytutorial]의 **파이프라인 및 데이터 조각을 모니터링** 섹션에 소개된 동일한 단계를 사용하여 새 온-프레미스 ADF 테이블에 대한 새 파이프라인 및 데이터 조각을 모니터링할 수 있습니다.
 
**MarketingCampaignEffectivenessOnPremSQLTable** 테이블의 조각 상태가 Ready로 변경되면 파이프라인이 조각에 대한 실행을 완료했음을 의미합니다. 결과를 보려면 SQL Server의 **MarketingCampaigns** 데이터베이스에서 **MarketingCampaignEffectiveness** 테이블을 쿼리합니다.
 
축하합니다. 온-프레미스 데이터 원본을 사용하는 연습을 성공적으로 마쳤습니다.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=August15_HO6-->
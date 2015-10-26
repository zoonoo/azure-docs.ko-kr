<properties 
	pageTitle="온-프레미스 SQL Server 데이터베이스에 출력 데이터 복사(Azure PowerShell)" 
	description="이 연습에서는 파이프라인이 출력 데이터를 SQL Server 데이터베이스에 복사하도록 Azure PowerShell을 사용하는 자습서를 확장합니다."
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
	ms.date="08/25/2015" 
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

	온-프레미스 SQL Server 데이터베이스를 Azure Data Factory에 연결된 서비스로 추가하기 전에 회사 환경에 게이트웨이를 2개 이상 설치하고 Azure Data Factory에 등록해야 합니다.

2. [2단계: 온-프레미스 SQL Server에 대한 연결 서비스를 만듭니다.](#OnPremStep2) 이 단계에서는 먼저 온-프레미스 SQL Server 컴퓨터에서 데이터베이스와 테이블을 만든 다음 연결된서비스 **OnPremSqlLinkedService**를 만듭니다.
3. [3단계: 테이블 및 파이프라인을 만듭니다.](#OnPremStep3) 이 단계에서는 **MarketingCampaignEffectivenessOnPremSQLTable** 테이블과 **EgressDataToOnPremPipeline** 파이프라인을 만듭니다. 

4. [4단계: 파이프라인을 모니터링하고 결과를 봅니다.](#OnPremStep4) 이 단계에서는 Azure 포털을 사용하여 파이프라인, 테이블 및 데이터 조각을 모니터링합니다.


## <a name="OnPremStep1"></a> 1단계: 데이터 관리 게이트웨이를 만듭니다.

데이터 관리 게이트웨이는 클라우드에서 조직의 온-프레미스 데이터 원본에 액세스할 수 있게 해주는 클라이언트 에이전트입니다. 이 게이트웨이를 사용하여 온-프레미스 SQL Server와 Azure 데이터 저장소 간에 데이터를 전송할 수 있습니다.
  
온-프레미스 SQL Server 데이터베이스를 Azure Data Factory에 연결된 서비스로 추가하기 전에 회사 환경에 게이트웨이를 2개 이상 설치하고 Azure Data Factory에 등록해야 합니다.

사용할 수 있는 기존 데이터 게이트웨이가 있는 경우 이 단계를 건너뜁니다.

1.	논리 데이터 게이트웨이를 만듭니다. **Azure Preview 포털**의 **데이터 팩터리** 블레이드에서 **연결된 서비스**를 클릭합니다.
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

1.	**Azure Preview 포털**에서 **LogProcessingFactory**의 **데이터 팩터리** 블레이드에 있는 **연결된 서비스** 타일을 클릭합니다.
2.	**연결된 서비스** 블레이드에서 **데이터 저장소 추가(+)**를 클릭합니다.
3.	**새 데이터 저장소** 블레이드에서 **OnPremSqlLinkedService**를 **이름**으로 입력합니다. 
4.	**유형(필요한 설정)**을 클릭하고 **SQL Server**를 선택합니다. 이제 **새 데이터 저장소** 블레이드에 **데이터 게이트웨이**, **서버**, **데이터베이스** 및 **자격 증명** 설정이 표시됩니다. 
5.	**데이터 게이트웨이(필요한 설정 구성)**를 클릭하고 앞에서 만든 **MyGateway**를 선택합니다. 
6.	**MarketingCampaigns** 데이터베이스를 호스트하는 데이터베이스 서버의 **이름**을 입력합니다. 
7.	**MarketingCampaigns**를 데이터베이스로 입력합니다. 
8.	**자격 증명**을 클릭합니다. 
9.	**자격 증명** 블레이드에서 **Click here to set Credentials securely(자격 증명을 안전하게 설정하려면 여기를 클릭하세요.)**를 클릭합니다.
10.	One-Click 응용 프로그램이 처음으로 설치되고 **Setting Credentials **대화 상자가 시작됩니다.
11.	**자격 증명 설정** 대화 상자에서 **사용자 이름** 및 **암호**를 입력하고 **확인**을 클릭합니다. 대화 상자가 닫힐 때까지 기다립니다. 
12.	**새 데이터 저장소** 블레이드에서 **확인**을 클릭합니다. 
13.	**연결된 서비스** 블레이드에서 **OnPremSqlLinkedService**가 목록에 표시되고 연결된 서비스의 **상태**가 **Good**인지 확인합니다.

## <a name="OnPremStep3"></a> 3단계: 테이블 및 파이프라인을 만듭니다.

### 온-프레미스 논리 테이블 만들기

1.	**Azure PowerShell**에서 **C:\\ADFWalkthrough\\OnPremises** 폴더로 전환합니다. 
2.	다음과 같이 **New-AzureDataFactoryDataset** cmdlet을 사용하여 **MarketingCampaignEffectivenessOnPremSQLTable.json**에 대한 테이블을 만듭니다.

			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Azure Blob에서 SQL Server로 데이터를 복사하는 파이프라인 만들기

1.	다음과 같이 **New-AzureDataFactoryPipeline** cmdlet을 사용하여 **EgressDataToOnPremPipeline.json**에 대한 파이프라인을 만듭니다.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. **Set-AzureDataFactoryPipelineActivePeriod** cmdlet을 사용하여 **EgressDataToOnPremPipeline**의 활성 기간을 지정합니다.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	**‘Y’**를 눌러 계속합니다.
	
## <a name="OnPremStep4"></a> 4단계: 파이프라인을 모니터링하고 결과를 봅니다.

이제 [6단계: 테이블 및 파이프라인 모니터링](#MainStep6)에 소개된 것과 동일한 단계를 사용하여 새로운 온-프레미스 ADF 테이블에 대한 새 파이프라인과 데이터 조각을 모니터링할 수 있습니다.
 
**MarketingCampaignEffectivenessOnPremSQLTable** 테이블의 조각 상태가 Ready로 변경되면 파이프라인이 조각에 대한 실행을 완료했음을 의미합니다. 결과를 보려면 SQL Server의 **MarketingCampaigns** 데이터베이스에서 **MarketingCampaignEffectiveness** 테이블을 쿼리합니다.
 
축하합니다. 온-프레미스 데이터 원본을 사용하는 연습을 성공적으로 마쳤습니다.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=Oct15_HO3-->
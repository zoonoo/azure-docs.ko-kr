<properties 
	pageTitle="Azure 데이터 팩터리를 사용하여 로그 파일 이동 및 처리" 
	description="이 고급 자습서는 거의 실제 업무 시나리오에 설명 하 고 Azure 데이터 팩터리 서비스 및 데이터 팩터리 편집기를 사용 하는 시나리오를 구현 합니다." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 자습서: 마케팅 캠페인의 효과 측정합니다.  
Contoso는 여러 플랫폼에 대 한 게임을 만드는 게임 회사: 게임 콘솔, 핸드헬드의 장치 및 개인용 컴퓨터 (Pc). 이러한 게임 로그 많이 생성 및 Contoso의 목표는 수집 하 고 고객 환경 설정, 인구 통계, 상향 판매 및 교차 판매 기회를 식별 하 고, 비즈니스를 성장 시키십시오에 강력한 새 기능을 개발 하 고, 고객에 게 더 나은 환경을 제공 하는 등 사용 현황 동작에 대 한 통찰력을 얻으려고 이러한 로그를 분석 합니다.

이 자습서에서는 Contoso가 샘플 로그를 수집, 처리 및 참조 데이터를 보강 하 및 데이터를 변환 하 여 최근에 출시 하는 마케팅 캠페인의 효율성을 평가 하기 위해 데이터 팩터리 파이프라인을 만듭니다. 여기에 다음 세 파이프라인이 있습니다.

1.	 **PartitionGameLogsPipeline** blob 저장소에서 원시 게임 이벤트를 읽고 연도, 월 및 일을 기반으로 파티션을 만듭니다.
2.	 **EnrichGameLogsPipeline** 지역 코드 참조 데이터를 사용 하 여 분할 된 게임 이벤트를 조인 하 고 해당 지리적 위치를 IP 주소에 매핑하여 데이터를 강화 합니다.
3.	 **AnalyzeMarketingCampaignPipeline** 파이프라인 풍부해 진된 데이터를 활용 하 고 마케팅 캠페인 효과 포함 하는 최종 출력을 만들려면 광고 데이터를 사용 하 여 처리 합니다.

## 자습서 수행을 위한 준비
1.	읽기 [Azure 데이터 팩터리 소개][adfintroduction] Azure 데이터 팩터리의 개요 및 최상위 개념의 이해를 가져올 수 있습니다.
2.	이 자습서를 수행하려면 Azure 구독이 있어야 합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션](http://azure.microsoft.com/pricing/purchase-options/), [구성원 제공 항목](http://azure.microsoft.com/pricing/member-offers/) 또는 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하십시오.
3.	다운로드 하 여 설치 해야 [Azure PowerShell][download-azure-powershell] 컴퓨터에 있습니다. 예제 데이터를 업로드 및 pig/hive 스크립트도 blob 저장소에 데이터 팩터리 cmdlet를 실행 합니다. 
2.	**(권장)** 검토에 대 한 자습서를 연습 하 고는 [Azure 데이터 팩터리 시작][adfgetstarted] 포털 및 cmdlet을 익힐 수에 대 한 간단한 자습서에 대 한 문서.
3.	**(권장)** 검토의 연습을 연습 하 고는 [사용 하 여 Pig 및 Hive Azure 데이터 팩터리로][usepigandhive] 를 Azure blob 저장소에 온-프레미스 데이터 원본에서 데이터를 이동 하는 파이프라인을 만드는 방법에 대 한 연습에 대 한 문서.
4.	다운로드 [ADFWalkthrough][adfwalkthrough-download] 파일을 **C:\ADFWalkthrough** 폴더 **폴더 구조를 유지**:
	- **파이프라인:** 파이프라인의 정의 포함 하는 JSON 파일 포함 됩니다.
	- **테이블:** 테이블의 정의 포함 하는 JSON 파일 포함 됩니다.
	- **LinkedServices:** (HDInsight) 계산 및 저장소의 정의 포함 하는 JSON 파일 포함 클러스터 
	- **스크립트:** 데이터 처리를 위한 사용 및 파이프라인에서 호출 되는 Hive 및 Pig 스크립트를 포함 하는 것
	- **SampleData:** 이 연습에 대 한 샘플 데이터를 포함
	- **온 프레미스:** 온-프레미스 데이터에 액세스를 보여주는데 사용 되는 JSON 파일 및 스크립트를 포함
	- **uploadSampleDataAndScripts.ps1:** 이 스크립트는 샘플 데이터 및 스크립트 Azure에 업로드 합니다.
5. 다음과 같은 Azure 리소스를 만들었는지 확인합니다.			
	- Azure 저장소 계정
	- Azure SQL 데이터베이스
	- Azure HDInsight 클러스터 버전 3.1 이상 (또는 데이터 팩터리 서비스를 자동으로 만듭니다 하는 요청 시 사용 하 여 HDInsight 클러스터)	
7. Azure 리소스를 만들었으면 이러한 각 리소스에 연결하는 데 필요한 정보를 알고 있는지 확인합니다.
 	- **Azure 저장소 계정** -계정 이름 및 계정 키입니다.  
	- **Azure SQL 데이터베이스** -서버, 데이터베이스, 사용자 이름 및 암호입니다.
	- **Azure HDInsight 클러스터**.-HDInsight 클러스터의 이름, 사용자 이름, 암호 및 계정 이름 및이 클러스터와 연결 된 Azure 저장소에 대 한 계정 키입니다. HDInsight 클러스터 사용자 고유의 대신 주문형 HDInsight 클러스터를 사용 하려는 경우이 단계를 건너뛸 수 있습니다.  
8. 시작 **Azure PowerShell** 하 고 다음 명령을 실행 합니다. Azure PowerShell을 열어둡니다. 닫고 다시 열 경우 이러한 명령을 다시 실행 해야 합니다.
	- 실행 **Add-azureaccount** 사용자 이름 및 Azure 미리 보기 포털에 로그인 하는데 사용할 암호를 입력 합니다.  
	- 실행 **Get-azuresubscription** 이 계정에 대 한 모든 구독을 볼 수 있습니다.
	- 실행 **Select-azuresubscription** 를 사용 하려면 구독을 선택 합니다. 이 구독은 Azure 미리 보기 포털에서 사용한 것과 같아야 합니다.	

## 개요
종단 간 워크플로는 아래와 같습니다.

![자습서 종단간 흐름][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** blob 저장소 (RawGameEventsTable)에서 원시 게임 이벤트를 읽고 연도, 월 및 일 (PartitionedGameEventsTable)를 기반으로 파티션을 만듭니다.
2.  **EnrichGameLogsPipeline** 지역 코드 (RefGetoCodeDictionaryTable)와 분할 된 게임 이벤트 (PartitionedGameEvents 테이블은 PartitionGameLogsPipeline의 출력)를 조인 및 해당 지리적 위치 (EnrichedGameEventsTable)를 IP 주소에 매핑하여 데이터를 강화 합니다.
3.  **AnalyzeMarketingCampaignPipeline** 파이프라인 풍부해 진된 데이터 (EnrichedGameEventTable는 EnrichGameLogsPipeline에서 생성)를 활용 하 고 Azure SQL 데이터베이스 (MarketingCampainEffectivensessSQLTable) 및 분석에 대 한 Azure blob 저장소 (MarketingCampaignEffectivenessBlobTable)에 복사 되는 광고 데이터 마케팅 캠페인 효과의 최종 출력을 만들려면 (RefMarketingCampaignnTable)를 사용 하 여 처리 합니다.
    
## 연습: 만들기, 배포 및 워크플로 모니터링 합니다.
1. [1 단계: 예제 데이터 및 스크립트를 업로드](#MainStep1). 이 단계에서는 모든 샘플 데이터(모든 로그 및 참조 데이터 포함)와 워크플로에서 실행할 Hive/Pig 스크립트를 업로드합니다. 실행하는 스크립트에서는 MarketingCampaigns라는 Azure SQL 데이터베이스, 테이블, 사용자 정의 형식 및 저장 프로시저도 만듭니다.
2. [2 단계: 프로그램 Azure 데이터 팩터리 만들기](#MainStep2). 이 단계에서는 LogProcessingFactory라는 Azure 데이터 팩터리를 만듭니다.
3. [3 단계: 연결 된 서비스를 만드는](#MainStep3). 이 단계에서는 연결된 서비스 
	
	- 	**StorageLinkedService**. 원시 게임 이벤트, 분할된 게임 이벤트, 보강된 게임 이벤트, 마케팅 캠페인 효과 정보, 참조 지역 코드 데이터 및 참조 마케팅 캠페인 데이터를 포함하는 Azure 저장소 위치를 LogProcessingFactory에 연결합니다.   
	- 	**AzureSqlLinkedService**. 마케팅 캠페인 효과 정보를 포함하는 Azure SQL 데이터베이스를 연결합니다. 
	- 	**HDInsightStorageLinkedService**. HDInsightLinkedService가 참조하는 HDInsight 클러스터와 관련된 Azure Blob 스토리지를 연결합니다. 
	- 	**HDInsightLinkedService**. Azure HDInsight 클러스터를 LogProcessingFactory에 연결합니다. 이 클러스터는 데이터에 대한 pig/hive 처리를 수행하는 데 사용됩니다. 
 		
4. [4 단계: 테이블 만들기](#MainStep4). 이 단계에서는 다음 테이블을 만듭니다.
	
	- **RawGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 원시 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 분할된 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 참조 지역 코드 데이터가 있는 위치를 지정합니다(adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 참조 마케팅 캠페인 데이터가 있는 위치를 지정합니다(adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 강화된 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. 이 표에서 AzureSqlLinkedService 마케팅 캠페인 효율성 데이터를 포함 하 여 정의 된 Azure SQL 데이터베이스의 SQL 테이블 (MarketingCampaignEffectiveness)을 지정 합니다. 
	- **MarketingCampaignEffectivenessBlobTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 마케팅 캠페인 효과 데이터가 있는 위치를 지정합니다(adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [5 단계: 만들기 및 예약 파이프라인](#MainStep5). 이 단계에서는 다음 파이프라인을 만듭니다.
	- **PartitionGameLogsPipeline**. 이 파이프라인은 Blob 저장소에서 원시 게임 이벤트(RawGameEventsTable)를 읽고 연도, 월 및 일을 기준으로 파티션(PartitionedGameEventsTable)을 만듭니다. 


		![PartitionGamesLogs 파이프라인][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. 이 파이프라인은 분할된 게임 이벤트(PartitionedGameEvents 테이블, PartitionGameLogsPipeline의 출력)를 지역 코드(RefGetoCodeDictionaryTable)와 조인하고 IP 주소를 해당하는 지리적 위치(EnrichedGameEventsTable)에 매핑하여 데이터를 보강합니다.

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. 이 파이프라인은 보강된 게임 이벤트 데이터(EnrichGameLogsPipeline에서 생성한 EnrichedGameEventTable)를 활용하고 이 데이터를 광고 데이터(RefMarketingCampaignnTable)와 함께 처리하여 마케팅 캠페인 효과에 대한 최종 출력을 만듭니다. 이 출력은 Azure SQL 데이터베이스(MarketingCampainEffectivensessSQLTable) 및 Azure Blob 저장소(MarketingCampaignEffectivenessBlobTable)로 복사되어 분석됩니다.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [6 단계: 모니터링 파이프라인 및 데이터 조각을](#MainStep6). 이 단계에서는 Azure 포털을 사용하여 파이프라인, 테이블 및 데이터 조각을 모니터링합니다.

## <a name="MainStep1"></a> 1 단계: 예제 데이터 및 스크립트를 업로드 합니다.
이 단계에서는 모든 샘플 데이터(로그 및 참조 데이터 모두 포함)와 워크플로에서 호출되는 Hive/Pig 스크립트를 업로드합니다. 또한 실행 하면 스크립트 라는 Azure SQL 데이터베이스를 만들 **MarketingCampaigns**, 테이블, 사용자 정의 형식 및 저장 프로시저입니다.

테이블, 사용자 정의 형식 및 저장 프로시저는 Azure Blob 저장소에서 Azure SQL 데이터베이스로 마케팅 캠페인 효과 결과를 이동할 때 사용됩니다.

1. 열기 **uploadSampleDataAndScripts.ps1** 에서 **C:\ADFWalkthrough** 폴더 (또는 압축 푼된 파일에 포함 된 폴더) 선호 하는 편집기에서 귀하의 클러스터 정보를 강조 표시 된 대체 하 고 파일을 저장 합니다.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][다운로드][sqlcmd-install]   
2. 로컬 컴퓨터가 Azure SQL 데이터베이스에 액세스할 수 있는지 확인합니다. 사용 하 여 액세스를 활성화 하려면는 **Azure 관리 포털** 또는 **sp_set_firewall_rule** 컴퓨터의 IP 주소에 대 한 방화벽 규칙을 만들려면 master 데이터베이스에 있습니다. 이 변경 내용이 적용되려면 최대 5분까지 걸릴 수 있습니다. 참조 [SQL Azure에 대 한 방화벽 규칙을 설정][azure-sql-firewall].
4. Azure PowerShell의 위치로 이동 하 여 예제를 추출한 (예: **C:\ADFWalkthrough**)
5. 실행 **uploadSampleDataAndScripts.ps1** 
6. 스크립트가 성공적으로 실행되면 다음이 표시됩니다.

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> 2 단계: 프로그램 Azure 데이터 팩터리 만들기
이 단계에서는 라는 한 Azure 데이터 팩터리를 만듭니다 **LogProcessingFactory**.

1.	로그인 한 후는 [Azure 미리 보기 포털][azure-preview-portal], 클릭 **새로 만들기** 왼쪽 아래 모퉁이에서 클릭 하 여 **데이터 분석** 에 **만들기** 블레이드를 클릭 하 고 **데이터 팩터리** 에 **데이터 분석** 블레이드. 

	![새로운 사용할->][image-data-factory-new-datafactory-menu]

5. 에 **새 데이터 팩터리** 블레이드, 입력 **LogProcessingFactory** 에 대 한는 **이름**.

	![데이터 팩터리 블레이드][image-data-factory-tutorial-new-datafactory-blade]

6. 명명 된는 Azure 리소스 그룹을 만들지 않은 경우 **ADF** 이미, 다음을 수행 합니다.
	1. 클릭 하 여 **리소스 그룹 이름**, 를 클릭 하 고 **새 리소스 그룹을 만들**.
	
		![리소스 그룹 블레이드][image-data-factory-tutorial-resourcegroup-blade]
	2. 에 **리소스 그룹 만들기** 블레이드, 입력 **ADF** 를클릭하고 리소스 그룹의 이름에 대 한 **확인**.
	
		![리소스 그룹 만들기][image-data-factory-tutorial-create-resourcegroup]
7. 선택 **ADF** 에 대 한는 **리소스 그룹 이름**.  
8.	에 **새 데이터 팩터리** 블레이드에서 있음을 **시작 보드에 추가** 기본적으로 선택 됩니다. 선택된 경우 데이터 팩터리에 대한 링크가 시작 보드(Azure 미리 보기 포털에 로그인할 때 표시되는 페이지)에 추가됩니다.

	![데이터 팩터리 블레이드 만들기][image-data-factory-tutorial-create-datafactory]

9.	에 **새 데이터 팩터리** 블레이드를 클릭 하 여 **만들기** 데이터 팩터리를 만드는 합니다.
10.	데이터 팩터리를 만든 후 나타나야는 **데이터 팩터리** 블레이드 라는 **LogProcessingFactory**.

	![데이터 팩터리 홈페이지][image-data-factory-tutorial-datafactory-homepage]

	
	이 블레이드가 표시되지 않는 경우 다음 중 하나를 수행합니다.

	- 클릭 하 여 **LogProcessingFactory** 에 **시작 보드** (홈 페이지)
	- 클릭 하 여 **찾아보기** 는 왼쪽에서 **모든**, 클릭 **데이터 팩터리**, 데이터 팩터리를 클릭 합니다.
 
	> [AZURE.NOTE]Azure 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 오류를 수신 하는 경우: **데이터 팩터리 이름을 "LogProcessingFactory" 사용할 수 없으면**, 이름 (예: yournameLogProcessingFactory)을 변경 합니다. 이 자습서의 단계를 수행 하는 동안 LogProcessingFactory 대신이 이름을 사용 합니다.
 
## <a name="MainStep3"></a> 3 단계: 연결 된 서비스 만들기

> [AZURE.NOTE]이 문서 Azure 포털, 특히 데이터 팩터리 편집기를 사용 하 여 연결 된 서비스, 테이블 및 파이프라인을 만듭니다. 참조 [Azure PowerShell을 사용 하 여 자습서][adftutorial-using-powershell] Azure PowerShell을 사용 하 여이 자습서를 수행 하려는 경우.

이 단계에서는 연결된 서비스

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### StorageLinkedService 및 HDInsightStorageLinkedService 만들기

1.	에 **데이터 팩터리** 블레이드를 클릭 하 여 **작성자 및 배포** 타일을 시작는 **편집기** 데이터 팩터리에 대 한 합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

	> [AZURE.NOTE]참조 [데이터 팩터리 편집기][data-factory-editor] 데이터 팩터리 편집기의 자세한 개요에 대 한 항목입니다.

2.  에 **편집기**, 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **Azure 저장소** 의 드롭다운 메뉴에서에서. 오른쪽 창에는 연결 된 Azure 저장소 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다.
	
	![새 데이터 저장소 단추 편집기][image-editor-newdatastore-button]

3. 대체 **accountname** 및 **accountkey** 계정 이름 및 Azure 저장소 계정에 대 한 계정 키 값입니다.

	![JSON 편집기 Blob 저장소][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.

4. 클릭 하 여 **배포** 는 StorageLinkedService를 배포 하려면 도구 모음입니다. 메시지가 표시 되는지 확인 **연결 된 서비스 만든 성공적으로** 제목 표시줄에 있습니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

5. 다른 Azure 저장소를 만드는 단계 라는 서비스를 연결 하는 반복: **HDInsightStorageLinkedService** HDInsight 클러스터와 연결 된 저장소에 대 한 합니다. 연결 된 서비스에 대 한 JSON 스크립트에서의 값을 변경는 **이름** 속성을 **HDInsightStorageLinkedService**.

### AzureSqlLinkedService 만들기
1. 에 **데이터 팩터리 편집기** , 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **Azure SQL 데이터베이스** 의 드롭다운 메뉴에서에서. 오른쪽 창에서 Azure SQL 연결 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다.
2. 대체 **servername**, **username@servername**, 및 **암호** SQL Azure 서버, 사용자 계정 및 암호의 이름으로. 3. 대체 **databasename** 와 **MarketingCampaigns**. 1 단계에서에서 실행 되는 스크립트로 만든 Azure SQL 데이터베이스입니다. 이 데이터베이스 실제로 만들어졌는지 스크립트를 통해 (경우에 오류가 발생 했습니다)를 확인 해야 합니다. 
3. 클릭 하 여 **배포** 만들기 및 배포는 AzureSqlLinkedService 도구 모음에 있습니다.

### HDInsightLinkedService 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다.

#### 주문형 HDInsight 클러스터를 사용하려면
1. 클릭 하 여 **새 계산** 를선택하고 명령 모음에서 **주문형 HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterSize** 속성을 HDInsight 클러스터의 크기를 지정 합니다.
	2. 에 대 한는 **jobsContainer** 속성, 클러스터 로그를 저장할 기본 컨테이너의 이름을 지정 합니다. 이 자습서에서는 지정 **adfjobscontainer**.
	3. 에 대 한는 **timeToLive** 속성을 얼마나 오래 고객 유휴 상태일 수 있는 삭제 하기 전에 지정 합니다. 
	4. 에 대 한는 **버전** 속성을 사용 하려는 HDInsight 버전을 지정 합니다. 이 속성을 제외 하는 경우에 최신 버전이 사용 됩니다.  
	5. 에 대 한는 **linkedServiceName**, 지정 **HDInsightStorageLinkedService** 가져오기에서 만들었을 자습서를 시작 합니다. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		이때는 **유형** 로 설정 된 연결 된 서비스의 **HDInsightOnDemandLinkedService**.

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.
   
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 클릭 하 여 **새 계산** 를선택하고 명령 모음에서 **HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterUri** 속성을 프로그램 HDInsight에 대 한 URL을 입력 합니다. 예: https://<clustername>.azurehdinsight.net/     
	2. 에 대 한는 **UserName** 속성을 HDInsight 클러스터에 대 한 액세스 권한이 있는 사용자 이름을 입력 합니다.
	3. 에 대 한는 **암호** 속성을 사용자에 대 한 암호를 입력 합니다. 
	4. 에 대 한는 **LinkedServiceName** 속성, 입력 **StorageLinkedService**. 시작된 자습서 다운로드에에서 만들 때 연결 된 서비스입니다. 

	Nore 하는 **유형** 로 설정 된 연결 된 서비스의 **HDInsightBYOCLinkedService** (BYOC의 자체 클러스터 가져오기에 대 한 약어).

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.


## <a name="MainStep4"></a> 4 단계: 테이블 만들기
 
이 단계에서는 다음 데이터 팩터리 테이블을 만듭니다.

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![자습서-종단간 흐름][image-data-factory-tutorial-end-to-end-flow]
 
위 그림에서는 가운데 행에는 파이프라인을, 위쪽과 아래쪽 행에는 테이블을 표시합니다.

### 테이블을 만들려면
	
1. 에 **편집기** 데이터 팩터리를 클릭 합니다. **새 데이터 집합** 클릭 하 여 도구 모음에서 단추 **Azure Blob 저장소** 의 드롭다운 메뉴에서에서. 
2. 오른쪽 창에서 JSON 스크립트에서 JSON 바꿉니다는 **RawGameEventsTable.json** 에서 파일의 **C:\ADFWalkthrough\Tables** 폴더입니다.
3. 클릭 하 여 **배포** 만들어 테이블을 배포 하는 도구 모음의 합니다. 표시 되는지 확인은 **테이블 만든 성공적으로** 편집기의 제목 표시줄에는 메시지입니다.
4. 다음 파일 내용 사용 하 여 1-3 단계를 반복 합니다. 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. 다음 파일 내용 사용 하 여 1-3 단계를 반복 합니다. 하지만 선택 **Azure Sql** 클릭 한 후 **새 데이터 집합**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> 5 단계: 만들기 및 파이프라인을 예약
이 단계에서는 다음 파이프라인을 만듭니다.

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### 파이프라인을 만들 수

1. **데이터 팩터리 편집기**의 도구 모음에서 **새 파이프라인** 단추를 클릭합니다. 단추가 표시되지 않는 경우 도구 모음에서 **... (줄임표)**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.
2. 오른쪽 창에서 JSON 스크립트에서 JSON 바꿉니다는 **PartitionGameLogsPipeline.json** 에서 파일의 **C:\ADFWalkthrough\Pipelines** 폴더입니다.
3. JSON의 **닫는 대괄호 (']')** 끝에 **쉼표 (',')**를 추가한 다음 닫는 대괄호 후 다음 세 줄을 추가합니다. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]이 연습에서 예제 데이터는 2014년 5월 1일부터 2014년 5월 5일까지이기 때문에 시작 시간과 종료 시간이 2014년 5월 1일부터 2014년 5월 5일까지 설정됩니다.
 
3. 도구 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다. 편집기의 제목 표시줄에 **파이프라인이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.
4. 다음 파일 내용 사용 하 여 1-3 단계를 반복 합니다. 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. 키를 눌러 데이터 팩터리 블레이드를 닫고 **X** 홈페이지를 볼 수 (오른쪽 위 모퉁이) (* * 데이터 팩터리 **블레이드) 팩터리를 데이터에 대 한. 
### 다이어그램 보기

1. 에 **데이터 팩터리** 에 대 한 블레이드는 **LogProcessingFactory**, 클릭 **다이어그램**. 

	![다이어그램 링크][image-data-factory-tutorial-diagram-link]

2. 다이어그램을 다시 정렬할 수 있으며, 다음은 맨 위에 직접 입력, 맨 아래에 출력이 표시되도록 다시 정렬한 다이어그램입니다. 볼 수 있습니다의 출력의 **PartitionGameLogsPipeline** EnrichGameLogsPipeline 및의 출력에 대 한 입력으로 전달 되어는 **EnrichGameLogsPipeline** 에 전달 되는 **AnalyzeMarketingCampaignPipeline**. 제목을 두 번 클릭하면 블레이드가 나타내는 아티팩트에 대한 자세한 내용이 표시됩니다.

	![다이어그램 보기][image-data-factory-tutorial-diagram-view]

3. 마우스 오른쪽 단추로 클릭 **AnalyzeMarketingCampaignPipeline**, 를 클릭 하 고 **개방 된 파이프라인이**. 활동에 대 한 입력 및 출력 데이터 집합을 함께 파이프라인에서 모든 활동에 표시 되어야 합니다.
 
	![개방 된 파이프라인이](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	클릭 하 여 **데이터 팩터리** 모든 파이프라인을 사용 하 여 다이어그램 뷰 돌아가려면 왼쪽 위 모서리의 이동 경로에 있습니다.


**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 파이프라인, 테이블을 만들었고 워크플로를 시작했습니다.


## <a name="MainStep6"></a> 6 단계: 모니터링 파이프라인 및 데이터 조각 

1.	하지 않은 경우는 **데이터 팩터리** 에 대 한 블레이드는 **LogProcessingFactory** 열기를 할 수 있는 다음 중 하나:
	1.	클릭 하 여 **LogProcessingFactory** 에 **시작 보드**. 데이터 팩터리를 만드는 동안는 **시작 보드에 추가** 옵션이 자동으로 선택 되었습니다.

		![시작 보드를 모니터링합니다.][image-data-factory-monitoring-startboard]

	2. 클릭 하 여 **찾아보기** 허브, 를클릭하고 **모든**.
	 	
		![모든 허브 모니터링][image-data-factory-monitoring-hub-everything]

		에 **찾아보기** 블레이드를 **데이터 팩터리** 선택 하 고 **LogProcessingFactory** 에 **데이터 팩터리** 블레이드입니다.

		![찾아보기 Datafactories 모니터링][image-data-factory-monitoring-browse-datafactories]
2. 여러 가지 방법으로 데이터 팩터리를 모니터링할 수 있습니다. 파이프라인이나 데이터 집합으로 시작할 수 있습니다. 우선 파이프라인부터 자세히 다루겠습니다. 
3.	클릭 하 여 **파이프라인** 에 **데이터 팩터리** 블레이드입니다. 
4.	클릭 하 여 **PartitionGameLogsPipeline** 파이프라인 블레이드에서 합니다. 
5.	에 **파이프라인** 에 대 한 블레이드 **PartitionGameLogsPipeline**, 파이프라인에서 사용 하는 것이 표시 **RawGameEventsTable** 데이터 집합입니다. 클릭 하 여 **RawGameEventsTable**.

	![사용 하 고 생성 된 파이프라인][image-data-factory-monitoring-pipeline-consumed-produced]

6. 에 대 한 테이블 블레이드에서 **RawGameEventsTable**, 모든 슬라이스를 참조 하십시오. 에서는 다음 스크린샷과, 모든 조각에는 **준비** 상태 되며 문제가 조각 없습니다. 즉, 데이터를 처리할 준비가 되었습니다.

	![RawGameEventsTable 테이블 블레이드][image-data-factory-monitoring-raw-game-events-table]

	모두 **조각 최근에 업데이트** 및 **최근에 조각 실패 한** 목록을 기준으로 정렬 됩니다는 **마지막 업데이트 시간**. 다음과 같은 상황에서 조각의 업데이트 시간이 변경 됩니다.

	-  수동으로 업데이트할 있습니다 조각의 상태, 예를 사용 하 여는 **집합 AzureDataFactorySliceStatus** (또는)를 클릭 하 여 **실행** 에 **조각** 블레이드는 조각에 대 한 합니다.
	-  실행으로 인해 상태를 변경 하는 조각 (예: 시작을 실행 하는, 종료 하 고 실패 한 실행, 실행을 종료 하 고 성공, 등).
 
	목록 또는 의 제목을 클릭 **... (타원)** 더 큰 조각 목록을 확인 합니다. 클릭 하 여 **필터** 슬라이스를 필터링 하려면 도구 모음입니다.
	
	기준으로 정렬 된 조각 시작/종료 시간 대신 데이터 조각이 보려면 클릭 합니다. **(시간별 slice) 데이터 조각이** 바둑판식으로 배열 합니다.

	![Slice 시간별 데이터 조각][DataSlicesBySliceTime]
 
7. 이제는 **파이프라인** 에 대 한 블레이드 **PartiionGameLogsPipeline**, 클릭 **생산**.
8. 이 파이프라인에서 생성하는 데이터 집합 목록이 표시됩니다. 
9. 클릭 하 여 **PartitionedGameEvents** 테이블에 **데이터 집합 생성** 블레이드입니다. 
10.	확인 하는 **상태** 로 설정 된 모든 조각 **준비**. 
11.	조각 중 하나를 클릭 **준비** 참조 하는 **데이터 조각을** 에 대 한 해당 조각의 블레이드입니다.

	![RawGameEventsTable 데이터 조각을 블레이드][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	오류가 있으면 표시 되는 한 **실패 **여기 상태입니다. 상태와 함께 분할 두 영역을 볼 수도 있습니다 **준비**, 또는 둘 모두 상태와 함께 **PendingValidation**, 조각이 처리 되는 속도에 따라 합니다.

	조각에 없는 경우는 **준비** 상태를 준비 하지 않으며는 현재 조각에서 실행할 수 없도록 차단 여부를 지정 하는 업스트림 조각을 볼 수 있습니다는 **준비 하지 않은 업스트림 조각** 목록입니다.
 
	참조는 [Azure 데이터 팩터리 개발자 참조][developer-reference] 의 모든 가능한 슬라이스 상태 이해할 수 있습니다.

12.	에 **데이터 조각을** 블레이드를에서 실행을 클릭는 **활동 실행** 목록입니다. 해당 조각의 Activity Run 블레이드가 표시됩니다. 다음을 참조 해야 **작업 실행 세부 정보** 블레이드입니다.

	![활동 실행 정보 블레이드][image-data-factory-monitoring-activity-run-details]

13.	클릭 하 여 **다운로드** 파일을 다운로드 합니다. 이 화면은 HDInsight 처리의 오류를 해결할 때 특히 유용합니다.
	 
	
모든 파이프라인 실행을 완료 하는 경우로 볼 수 있습니다는 **MarketingCampaignEffectivenessTable** 에 **MarketingCampaigns** Azure SQL 데이터베이스는 결과 볼 수 있습니다.

**축하합니다.** 이제 워크플로를 모니터링하고 문제를 해결할 수 있습니다. 지금까지 Azure 데이터 팩터리를 사용하여 데이터를 처리하고 분석 결과를 얻는 방법을 배웠습니다.

## 온-프레미스 데이터를 사용 하 여이 자습서를 확장 합니다.
이 문서에서는 연습에서 시나리오를 처리 하는 로그의 마지막 단계에서는 마케팅 캠페인 효율성 출력 Azure SQL 데이터베이스에 복사 되었습니다. 이 데이터를 조직 내에서 분석하기 위해 온-프레미스 SQL Server로 이동할 수도 있습니다.
 
Azure Blob에서 마케팅 캠페인 효율성 데이터를 복사 하기 위해 온-프레미스 SQL Server, 추가 온-프레미스 연결 된 서비스를 만들 필요가,이 문서의 연습에 도입 된 테이블 및 파이프라인.

연습에서 [연습:를 사용 하 여 온-프레미스 데이터 원본][tutorial-onpremises] 마케팅 캠페인 효율성 데이터는 온-프레미스 SQL Server 데이터베이스를 복사 하는 파이프라인을 만드는 방법을 배울 수 있습니다.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->
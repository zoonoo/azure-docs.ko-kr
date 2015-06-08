<properties 
	pageTitle="Azure 데이터 팩터리를 사용하여 로그 파일 이동 및 처리" 
	description="이 고급 자습서에서는 거의 실제 시나리오를 설명하고 Azure 데이터 팩터리 서비스를 사용하여 시나리오를 구현합니다." 
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

# 자습서: 이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리 하 [PowerShell]
이 문서에서는 Azure 데이터 팩터리를 사용하여 로그 파일의 데이터를 유용한 정보로 변환하는 정식 로그 처리 시나리오로 구성된 완전한 연습을 제공합니다.

## 시나리오
Contoso는 여러 플랫폼에 대 한 게임을 만드는 게임 회사: 게임 콘솔, 핸드헬드의 장치 및 개인용 컴퓨터 (Pc). 이러한 각 게임은 많은 로그를 생성합니다. Contoso는 이러한 게임에서 생성되는 로그를 수집 및 분석하여 사용량 정보를 얻고, 업셀 및 교차 판매 기회를 포착하고, 탁월한 새 기능을 개발하는 등을 통해 비즈니스를 개선하고 고객에게 더 나은 환경을 제공하려는 목표가 있습니다.
 
이 연습에서는 샘플 로그를 수집하고, 참조 데이터를 사용하여 이 로그를 처리 및 보강하고, 데이터를 변환하여 Contoso가 최근 시작한 마케팅 캠페인의 효과를 평가합니다.

## 자습서 수행을 위한 준비
1.	읽기 [Azure 데이터 팩터리 소개][adfintroduction] Azure 데이터 팩터리의 개요 및 최상위 개념의 이해를 가져올 수 있습니다.
2.	이 자습서를 수행하려면 Azure 구독이 있어야 합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하십시오.
3.	다운로드 하 여 설치 해야 [Azure PowerShell][download-azure-powershell] 컴퓨터에 있습니다. 
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
종단간 워크플로 아래와 같은: ![자습서 종단간 흐름][image-data-factory-tutorial-end-to-end-flow]

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

1.	로그인 한 후는 [Azure 미리 보기 포털][azure-preview-portal], 클릭 **새로** 클릭 하 여 확인 하 고 왼쪽 아래 모퉁이에서 **데이터 팩터리** 에 **새로 만들기** 블레이드. 

	![새로운 사용할->][image-data-factory-new-datafactory-menu]
	
	표시 되지 않으면 **데이터 팩터리** 에 **새로** 블레이드를 아래로 스크롤합니다.
	
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

> [AZURE.NOTE]이 문서를 연결 된 서비스, 테이블 및 파이프라인을 만들려면 Azure PowerShell을 사용 합니다. 참조 [데이터 팩터리 편집기를 사용 하 여 자습서][adftutorial-using-editor] Azure 포털, 특히 데이터 팩터리 편집기를 사용 하 여이 자습서를 수행 하려는 경우.

이 단계에서는 다음 연결 된 서비스 만듭니다: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService, 및 HDInsightLinkedService 합니다.


1.	에 **LogProcessingFactory** 블레이드를 클릭 하 여 **연결 된 서비스** 바둑판식으로 배열 합니다.

	![연결 된 서비스 타일][image-data-factory-tutorial-linkedservice-tile]

2. 에 **연결 된 서비스** 블레이드를 클릭 하 여 **+ 데이터 저장소** 명령 모음에서.

	![연결 된 서비스-저장소를 추가 합니다.][image-data-factory-tutorial-linkedservices-add-datstore]

3. 에 **새로운 데이터 저장소** 블레이드를 입력 **StorageLinkedService** 에 대 한는 **이름**, 를 클릭 하 여 **유형 (설정 필요)**, 선택 하 고 **Azure 저장소 계정**.

	![데이터 저장소 유형-Azure 저장소][image-data-factory-tutorial-datastoretype-azurestorage]

4. 에 **새로운 데이터 저장소** 블레이드를 새로운 두 필드를 참조 합니다: **계정 이름** 및 **계정 키**. 계정 이름과 계정 키를 입력 하면 **Azure 저장소 계정**.

	![Azure 저장소 설정][image-data-factory-tutorial-azurestorage-settings]

	Azure 저장소 계정의 계정 이름과 계정 키는 아래와 같이 포털에서 얻을 수 있습니다.

	![저장소 키][image-data-factory-tutorial-storage-key]
  
5. 클릭 한 후 **확인** 저장 블레이드를 새 데이터에 표시 되어야 **StorageLinkedService** 목록에서 **데이터 저장소** 에 **연결 된 서비스** 블레이드입니다. 확인 **알림을** 허브 (왼쪽)에 모든 메시지를 확인 합니다.

	![저장소와 연결 된 서비스 블레이드][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. 반복 **2-5 단계를** 연결 된 명명 된 서비스를 만드는 다른: **HDInsightStorageLinkedService**. 이 서비스는 HDInsight 클러스터에서 사용하는 저장소입니다.
7. 모두 표시 되는지 확인 **StorageLinkedService** 및 **HDInsightStorageLinkedService** 목록에서 연결 된 서비스 블레이드입니다.
8. 에 **연결 된 서비스** 블레이드를 클릭 하 여 **추가 (+) 데이터 저장소** 명령 모음에서.
9. 입력 **AzureSqlLinkedService** 이름입니다.
10. 클릭 하 여 **유형 (설정 필요)**, 선택, **Azure SQL 데이터베이스**.
11. 이제을 수행 해야 다음 추가 필드에는 **새로운 데이터 저장소** 블레이드입니다. Azure SQL 데이터베이스의 이름을 입력 **서버**, **데이터베이스** 이름, **사용자 이름**, 및 **암호**, 를 클릭 하 고 **확인**.
	1. 입력 **MarketingCampaigns** 에 대 한는 **데이터베이스**. 1 단계에서에서 실행 되는 스크립트로 만든 Azure SQL 데이터베이스입니다. 이 데이터베이스 실제로 만들어졌는지 스크립트를 통해 (경우에 오류가 발생 했습니다)를 확인 해야 합니다.
		
 		![Azure SQL 설정][image-data-factory-tutorial-azuresql-settings]

		Azure 관리 포털에서 이러한 값을 가져오려면: MarketingCampaigns 데이터베이스에 대 한 연결 문자열을 SQL 데이터베이스 보기를 클릭 합니다.

		![Azure SQL 데이터베이스 연결 문자열][image-data-factory-tutorial-azuresql-database-connection-string]

12. 만든 모든 3 개의 데이터 저장소에 표시 되는지 확인: **StorageLinkedService**, **HDInsightStorageLinkedService**, 및 **AzureSqlLinkedService**.
13. 다른 연결 된 서비스를 만들어야 하지만 이것은 계산 서비스에 특히 **Azure HDInsight 클러스터**. 아직은 포털에서 계산 연결된 서비스를 만들 수 없습니다. 따라서 이 연결된 서비스를 만들려면 Azure PowerShell을 사용해야 합니다. 
14. 전환 **Azure PowerShell** 이미 열려있는 (또는) 시작 하는 경우 **Azure PowerShell**. Azure PowerShell을 닫았다가 줄일 했습니다 하면, 다음 명령을 실행 해야 합니다. 
	- 실행 **Add-azureaccount** 사용자 이름 및 Azure 미리 보기 포털에 로그인 하는데 사용할 암호를 입력 합니다.  
	- 실행 **Get-azuresubscription** 이 계정에 대 한 모든 구독을 볼 수 있습니다.
	- 실행 **Select-azuresubscription** 를 사용 하려면 구독을 선택 합니다. 이 구독은 Azure 미리 보기 포털에서 사용한 것과 같아야 합니다. 
15. 전환 **AzureResourceManager** Azure 데이터 팩터리 cmdlet으로 모드는이 모드에서 사용할 수 있습니다.

		Switch-AzureMode AzureResourceManager

16. 탐색 하는 **LinkedServices** 하위 폴더에 **C:\ADFWalkthrough** (또는)가 압축을 푼 파일 위치의 폴더에서.
17. 열기 **HDInsightLinkedService.json** 좋아하는 편집기 및 알림 유형으로 설정 된 **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다. HDInsightLinkedService 데이터 팩터리를 주문형 HDInsight 클러스터를 연결합니다. 사용자 고유의 HDInsight 클러스터를 사용 하려면 (replace clustername, 사용자 이름 및 암호를 적절 한 값) 아래와 같이 HDInsightLinkedService.json 파일의 속성 섹션을 업데이트 합니다.
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. 다음 명령을 사용하여 $df 변수를 데이터 팩터리의 이름으로 설정합니다.

		$df = “LogProcessingFactory”
19. Cmdlet을 사용 하 여 **새로 AzureDataFactoryLinkedService** 를 다음과 같이 연결 된 서비스를 만듭니다. 저장소 계정으로 시작합니다.

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	ResourceGroupName, DataFactoryName 또는 LinkedService 이름에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 파일을 찾을 수 없는 경우 연결된 서비스 JSON 파일의 전체 파일 경로 제공합니다.
20. 모든 4 개의 연결 된 서비스에 표시 되어야는 **연결 된 서비스** 블레이드 아래와 같이 합니다. 연결 된 서비스 블레이드 열려 있으면 클릭에 연결 된 서비스는 **데이터 팩터리** 페이지에 대 한 **LogProcessingFactory**. 연결된 서비스 블레이드가 새로 고쳐질 때까지 몇 초 정도 걸릴 수 있습니다.

	![서비스에 연결 된 모든][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> 4 단계: 테이블 만들기 
이 단계에서는 다음 테이블을 만듭니다.

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![자습서-종단간 흐름][image-data-factory-tutorial-end-to-end-flow]
 
위 그림에서는 가운데 행에는 파이프라인을, 위쪽과 아래쪽 행에는 테이블을 표시합니다.

이 릴리스에서는 Azure 포털을 통해 데이터 집합/테이블을 만들 수 없으므로 Azure PowerShell을 사용하여 테이블을 만들어야 합니다.

### 테이블을 만들려면

1.	Azure PowerShell에서 이동 된 **테이블** 폴더 (* * C:\ADFWalkthrough\Tables**) 예제를 추출한 위치에서. 
2.	Cmdlet을 사용 하 여 **새로 AzureDataFactoryTable** 에 대 한 다음과 같은 테이블을 만들기 위해 **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	ResourceGroupName 및 DataFactoryName에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 cmdlet에서 파일을 찾을 수 없는 경우 테이블 JSON 파일의 전체 파일 경로 제공합니다.

3. 이전 단계를 반복하여 다음 테이블을 만듭니다.
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. 에 **Azure 미리 보기 포털**, 클릭 **데이터 집합** 에 **데이터 팩터리** 블레이드에 대 한 **LogProcessingFactory** 모든 데이터 집합에 표시 되는지 확인 하 고 (테이블은 사각형 데이터 집합 임).

	![모든 데이터 집합][image-data-factory-tutorial-datasets-all]

	Azure PowerShell에서 다음 명령을 사용할 수도 있습니다.
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> 5 단계: 만들기 및 파이프라인을 예약
이 단계에서는 다음 파이프라인 만듭니다: PartitionGameLogsPipeline, EnrichGameLogsPipeline, 및 AnalyzeMarketingCampaignPipeline 합니다.

1.  **Windows 탐색기**, 탐색 하는 **파이프라인** 하위 폴더에 **C:\ADFWalkthrough** 폴더 (또는 예제를 추출한 위치에서).
2.	열기 **PartitionGameLogsPipeline.json** 선호 하는 편집기에서 강조 표시 된 데이터 저장소 계정 정보에 대 한 저장소 계정의 바꾸고 파일을 저장 합니다.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 단계를 반복하여 다음 파이프라인을 만듭니다.
	1. **EnrichGameLogsPipeline**.json (3 회)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 회)

	**중요:** 모두 대체 한 확인 <storageaccountname> 을 저장소 계정 이름입니다.
 
4.   **Azure PowerShell**, 탐색 하는 **파이프라인** 하위 폴더에 **C:\ADFWalkthrough** 폴더 (또는 예제를 추출한 위치에서).
5.  Cmdlet을 사용 하 여 **새로 AzureDataFactoryPipeline** 에 대 한 파이프라인을 다음과 같이 만들려는 **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	ResourceGroupName, DataFactoryName 또는 Pipeline 이름에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 파이프라인 JSON 파일의 전체 파일 경로 제공합니다.
6. 이전 단계를 반복하여 다음 파이프라인을 만듭니다.
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Cmdlet을 사용 하 여 **Get AzureDataFactoryPipeline** 를 파이프라인의 목록을 가져옵니다.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. 파이프라인이 만들어지면 데이터 처리가 수행되는 기간을 지정할 수 있습니다. 파이프라인의 활성 기간을 지정하면 각 ADF 테이블에 대해 정의된 가용성 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.

파이프라인의 활성 기간을 지정하려면 Set-AzureDataFactoryPipelineActivePeriod cmdlet을 사용할 수 있습니다. 이 연습에서는 예제 데이터-05-01에서 05/05는 합니다. 사용 하 여 2014-05-01로는 시작 날짜 시간입니다. EndDateTime은 선택 사항입니다.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. 파이프라인의 활성 기간 설정을 확인합니다.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 이전의 두 단계를 반복하여 다음 파이프라인의 활성 기간을 설정합니다.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. 에 **Azure 미리 보기 포털**, 클릭 **파이프라인** (파이프라인의 이름)에 없는 타일에 **데이터 팩터리** 에 대 한 블레이드는 **LogProcessingFactory**, 만든 파이프라인 표시 되어야 합니다.

	![모든 파이프라인][image-data-factory-tutorial-pipelines-all]

12. 에 **데이터 팩터리** 에 대 한 블레이드는 **LogProcessingFactory**, 클릭 **다이어그램**.

	![다이어그램 링크][image-data-factory-tutorial-diagram-link]

13. 다이어그램을 다시 정렬할 수 있으며, 다음은 맨 위에 직접 입력, 맨 아래에 출력이 표시되도록 다시 정렬한 다이어그램입니다. 볼 수 있습니다의 출력의 **PartitionGameLogsPipeline** EnrichGameLogsPipeline 및의 출력에 대 한 입력으로 전달 되어는 **EnrichGameLogsPipeline** 에 전달 되는 **AnalyzeMarketingCampaignPipeline**. 제목을 두 번 클릭하면 블레이드가 나타내는 아티팩트에 대한 자세한 내용이 표시됩니다.

	![다이어그램 보기][image-data-factory-tutorial-diagram-view]

	**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 파이프라인, 테이블을 만들었고 워크플로를 시작했습니다.


## <a name="MainStep6"></a> 6 단계: 모니터링 파이프라인 및 데이터 조각 

1.	LogProcessingFactory에 대한 데이터 팩터리 블레이드가 열려 있지 않은 경우 다음 중 하나를 수행할 수 있습니다.
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
 
7. 이제는 **파이프라인** 에 대 한 블레이드 **PartiionGameLogsPipeline**, 클릭 **생산**.
8. 이 파이프라인에서 생성하는 데이터 집합 목록이 표시됩니다. 
9. 클릭 하 여 **PartitionedGameEvents** 테이블에 **데이터 집합 생성** 블레이드입니다. 
10.	확인 하는 **상태** 로 설정 된 모든 조각 **준비**. 
11.	조각 중 하나를 클릭 **준비** 참조 하는 **데이터 조각을** 에 대 한 해당 조각의 블레이드입니다.

	![RawGameEventsTable 데이터 조각을 블레이드][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	오류가 있으면 표시 되는 한 **실패 **여기 상태입니다. 상태와 함께 분할 두 영역을 볼 수도 있습니다 **준비**, 또는 둘 모두 상태와 함께 **PendingValidation**, 조각이 처리 되는 속도에 따라 합니다.
 
	참조는 [Azure 데이터 팩터리 개발자 참조][developer-reference] 의 모든 가능한 슬라이스 상태 이해할 수 있습니다.

12.	에 **데이터 조각을** 블레이드를에서 실행을 클릭는 **활동 실행** 목록입니다. 해당 조각의 Activity Run 블레이드가 표시됩니다. 다음을 참조 해야 **작업 실행 세부 정보** 블레이드입니다.

	![활동 실행 정보 블레이드][image-data-factory-monitoring-activity-run-details]

13.	클릭 하 여 **다운로드** 파일을 다운로드 합니다. 이 화면은 HDInsight 처리의 오류를 해결할 때 특히 유용합니다.
	 
	
모든 파이프라인 실행을 완료 하는 경우로 볼 수 있습니다는 **MarketingCampaignEffectivenessTable** 에 **MarketingCampaigns** Azure SQL 데이터베이스는 결과 볼 수 있습니다.

**축하합니다.** 이제 워크플로를 모니터링하고 문제를 해결할 수 있습니다. 지금까지 Azure 데이터 팩터리를 사용하여 데이터를 처리하고 분석 결과를 얻는 방법을 배웠습니다.

## 온-프레미스 데이터를 사용 하 여이 자습서를 확장 합니다.
이 문서에서는 연습에서 시나리오를 처리 하는 로그의 마지막 단계에서는 마케팅 캠페인 효율성 출력 Azure SQL 데이터베이스에 복사 되었습니다. 이 데이터를 조직 내에서 분석하기 위해 온-프레미스 SQL Server로 이동할 수도 있습니다.
 
Azure Blob에서 마케팅 캠페인 효율성 데이터를 복사 하기 위해 온-프레미스 SQL Server, 추가 온-프레미스 연결 된 서비스를 만들 필요가,이 문서의 연습에 도입 된 테이블 및 파이프라인.

연습에서 [연습:를 사용 하 여 온-프레미스 데이터 원본][tutorial-onpremises-using-powershell] 마케팅 캠페인 효율성 데이터는 온-프레미스 SQL Server 데이터베이스를 복사 하는 파이프라인을 만드는 방법을 배울 수 있습니다.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->
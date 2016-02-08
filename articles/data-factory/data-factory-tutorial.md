<properties 
	pageTitle="Azure Data Factory를 사용하여 로그 파일 이동 및 처리(Azure 클래식 포털)" 
	description="이 고급 자습서에서는 거의 실제 시나리오를 설명하고 Azure 클래식 포털의 Azure Data Factory 서비스 및 데이터 팩터리 편집기를 사용하여 시나리오를 구현합니다." 
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

# 자습서: 마케팅 캠페인의 효과 측정  
Contoso는 게임 콘솔, 핸드헬드 장치, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 이러한 게임은 많은 로그를 생성하며, Contoso의 목표는 이러한 로그를 수집 및 분석하여 고객 선호도, 인구 통계, 사용 동작 등에 대한 정보를 얻고, 업셀 및 교차 판매 기회를 포착하고, 탁월한 새 기능을 개발하여 비즈니스 성장을 견인하고, 고객에게 더 나은 환경을 제공하는 것입니다.

이 자습서에서는 샘플 로그를 수집하고, 참조 데이터를 사용하여 이 로그를 처리 및 보강하고, 데이터를 변환하여 Contoso가 최근 시작한 마케팅 캠페인의 효과를 평가하는 데이터 팩터리 파이프라인을 만듭니다. 다음 3개의 파이프라인이 있습니다.

1.	**PartitionGameLogsPipeline**은 Blob 저장소에서 원시 게임 이벤트를 읽고 연도, 월 및 일을 기준으로 파티션을 만듭니다.
2.	**EnrichGameLogsPipeline**은 분할된 게임 이벤트를 지역 코드 참조 데이터와 조인하고 IP 주소를 해당하는 지리적 위치에 매핑하여 데이터를 강화합니다.
3.	**AnalyzeMarketingCampaignPipeline** 파이프라인은 강화된 데이터를 활용하고 이 데이터를 광고 데이터와 함께 처리하여 마케팅 캠페인 효과가 포함된 최종 출력을 만듭니다.

## 자습서 수행을 위한 준비
1.	[Azure Data Factory 소개][adfintroduction]를 읽고 Azure Data Factory에 대한 개요를 확인하고 최상위 개념을 이해합니다.
2.	이 자습서를 수행하려면 Azure 구독이 있어야 합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션](https://azure.microsoft.com/pricing/purchase-options/), [구성원 제공 항목](https://azure.microsoft.com/pricing/member-offers/) 또는 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
3.	컴퓨터에 [Azure PowerShell][download-azure-powershell]을 다운로드하고 설치해야 합니다. 데이터 팩터리 cmdlet을 실행하여 샘플 데이터 및 pig/hive 스크립트를 Blob 저장소로 업로드합니다. 
2.	**(권장)** [Azure Data Factory 시작][adfgetstarted] 문서의 간단한 자습서를 검토하고 연습하여 포털 및 cmdlet을 익힙니다.
3.	**(권장)** [Azure 데이터 팩터리에서 Pig 및 Hive 사용][usepigandhive] 문서의 연습을 검토하고 실습하여 온-프레미스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만들어 봅니다.
4.	[ADFWalkthrough][adfwalkthrough-download] 파일을 **C:\\ADFWalkthrough** 폴더로 다운로드합니다. 이때 다음과 같이 **폴더 구조를 유지**합니다.
	- **Pipelines:** 파이프라인 정의가 포함된 JSON 파일을 포함합니다.
	- **Tables:** 테이블 정의가 포함된 JSON 파일을 포함합니다.
	- **LinkedServices:** 저장소 및 계산(HDInsight) 클러스터에 대한 정의가 포함된 JSON 파일을 포함합니다. 
	- **Scripts:** 데이터 처리에 사용되고 파이프라인에서 호출되는 Hive 및 Pig 스크립트를 포함합니다.
	- **SampleData:** 이 연습의 샘플 데이터를 포함합니다.
	- **OnPremises:** 온-프레미스 데이터 액세스를 보여 주는 데 사용되는 스크립트 및 JSON 파일을 포함합니다.
	- **uploadSampleDataAndScripts.ps1:** 이 스크립트는 샘플 데이터 및 스크립트를 Azure에 업로드합니다.
5. 다음과 같은 Azure 리소스를 만들었는지 확인합니다.			
	- Azure 저장소 계정
	- Azure SQL 데이터베이스
	- Azure HDInsight 클러스터 버전 3.1 이상(또는 데이터 팩터리 서비스에서 자동으로 만드는 주문형 HDInsight 클러스터 사용)	
7. Azure 리소스를 만들었으면 이러한 각 리소스에 연결하는 데 필요한 정보를 알고 있는지 확인합니다.
 	- **Azure 저장소 계정** - 계정 이름 및 계정 키  
	- **Azure SQL 데이터베이스** - 서버, 데이터베이스, 사용자 이름 및 암호
	- **Azure HDInsight 클러스터** - HDInsight 클러스터 이름, 사용자 이름, 암호 및 이 클러스터와 연결된 Azure 저장소의 계정 이름과 계정 키. 사용자 고유의 HDInsight 클러스터 대신 주문형 HDInsight 클러스터를 사용하려는 경우 이 단계를 건너뛸 수 있습니다.  
8. **Azure PowerShell**을 시작하고 다음 명령을 실행합니다. Azure PowerShell을 열어둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
	- **Add-AzureAccount**를 실행하고 Azure 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 포털에서 사용한 것과 같아야 합니다.	

## 개요
종단 간 워크플로는 아래와 같습니다.

![자습서 종단 간 흐름][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline**은 Blob 저장소에서 원시 게임 이벤트(RawGameEventsTable)를 읽고 연도, 월 및 일을 기준으로 파티션(PartitionedGameEventsTable)을 만듭니다.
2. **EnrichGameLogsPipeline**은 분할된 게임 이벤트(PartitionedGameEvents 테이블, PartitionGameLogsPipeline의 출력)를 지역 코드(RefGetoCodeDictionaryTable)와 조인하고 IP 주소를 해당하는 지리적 위치(EnrichedGameEventsTable)에 매핑하여 데이터를 보강합니다.
3. **AnalyzeMarketingCampaignPipeline** 파이프라인은 보강된 데이터(EnrichGameLogsPipeline에서 생성한 EnrichedGameEventTable)를 활용하고 이 데이터를 광고 데이터(RefMarketingCampaignnTable)와 함께 처리하여 마케팅 캠페인 효과에 대한 최종 출력을 만듭니다. 이 출력은 Azure SQL 데이터베이스(MarketingCampainEffectivensessSQLTable) 및 Azure Blob 저장소(MarketingCampaignEffectivenessBlobTable)로 복사되어 분석됩니다.
    
## 연습: 워크플로 만들기, 배포 및 모니터링
1. [1단계: 샘플 데이터 및 스크립트 업로드](#MainStep1) 이 단계에서는 모든 샘플 데이터(모든 로그 및 참조 데이터 포함)와 워크플로에서 실행할 Hive/Pig 스크립트를 업로드합니다. 실행하는 스크립트에서는 MarketingCampaigns라는 Azure SQL 데이터베이스, 테이블, 사용자 정의 형식 및 저장 프로시저도 만듭니다.
2. [2단계: Azure 데이터 팩터리 만들기](#MainStep2) 이 단계에서는 LogProcessingFactory라는 Azure Data Factory를 만듭니다.
3. [3단계: 연결된 서비스 만들기](#MainStep3) 이 단계에서는 연결된 서비스 
	
	- 	**StorageLinkedService**. 원시 게임 이벤트, 분할된 게임 이벤트, 보강된 게임 이벤트, 마케팅 캠페인 효과 정보, 참조 지역 코드 데이터 및 참조 마케팅 캠페인 데이터를 포함하는 Azure 저장소 위치를 LogProcessingFactory에 연결합니다.   
	- 	**AzureSqlLinkedService**. 마케팅 캠페인 효과 정보를 포함하는 Azure SQL 데이터베이스를 연결합니다. 
	- 	**HDInsightStorageLinkedService**. HDInsightLinkedService가 참조하는 HDInsight 클러스터와 관련된 Azure Blob 스토리지를 연결합니다. 
	- 	**HDInsightLinkedService**. Azure HDInsight 클러스터를 LogProcessingFactory에 연결합니다. 이 클러스터는 데이터에 대한 pig/hive 처리를 수행하는 데 사용됩니다. 
 		
4. [4단계: 테이블 만들기](#MainStep4) 이 단계에서는 다음 테이블을 만듭니다.
	
	- **RawGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 원시 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 분할된 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 참조 지역 코드 데이터가 있는 위치를 지정합니다(adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 참조 마케팅 캠페인 데이터가 있는 위치를 지정합니다(adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내의 강화된 게임 이벤트 데이터가 있는 위치를 지정합니다(adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. 이 테이블은 AzureSqlLinkedService에서 정의한 Azure SQL 데이터베이스에서 마케팅 캠페인 효과 데이터를 포함하는 SQL 테이블(MarketingCampaignEffectiveness)을 지정합니다. 
	- **MarketingCampaignEffectivenessBlobTable**. 이 테이블은 StorageLinkedService에서 정의한 Azure Blob 저장소 내에서 마케팅 캠페인 효과 데이터가 있는 위치를 지정합니다(adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [5단계: 파이프라인 만들기 및 예약](#MainStep5) 이 단계에서는 다음 파이프라인을 만듭니다.
	- **PartitionGameLogsPipeline**. 이 파이프라인은 Blob 저장소에서 원시 게임 이벤트(RawGameEventsTable)를 읽고 연도, 월 및 일을 기준으로 파티션(PartitionedGameEventsTable)을 만듭니다. 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. 이 파이프라인은 분할된 게임 이벤트(PartitionedGameEvents 테이블, PartitionGameLogsPipeline의 출력)를 지역 코드(RefGetoCodeDictionaryTable)와 조인하고 IP 주소를 해당하는 지리적 위치(EnrichedGameEventsTable)에 매핑하여 데이터를 보강합니다.

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. 이 파이프라인은 보강된 게임 이벤트 데이터(EnrichGameLogsPipeline에서 생성한 EnrichedGameEventTable)를 활용하고 이 데이터를 광고 데이터(RefMarketingCampaignnTable)와 함께 처리하여 마케팅 캠페인 효과에 대한 최종 출력을 만듭니다. 이 출력은 Azure SQL 데이터베이스(MarketingCampainEffectivensessSQLTable) 및 Azure Blob 저장소(MarketingCampaignEffectivenessBlobTable)로 복사되어 분석됩니다.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [6단계: 파이프라인 및 데이터 조각 모니터링](#MainStep6) 이 단계에서는 Azure 클래식 포털을 사용하여 파이프라인, 테이블 및 데이터 조각을 모니터링합니다.

## <a name="MainStep1"></a> 1단계: 샘플 데이터 및 스크립트 업로드
이 단계에서는 모든 샘플 데이터(로그 및 참조 데이터 모두 포함)와 워크플로에서 호출되는 Hive/Pig 스크립트를 업로드합니다. 실행하는 스크립트에서는 **MarketingCampaigns**라는 Azure SQL 데이터베이스, 테이블, 사용자 정의 형식 및 저장 프로시저도 만듭니다.

테이블, 사용자 정의 형식 및 저장 프로시저는 Azure Blob 저장소에서 Azure SQL 데이터베이스로 마케팅 캠페인 효과 결과를 이동할 때 사용됩니다.

1. 즐겨 사용하는 편집기에서 **uploadSampleDataAndScripts.ps1** 파일을 **C:\\ADFWalkthrough** 폴더(또는 압축을 푼 파일이 포함된 폴더)에서 열고 강조 표시된 내용을 클러스터 정보로 바꾼 다음 파일을 저장합니다.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	이 스크립트를 사용하려면 컴퓨터에 sqlcmd 유틸리티가 설치되어 있어야 합니다. SQL Server를 설치한 경우에는 이 유틸리티가 이미 설치되어 있습니다. 그렇지 않은 경우 유틸리티를 [다운로드][sqlcmd-install]하고 설치합니다.
	
	또는 C:\\ADFWalkthrough\\Scripts 폴더의 파일을 사용하여 pig/hive 스크립트 및 샘플 파일을 blob 저장소의 adfwalkthrough 컨테이너로 업로드하고 MarketingCamapaigns Azure SQL 데이터베이스에 MarketingCampaignEffectiveness 테이블을 만들 수 있습니다.
   
2. 로컬 컴퓨터가 Azure SQL 데이터베이스에 액세스할 수 있는지 확인합니다. 액세스할 수 있게 하려면 [Azure 클래식 포털](http://manage.windowsazure.com)이나 master 데이터베이스의 **sp\_set\_firewall\_rule**을 사용하여 컴퓨터의 IP 주소에 대한 방화벽 규칙을 만듭니다. 이 변경 내용이 적용되려면 최대 5분까지 걸릴 수 있습니다. [Azure SQL에 대한 방화벽 규칙 설정][azure-sql-firewall]을 참조하세요.
4. Azure PowerShell에서 샘플의 압축을 푼 위치(**C:\\ADFWalkthrough**)로 이동합니다.
5. **uploadSampleDataAndScripts.ps1** 실행 
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

## <a name="MainStep2"></a> 2단계: Azure 데이터 팩터리 만들기
이 단계에서는 **LogProcessingFactory**라는 Azure 데이터 팩터리를 만듭니다.

1.	[Azure 포털][azure-portal]에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭하고 **만들기** 블레이드에서 **데이터 분석**을 클릭한 다음 **데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다. 

	![새로 만들기->DataFactory][image-data-factory-new-datafactory-menu]

5. **새 데이터 팩터리** 블레이드에서 **LogProcessingFactory**를 **이름**으로 입력합니다.

	![데이터 팩터리 블레이드][image-data-factory-tutorial-new-datafactory-blade]

6. **ADF**라는 Azure 리소스 그룹을 아직 만들지 않은 경우 다음을 수행합니다.
	1. **리소스 그룹 이름**을 클릭하고 **새 리소스 그룹 만들기**를 클릭합니다.
	
		![리소스 그룹 블레이드][image-data-factory-tutorial-resourcegroup-blade]
	2. **리소스 그룹 만들기** 블레이드에서 리소스 그룹 이름으로 **ADF**를 입력하고 **확인**을 클릭합니다.
	
		![리소스 그룹 만들기][image-data-factory-tutorial-create-resourcegroup]
7. **리소스 그룹 이름**으로 **ADF**를 선택합니다.  
8.	**새 데이터 팩터리** 블레이드에서는 **시작 보드에 추가**가 기본적으로 선택되어 있습니다. 선택된 경우 데이터 팩터리에 대한 링크가 시작 보드(Azure 포털에 로그인할 때 표시되는 페이지)에 추가됩니다.

	![데이터 팩터리 만들기 블레이드][image-data-factory-tutorial-create-datafactory]

9.	**새 데이터 팩터리** 블레이드에서 **만들기**를 클릭하여 데이터 팩터리를 만듭니다.
10.	데이터 팩터리가 만들어지면 **LogProcessingFactory**라는 제목의 **데이터 팩터리** 블레이드가 표시됩니다.

	![데이터 팩터리 홈페이지][image-data-factory-tutorial-datafactory-homepage]

	
	이 블레이드가 표시되지 않는 경우 다음 중 하나를 수행합니다.

	- **시작 보드**(홈페이지)에서 **LogProcessingFactory**를 클릭합니다.
	- 왼쪽에서 **찾아보기**를 클릭하고, **모두**, **데이터 팩터리**를 차례로 클릭한 다음 데이터 팩터리를 클릭합니다.
 
	Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “LogProcessingFactory”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다(예: yournameLogProcessingFactory). 이 자습서의 단계를 수행하는 동안 LogProcessingFactory 대신 이 이름을 사용합니다.
 
## <a name="MainStep3"></a> 3단계: 연결된 서비스 만들기

> [AZURE.NOTE] 이 문서에서는 Azure 클래식 포털, 특히 데이터 팩터리 편집기를 사용하여 연결된 서비스, 테이블 및 파이프라인을 만듭니다. Azure PowerShell을 사용하여 이 자습서를 수행하려면 [Azure PowerShell 사용 자습서][adftutorial-using-powershell]를 참조하세요.

이 단계에서는 연결된 서비스

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### StorageLinkedService 및 HDInsightStorageLinkedService 만들기

1.	**데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

2.  **편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **Azure 저장소**를 선택합니다. 오른쪽 창에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다.
	
	![편집기 새 데이터 저장소 단추][image-editor-newdatastore-button]

3. **accountname** 및 **accountkey**를 Azure 저장소 계정의 계정 이름 및 계정 키 값으로 바꿉니다.

	![편집기 Blob 저장소 JSON][image-editor-blob-storage-json]
	
	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.

4. 도구 모음에서 **배포**를 클릭하여 StorageLinkedService를 배포합니다. 제목 표시줄에 **연결된 서비스가 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

5. 단계를 반복하여 HDInsight 클러스터와 연결된 저장소에 대해 **HDInsightStorageLinkedService**라는 다른 Azure 저장소 연결된 서비스를 만듭니다. 연결된 서비스에 대한 JSON 스크립트에서 **name** 속성의 값을 **HDInsightStorageLinkedService**로 변경합니다.

### AzureSqlLinkedService 만들기
1. **데이터 팩터리 편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **Azure SQL 데이터베이스**를 선택합니다. 오른쪽 창에 Azure SQL 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다.
2. **servername**, ****username@servername** 및 **password**를 Azure SQL Server의 이름, 사용자 계정 및 암호로 바꿉니다.
3. **databasename**을 **MarketingCampaigns**로 바꿉니다. 이 Azure SQL 데이터베이스는 1단계에서 실행한 스크립트에 의해 생성되었습니다. 이 데이터베이스가 스크립트에 의해 실제로 생성되었는지 확인해야 합니다(오류가 발생한 경우). 
3. 도구 모음에서 **배포**를 클릭하여 AzureSqlLinkedService를 만들고 배포합니다.

### HDInsightLinkedService 만들기
Azure Data Factory 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다.

#### 주문형 HDInsight 클러스터를 사용하려면
1. 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **주문형 HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterSize** 속성에 대해 HDInsight 클러스터의 크기를 지정합니다.
	3. **timeToLive** 속성에 대해 고객이 삭제되기 전에 유휴 상태로 유지될 수 있는 기간을 지정합니다. 
	4. **version** 속성에 대해 사용할 HDInsight 버전을 지정합니다. 이 속성을 제외하면 최신 버전이 사용됩니다.  
	5. **linkedServiceName**에 대해 시작 자습서에서 만든 **HDInsightStorageLinkedService**를 지정합니다. 

			{
			    "name": "HDInsightOnDemandLinkedService",
			    "properties": {
			        "type": "HDInsightOnDemand",
			        "description": "",
			        "typeProperties": {
			            "clusterSize": "4",
			            "timeToLive": "00:30:00",
			            "version": "3.2",
			            "linkedServiceName": "StorageLinkedService"
			        }
			    }
			}

		연결된 서비스의 **type**을 **HDInsightOnDemand**로 설정합니다.

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.
   
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterUri** 속성에 대해 HDInsight의 URL을 입력합니다. 예를 들어 https://<clustername>.azurehdinsight.net/을 입력합니다.     
	2. **UserName** 속성에 대해 HDInsight 클러스터에 액세스할 수 있는 사용자 이름을 입력합니다.
	3. **password** 속성에 대해 사용자 암호를 입력합니다. 
	4. **LinkedServiceName** 속성에 대해 **StorageLinkedService**를 입력합니다. 시작 자습서에서 만든 연결된 서비스입니다. 

	연결된 서비스의 **type**을 **HDInsightBYOCLinkedService**로 설정합니다(BYOC는 사용자 고유의 클러스터 가져오기의 약어임).

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.


## <a name="MainStep4"></a> 4단계: 테이블 만들기
 
이 단계에서는 다음 데이터 팩터리 테이블을 만듭니다.

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![자습서 종단 간 흐름][image-data-factory-tutorial-end-to-end-flow]
 
위 그림에서는 가운데 행에는 파이프라인을, 위쪽과 아래쪽 행에는 테이블을 표시합니다.

### 테이블을 만들려면
	
1. 데이터 팩터리에 대한 **편집기**의 도구 모음에서 **새 데이터 집합** 단추를 클릭하고 드롭다운 메뉴에서 **Azure Blob 저장소**를 클릭합니다. 
2. 오른쪽 창의 JSON을 **C:\\ADFWalkthrough\\Tables** 폴더의 **RawGameEventsTable.json** 파일에 있는 JSON 스크립트로 바꿉니다.
3. 도구 모음에서 **배포**를 클릭하여 테이블을 만들고 배포합니다. 편집기의 제목 표시줄에 **테이블이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.
4. 다음 파일의 내용으로 1-3단계를 반복합니다. 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. 다음 파일의 내용으로 1-3단계를 반복합니다. 하지만 **새 데이터 집합**을 클릭한 후 **Azure SQL**을 선택합니다.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> 5단계: 파이프라인 만들기 및 예약
이 단계에서는 다음 파이프라인을 만듭니다.

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### 파이프라인을 만들려면

1. **데이터 팩터리 편집기**의 도구 모음에서 **새 파이프라인** 단추를 클릭합니다. 단추가 표시되지 않는 경우 도구 모음에서 **... (줄임표)**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.
2. 오른쪽 창의 JSON을 **C:\\ADFWalkthrough\\Pipelines** 폴더의 **PartitionGameLogsPipeline.json** 파일에 있는 JSON 스크립트로 바꿉니다.
3. JSON의 **닫는 대괄호 (']')** 끝에 **쉼표 (',')**를 추가한 다음 닫는 대괄호 후 다음 세 줄을 추가합니다. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	이 연습에서 예제 데이터는 2014년 5월 1일부터 2014년 5월 5일까지이기 때문에 시작 시간과 종료 시간이 2014년 5월 1일부터 2014년 5월 5일까지 설정됩니다.
 	
	주문형 HDInsight 연결된 서비스를 사용하는 경우 **linkedServiceName** 속성을 **HDInsightOnDemandLinkedService**로 설정합니다.
3. 도구 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다. 편집기의 제목 표시줄에 **파이프라인이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.
4. 다음 파일의 내용으로 1-3단계를 반복합니다. 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. **X**(오른쪽 위)를 눌러 데이터 팩터리 블레이드를 닫으면 데이터 팩터리의 홈 페이지(**DATA FACTORY **블레이드)가 표시됩니다.

### 다이어그램 뷰

1. **LogProcessingFactory**의 **데이터 팩터리** 블레이드에서 **다이어그램**을 클릭합니다. 

	![다이어그램 링크][image-data-factory-tutorial-diagram-link]

2. 다이어그램을 다시 정렬할 수 있으며, 다음은 맨 위에 직접 입력, 맨 아래에 출력이 표시되도록 다시 정렬한 다이어그램입니다. 표시된 대로 **PartitionGameLogsPipeline**의 출력이 EnrichGameLogsPipeline의 입력으로 전달되고 **EnrichGameLogsPipeline**의 출력이 **AnalyzeMarketingCampaignPipeline**으로 전달됩니다. 제목을 두 번 클릭하면 블레이드가 나타내는 아티팩트에 대한 자세한 내용이 표시됩니다.

	![다이어그램 뷰][image-data-factory-tutorial-diagram-view]

3. **AnalyzeMarketingCampaignPipeline**을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기**를 클릭합니다. 작업에 대한 입력 및 출력 데이터 집합과 함께 파이프라인의 모든 작업이 표시됩니다.
 
	![파이프라인 열기](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	왼쪽 위의 이동 경로에서 **데이터 팩터리**를 클릭하여 모든 파이프라인이 포함된 다이어그램 뷰로 돌아갑니다.


**축하합니다.** Azure Data Factory, 연결된 서비스, 파이프라인, 테이블을 만들었고 워크플로를 시작했습니다.


## <a name="MainStep6"></a> 6단계: 파이프라인 및 데이터 조각 모니터링 

1.	**LogProcessingFactory**에 대한 **데이터 팩터리** 블레이드가 없는 경우, 다음 중 하나를 수행할 수 있습니다.
	1.	**시작 보드**에서 **LogProcessingFactory**를 클릭합니다. 데이터 팩터리를 만들 때 **시작 보드에 추가** 옵션이 자동으로 선택되어 있었습니다.

		![모니터링 시작 보드][image-data-factory-monitoring-startboard]

	2. **찾아보기**를 클릭하고 **찾아보기** 블레이드에서 **데이터 팩터리**를 선택한 후 **데이터 팩터리** 블레이드에서 **LogProcessingFactory**를 선택합니다.

		![모니터링 찾아보기 데이터 팩터리][image-data-factory-monitoring-browse-datafactories]
2. 여러 가지 방법으로 데이터 팩터리를 모니터링할 수 있습니다. 파이프라인이나 데이터 집합으로 시작할 수 있습니다. 우선 파이프라인부터 자세히 다루겠습니다. 
3.	**데이터 팩터리** 블레이드에서 **파이프라인**을 클릭합니다. 
4.	파이프라인 블레이드에서 **PartitionGameLogsPipeline**을 클릭합니다. 
5.	**PartitionGameLogsPipeline**의 **파이프라인** 블레이드에 이 파이프라인이 **RawGameEventsTable** 데이터 집합을 사용한다고 표시됩니다. **RawGameEventsTable**을 클릭합니다.

	![사용 및 생성된 파이프라인][image-data-factory-monitoring-pipeline-consumed-produced]

6. **RawGameEventsTable**에 대한 테이블 블레이드에서 모든 슬라이스를 참조하세요. 다음 스크린샷에서는 모든 조각이 **Ready** 상태이고 문제 조각이 없습니다. 즉, 데이터를 처리할 준비가 되었습니다.

	![RawGameEventsTable 테이블 블레이드][image-data-factory-monitoring-raw-game-events-table]

	**Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.

	-  **Set-AzureRmDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
	-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.
 
	목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.
	
	조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.

	![조각 시간별 데이터 조각][DataSlicesBySliceTime]
 
7. 이제 **PartiionGameLogsPipeline**의 **파이프라인** 블레이드에서 **Produced**를 클릭합니다.
8. 이 파이프라인에서 생성하는 데이터 집합 목록이 표시됩니다. 
9. **Produced datasets(생성된 데이터 집합)** 블레이드에서 **PartitionedGameEvents** 테이블을 클릭합니다. 
10.	모든 조각의 **상태**가 **Ready**로 설정되어 있는지 확인합니다. 
11.	**Ready** 상태인 조각 중 하나를 클릭하여 해당 조각에 대한 **데이터 조각** 블레이드를 확인합니다.

	![RawGameEventsTable 데이터 조각 블레이드][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	오류가 발생한 경우 여기에 **Failed ** 상태가 표시됩니다. 두 조각의 상태가 모두 **Ready**이거나 **PendingValidation**일 수 있으며, 이는 조각이 얼마나 빨리 처리되는지에 따라 다릅니다.

	조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.
 
	조각의 가능한 모든 상태에 대해 알아보려면 [Azure 데이터 팩터리 개발자 참조][developer-reference](영문)를 참조하세요.

12.	**데이터 조각** 블레이드의 **작업 실행** 목록에서 실행을 클릭합니다. 해당 조각의 Activity Run 블레이드가 표시됩니다. 다음과 같은 **작업 실행 세부 정보** 블레이드가 표시됩니다.

	![작업 실행 세부 정보 블레이드][image-data-factory-monitoring-activity-run-details]

13.	**다운로드**를 클릭하여 파일을 다운로드합니다. 이 화면은 HDInsight 처리의 오류를 해결할 때 특히 유용합니다.
	 
	
모든 파이프라인의 실행이 완료되면 **MarketingCampaigns** Azure SQL 데이터베이스에서 **MarketingCampaignEffectivenessTable**을 조사하여 결과를 볼 수 있습니다.

**축하합니다.** 이제 워크플로를 모니터링하고 문제를 해결할 수 있습니다. 지금까지 Azure Data Factory를 사용하여 데이터를 처리하고 분석 결과를 얻는 방법을 배웠습니다.

## 온-프레미스 데이터를 사용하도록 자습서 확장
이 문서의 연습에서 로그 처리 시나리오의 마지막 단계에 마케팅 캠페인 효과 출력이 Azure SQL 데이터베이스에 복사되었습니다. 이 데이터를 조직 내에서 분석하기 위해 온-프레미스 SQL Server로 이동할 수도 있습니다.
 
Azure Blob에서 온-프레미스 SQL Server로 마케팅 캠페인 효과 데이터를 복사하려면 이 문서의 연습에서 소개한 온-프레미스 연결된 서비스, 테이블 및 파이프라인을 추가로 만들어야 합니다.

[연습: 온-프레미스 데이터 원본 사용][tutorial-onpremises]을 실습하여 마케팅 캠페인 효과 데이터를 온-프레미스 SQL Server 데이터베이스로 복사하는 파이프라인을 만드는 방법을 알아봅니다.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


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


[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0128_2016-->
<properties 
	pageTitle="Azure Data Factory를 사용하여 로그 파일 이동 및 처리(Azure PowerShell)" 
	description="이 고급 자습서에서는 거의 실제 시나리오를 설명하고 Azure Data Factory 서비스 및 Azure PowerShell을 사용하여 시나리오를 구현합니다." 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# 자습서: 데이터 팩터리 [PowerShell]을 사용하여 로그 파일 이동 및 처리
이 문서에서는 Azure Data Factory를 사용하여 로그 파일의 데이터를 유용한 정보로 변환하는 정식 로그 처리 시나리오로 구성된 완전한 연습을 제공합니다.

> [AZURE.IMPORTANT]이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 데이터 팩터리 cmdlet에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조][cmdlet-reference](영문)를 참조하세요.
>    
> Azure PowerShell 1.0 Preview를 사용하는 경우 [여기](https://msdn.microsoft.com/library/dn820234.aspx)에서 설명된 cmdlet을 사용해야 합니다. 예를 들어 New-AzureDataFactory를 사용하는 대신 New-AzureRMDataFactory를 사용합니다.

## 시나리오
Contoso는 게임 콘솔, 핸드헬드 장치, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 이러한 각 게임은 많은 로그를 생성합니다. Contoso는 이러한 게임에서 생성되는 로그를 수집 및 분석하여 사용량 정보를 얻고, 업셀 및 교차 판매 기회를 포착하고, 탁월한 새 기능을 개발하는 등을 통해 비즈니스를 개선하고 고객에게 더 나은 환경을 제공하려는 목표가 있습니다.
 
이 연습에서는 샘플 로그를 수집하고, 참조 데이터를 사용하여 이 로그를 처리 및 보강하고, 데이터를 변환하여 Contoso가 최근 시작한 마케팅 캠페인의 효과를 평가합니다.

## 자습서 수행을 위한 준비
1.	[Azure 데이터 팩터리 소개][adfintroduction]를 읽고 Azure 데이터 팩터리에 대한 개요를 확인하고 최상위 개념을 이해합니다.
2.	이 자습서를 수행하려면 Azure 구독이 있어야 합니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하십시오.
3.	컴퓨터에 [Azure PowerShell][download-azure-powershell]을 다운로드하고 설치해야 합니다. 
2.	**(권장)** [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 간단한 자습서를 검토하고 연습하여 포털 및 cmdlet을 익힙니다.
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
	- **Add-AzureAccount**를 실행하고 Azure Preview 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 미리 보기 포털에서 사용한 것과 같아야 합니다.
	

## 개요
종단 간 워크플로는 아래와 같습니다. ![자습서 종단 간 흐름][image-data-factory-tutorial-end-to-end-flow]

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


6. [6단계: 파이프라인 및 데이터 조각 모니터링](#MainStep6) 이 단계에서는 Azure 포털을 사용하여 파이프라인, 테이블 및 데이터 조각을 모니터링합니다.

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
   
2. 로컬 컴퓨터가 Azure SQL 데이터베이스에 액세스할 수 있는지 확인합니다. 액세스할 수 있게 하려면 **Azure 관리 포털**이나 master 데이터베이스의 **sp\_set\_firewall\_rule**을 사용하여 컴퓨터의 IP 주소에 대한 방화벽 규칙을 만듭니다. 이 변경 내용이 적용되려면 최대 5분까지 걸릴 수 있습니다. [Azure SQL에 대한 방화벽 규칙 설정][azure-sql-firewall]을 참조하세요.
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

1.	[Azure Preview 포털][azure-preview-portal]에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭하고 **새로 만들기** 블레이드에서 **데이터 팩터리**를 클릭합니다. 

	![새로 만들기->DataFactory][image-data-factory-new-datafactory-menu]
	
	**새로 만들기** 블레이드에 **데이터 팩터리**가 표시되지 않으면 아래로 스크롤합니다.
	
5. **새 데이터 팩터리** 블레이드에서 **LogProcessingFactory**를 **이름**으로 입력합니다.

	![데이터 팩터리 블레이드][image-data-factory-tutorial-new-datafactory-blade]

6. **ADF**라는 Azure 리소스 그룹을 아직 만들지 않은 경우 다음을 수행합니다.
	1. **리소스 그룹 이름**을 클릭하고 **새 리소스 그룹 만들기**를 클릭합니다.
	
		![리소스 그룹 블레이드][image-data-factory-tutorial-resourcegroup-blade]
	2. **리소스 그룹 만들기** 블레이드에서 리소스 그룹 이름으로 **ADF**를 입력하고 **확인**을 클릭합니다.
	
		![리소스 그룹 만들기][image-data-factory-tutorial-create-resourcegroup]
7. **리소스 그룹 이름**으로 **ADF**를 선택합니다.  
8.	**새 데이터 팩터리** 블레이드에서는 **시작 보드에 추가**가 기본적으로 선택되어 있습니다. 선택된 경우 데이터 팩터리에 대한 링크가 시작 보드(Azure 미리 보기 포털에 로그인할 때 표시되는 페이지)에 추가됩니다.

	![데이터 팩터리 만들기 블레이드][image-data-factory-tutorial-create-datafactory]

9.	**새 데이터 팩터리** 블레이드에서 **만들기**를 클릭하여 데이터 팩터리를 만듭니다.
10.	데이터 팩터리가 만들어지면 **LogProcessingFactory**라는 제목의 **데이터 팩터리** 블레이드가 표시됩니다.

	![데이터 팩터리 홈페이지][image-data-factory-tutorial-datafactory-homepage]

	
	이 블레이드가 표시되지 않는 경우 다음 중 하나를 수행합니다.

	- **시작 보드**(홈페이지)에서 **LogProcessingFactory**를 클릭합니다.
	- 왼쪽에서 **찾아보기**를 클릭하고, **모두**, **데이터 팩터리**를 차례로 클릭한 다음 데이터 팩터리를 클릭합니다.
 
	Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “LogProcessingFactory”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다(예: yournameLogProcessingFactory). 이 자습서의 단계를 수행하는 동안 LogProcessingFactory 대신 이 이름을 사용합니다.
 
## <a name="MainStep3"></a> 3단계: 연결된 서비스 만들기

> [AZURE.NOTE]이 문서에서는 Azure PowerShell을 사용하여 연결된 서비스, 테이블 및 파이프라인을 만듭니다. Azure 포털, 특히 데이터 팩터리 편집기를 사용하여 이 자습서를 수행하려는 경우 [데이터 팩터리 편집기를 사용한 자습서][adftutorial-using-editor]를 참조하세요.

이 단계에서는 연결된 서비스 StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService 및 HDInsightLinkedService를 만듭니다.


1.	**LogProcessingFactory** 블레이드에서 **연결된 서비스** 타일을 클릭합니다.

	![연결된 서비스 타일][image-data-factory-tutorial-linkedservice-tile]

2. **연결된 서비스** 블레이드의 명령 모음에서 **+ 데이터 저장소**를 클릭합니다.

	![연결된 서비스 - 저장소 추가][image-data-factory-tutorial-linkedservices-add-datstore]

3. **새 데이터 저장소** 블레이드에서 **StorageLinkedService**를 **이름**으로 입력하고 **유형(필요한 설정)**을 클릭한 다음 **Azure 저장소 계정**을 선택합니다.

	![데이터 저장소 유형 - Azure 저장소][image-data-factory-tutorial-datastoretype-azurestorage]

4. **새 데이터 저장소** 블레이드에 2개의 새 필드 **계정 이름** 및 **계정 키**가 표시됩니다. **Azure 저장소 계정**의 계정 이름과 계정 키를 입력합니다.

	![Azure 저장소 설정][image-data-factory-tutorial-azurestorage-settings]

	Azure 저장소 계정의 계정 이름과 계정 키는 아래와 같이 포털에서 얻을 수 있습니다.

	![저장소 키][image-data-factory-tutorial-storage-key]
  
5. 새 데이터 저장 블레이드에서 **확인**을 클릭한 후, **연결된 서비스** 블레이드의 **데이터 저장소** 목록에 **StorageLinkedService**가 표시되어야 합니다. 왼쪽의 **알림** 허브에서 메시지를 확인합니다.

	![저장소와 연결된 서비스 블레이드][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. **2-5단계**를 반복하여 **HDInsightStorageLinkedService**라는 다른 연결된 서비스를 만듭니다. 이 서비스는 HDInsight 클러스터에서 사용하는 저장소입니다.
7. 연결된 서비스 블레이드의 목록에 **StorageLinkedService** 및 **HDInsightStorageLinkedService**가 모두 표시되는지 확인합니다.
8. **연결된 서비스** 블레이드의 명령 모음에서 **데이터 저장소 추가(+)**를 클릭합니다.
9. **AzureSqlLinkedService**를 이름으로 입력합니다.
10. **유형(필요한 설정)**을 클릭하고 **Azure SQL 데이터베이스**를 선택합니다.
11. 이제 **새 데이터 저장소** 블레이드에 다음과 같은 추가 필드가 표시됩니다. Azure SQL 데이터베이스 **서버**의 이름, **데이터베이스** 이름, **사용자 이름** 및 **암호**를 입력하고 **확인**을 클릭합니다.
	1. **MarketingCampaigns**를 **데이터베이스**로 입력합니다. 이 Azure SQL 데이터베이스는 1단계에서 실행한 스크립트에 의해 생성되었습니다. 이 데이터베이스가 스크립트에 의해 실제로 생성되었는지 확인해야 합니다(오류가 발생한 경우).
		
 		![Azure SQL 설정][image-data-factory-tutorial-azuresql-settings]

		Azure 관리 포털에서 이러한 값을 가져오려면 MarketingCampaigns 데이터베이스에 대한 SQL 데이터베이스 연결 문자열 보기를 클릭합니다.

		![Azure SQL 데이터베이스 연결 문자열][image-data-factory-tutorial-azuresql-database-connection-string]

12. 다음 3개의 데이터 저장소가 모두 만들어져 표시되는지 확인합니다. **StorageLinkedService**, **HDInsightStorageLinkedService** 및 **AzureSqlLinkedService**.
13. 다른 연결된 서비스를 만들어야 하지만 하나는 계산 서비스, 특히 **Azure HDInsight 클러스터**여야 합니다. 아직은 포털에서 계산 연결된 서비스를 만들 수 없습니다. 따라서 이 연결된 서비스를 만들려면 Azure PowerShell을 사용해야 합니다. 
14. **Azure PowerShell**이 이미 열려있거나 시작되어 있는 경우 **Azure PowerShell**로 전환합니다. Azure PowerShell을 닫았다가 다시 연 경우 다음 명령을 실행해야 합니다. 
	- **Add-AzureAccount**를 실행하고 Azure Preview 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 미리 보기 포털에서 사용한 것과 같아야 합니다. 
15. **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 이 모드에서 사용할 수 있습니다.

		Switch-AzureMode AzureResourceManager

16. **C:\\ADFWalkthrough** 또는 파일의 압축을 푼 폴더의 **LinkedServices** 하위 폴더로 이동합니다.
17. 유형이 **HDInsightOnDemandLinkedService**로 설정된 좋아하는 편집기 및 알림에서 **HDInsightLinkedService.json**을 엽니다.


	Azure Data Factory 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다.
	
	HDInsightLinkedService는 주문형 HDInsight 클러스터를 데이터 팩터리에 연결합니다. 사용자 고유의 HDInsight 클러스터를 사용하려면 HDInsightLinkedService.json 파일의 Properties 섹션을 아래와 같이 업데이트합니다(clustername, username 및 password를 적절한 값으로 대체).
	
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
19. 다음과 같이 **New-AzureDataFactoryLinkedService** cmdlet을 사용하여 연결된 서비스를 만듭니다. 저장소 계정으로 시작합니다.

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	ResourceGroupName, DataFactoryName 또는 LinkedService 이름에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 파일을 찾을 수 없는 경우 연결된 서비스 JSON 파일의 전체 파일 경로 제공합니다.
20. 아래와 같이 **연결된 서비스** 블레이드에 4개의 연결된 서비스가 모두 표시됩니다. 연결된 서비스 블레이드가 열려 있지 않은 경우 **LogProcessingFactory**의 **데이터 팩터리** 페이지에서 연결된 서비스를 클릭합니다. 연결된 서비스 블레이드가 새로 고쳐질 때까지 몇 초 정도 걸릴 수 있습니다.

	![연결된 서비스 모두][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> 4단계: 테이블 만들기 
이 단계에서는 다음 테이블을 만듭니다.

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![자습서 종단 간 흐름][image-data-factory-tutorial-end-to-end-flow]
 
위 그림에서는 가운데 행에는 파이프라인을, 위쪽과 아래쪽 행에는 테이블을 표시합니다.

이 릴리스에서는 Azure 포털을 통해 데이터 집합/테이블을 만들 수 없으므로 Azure PowerShell을 사용하여 테이블을 만들어야 합니다.

### 테이블을 만들려면

1.	Azure PowerShell에서 샘플의 압축을 푼 위치의 **Tables** 폴더(**C:\\ADFWalkthrough\\Tables**)로 이동합니다.
2.	다음과 같이 **New-AzureDataFactoryDataset** cmdlet을 사용하여 **RawGameEventsTable**.json에 대한 데이터 집합을 만듭니다.	


		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	ResourceGroupName 및 DataFactoryName에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 cmdlet에서 파일을 찾을 수 없는 경우 테이블 JSON 파일의 전체 파일 경로 제공합니다.

3. 이전 단계를 반복하여 다음 테이블을 만듭니다.
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. **Azure Preview 포털**에서 **LogProcessingFactory**의 **데이터 팩터리** 블레이드에서 **데이터 집합**을 클릭하고 데이터 집합이 모두 표시되는지 확인합니다(테이블은 사각형 데이터 집합).

	![데이터 집합 모두][image-data-factory-tutorial-datasets-all]

	Azure PowerShell에서 다음 명령을 사용할 수도 있습니다.
			
		Get-AzureDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> 5단계: 파이프라인 만들기 및 예약
이 단계에서는 PartitionGameLogsPipeline, EnrichGameLogsPipeline 및 AnalyzeMarketingCampaignPipeline 파이프라인을 만듭니다.

1. **Windows 탐색기**에서 **C:\\ADFWalkthrough** 폴더(또는 샘플의 압축을 푼 위치)의 **Pipelines** 하위 폴더로 이동합니다.
2.	즐겨 사용하는 편집기에서 **PartitionGameLogsPipeline.json**을 열고 강조 표시된 내용을 데이터 저장소 계정 정보로 바꾼 다음 파일을 저장합니다.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 단계를 반복하여 다음 파이프라인을 만듭니다.
	1. **EnrichGameLogsPipeline**.json(3회)
	2. **AnalyzeMarketingCampaignPipeline**.json(3회)

	**중요:** <storageaccountname>을 모두 저장소 계정 이름으로 바꾸어야 합니다.
 
4.  **Azure PowerShell**에서 **C:\\ADFWalkthrough** 폴더(또는 샘플의 압축을 푼 위치)의 **Pipelines** 하위 폴더로 이동합니다.
5.  다음과 같이 **New-AzureDataFactoryPipeline** cmdlet을 사용하여 **PartitionGameLogspeline**.json에 대한 파이프라인을 만듭니다.	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	ResourceGroupName, DataFactoryName 또는 Pipeline 이름에 다른 이름을 사용하는 경우 위의 cmdlet에서 이 이름을 참조합니다. 또한 파이프라인 JSON 파일의 전체 파일 경로 제공합니다.
6. 이전 단계를 반복하여 다음 파이프라인을 만듭니다.
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. **Get-AzureDataFactoryPipeline** cmdlet을 사용하여 파이프라인 목록을 가져옵니다.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. 파이프라인이 만들어지면 데이터 처리가 수행되는 기간을 지정할 수 있습니다. 파이프라인의 활성 기간을 지정하면 각 ADF 테이블에 대해 정의된 가용성 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.

파이프라인의 활성 기간을 지정하려면 Set-AzureDataFactoryPipelineActivePeriod cmdlet을 사용할 수 있습니다. 이 연습에서는 샘플 데이터가 5월 1일에서 5월 5일까지입니다. 2014-05-01을 StartDateTime으로 사용합니다. EndDateTime은 선택 사항입니다.
			
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

11. **Azure Preview 포털**에서 **LogProcessingFactory**의 **데이터 팩터리** 블레이드에서 파이프라인 이름이 아니라 **파이프라인** 타일을 클릭하면 만든 파이프라인이 표시됩니다.

	![모든 파이프라인][image-data-factory-tutorial-pipelines-all]

12. **LogProcessingFactory**의 **데이터 팩터리** 블레이드에서 **다이어그램**을 클릭합니다.

	![다이어그램 링크][image-data-factory-tutorial-diagram-link]

13. 다이어그램을 다시 정렬할 수 있으며, 다음은 맨 위에 직접 입력, 맨 아래에 출력이 표시되도록 다시 정렬한 다이어그램입니다. 표시된 대로 **PartitionGameLogsPipeline**의 출력이 EnrichGameLogsPipeline의 입력으로 전달되고 **EnrichGameLogsPipeline**의 출력이 **AnalyzeMarketingCampaignPipeline**으로 전달됩니다. 제목을 두 번 클릭하면 블레이드가 나타내는 아티팩트에 대한 자세한 내용이 표시됩니다.

	![다이어그램 뷰][image-data-factory-tutorial-diagram-view]

	**축하합니다.** Azure Data Factory, 연결된 서비스, 파이프라인, 테이블을 만들었고 워크플로를 시작했습니다.


## <a name="MainStep6"></a> 6단계: 파이프라인 및 데이터 조각 모니터링 

1.	LogProcessingFactory에 대한 데이터 팩터리 블레이드가 열려 있지 않은 경우 다음 중 하나를 수행할 수 있습니다.
	1.	**시작 보드**에서 **LogProcessingFactory**를 클릭합니다. 데이터 팩터리를 만들 때 **시작 보드에 추가** 옵션이 자동으로 선택되어 있었습니다.

		![모니터링 시작 보드][image-data-factory-monitoring-startboard]

	2. **찾아보기** 허브를 클릭하고 **모두**를 클릭합니다.
	 	
		![모니터링 허브 모두][image-data-factory-monitoring-hub-everything]

		**찾아보기** 블레이드에서 **데이터 팩터리**를 선택하고 **데이터 팩터리** 블레이드에서 **LogProcessingFactory**를 선택합니다.

		![모니터링 찾아보기 데이터 팩터리][image-data-factory-monitoring-browse-datafactories]
2. 여러 가지 방법으로 데이터 팩터리를 모니터링할 수 있습니다. 파이프라인이나 데이터 집합으로 시작할 수 있습니다. 우선 파이프라인부터 자세히 다루겠습니다. 
3.	**데이터 팩터리** 블레이드에서 **파이프라인**을 클릭합니다. 
4.	파이프라인 블레이드에서 **PartitionGameLogsPipeline**을 클릭합니다. 
5.	**PartitionGameLogsPipeline**의 **파이프라인** 블레이드에 이 파이프라인이 **RawGameEventsTable** 데이터 집합을 사용한다고 표시됩니다. **RawGameEventsTable**을 클릭합니다.

	![사용 및 생성된 파이프라인][image-data-factory-monitoring-pipeline-consumed-produced]

6. **RawGameEventsTable**에 대한 테이블 블레이드에서 모든 슬라이스를 참조하세요. 다음 스크린샷에서는 모든 조각이 **Ready** 상태이고 문제 조각이 없습니다. 즉, 데이터를 처리할 준비가 되었습니다.

	![RawGameEventsTable 테이블 블레이드][image-data-factory-monitoring-raw-game-events-table]
 
7. 이제 **PartiionGameLogsPipeline**의 **파이프라인** 블레이드에서 **Produced**를 클릭합니다.
8. 이 파이프라인에서 생성하는 데이터 집합 목록이 표시됩니다. 
9. **Produced datasets(생성된 데이터 집합)** 블레이드에서 **PartitionedGameEvents** 테이블을 클릭합니다. 
10.	모든 조각의 **상태**가 **Ready**로 설정되어 있는지 확인합니다. 
11.	**Ready** 상태인 조각 중 하나를 클릭하여 해당 조각에 대한 **데이터 조각** 블레이드를 확인합니다.

	![RawGameEventsTable 데이터 조각 블레이드][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	오류가 발생한 경우 여기에 **Failed ** 상태가 표시됩니다. 두 조각의 상태가 모두 **Ready**이거나 **PendingValidation**일 수 있으며, 이는 조각이 얼마나 빨리 처리되는지에 따라 다릅니다.
 
	조각의 가능한 모든 상태에 대해 알아보려면 [Azure 데이터 팩터리 개발자 참조][developer-reference](영문)를 참조하세요.

12.	**데이터 조각** 블레이드의 **작업 실행** 목록에서 실행을 클릭합니다. 해당 조각의 Activity Run 블레이드가 표시됩니다. 다음과 같은 **작업 실행 세부 정보** 블레이드가 표시됩니다.

	![작업 실행 세부 정보 블레이드][image-data-factory-monitoring-activity-run-details]

13.	**다운로드**를 클릭하여 파일을 다운로드합니다. 이 화면은 HDInsight 처리의 오류를 해결할 때 특히 유용합니다.
	 
	
모든 파이프라인의 실행이 완료되면 **MarketingCampaigns** Azure SQL 데이터베이스에서 **MarketingCampaignEffectivenessTable**을 조사하여 결과를 볼 수 있습니다.

**축하합니다.** 이제 워크플로를 모니터링하고 문제를 해결할 수 있습니다. 지금까지 Azure Data Factory를 사용하여 데이터를 처리하고 분석 결과를 얻는 방법을 배웠습니다.

## 온-프레미스 데이터를 사용하도록 자습서 확장
이 문서의 연습에서 로그 처리 시나리오의 마지막 단계에 마케팅 캠페인 효과 출력이 Azure SQL 데이터베이스에 복사되었습니다. 이 데이터를 조직 내에서 분석하기 위해 온-프레미스 SQL Server로 이동할 수도 있습니다.
 
Azure Blob에서 온-프레미스 SQL Server로 마케팅 캠페인 효과 데이터를 복사하려면 이 문서의 연습에서 소개한 온-프레미스 연결된 서비스, 테이블 및 파이프라인을 추가로 만들어야 합니다.

[연습: 온-프레미스 데이터 원본 사용][tutorial-onpremises-using-powershell]을 실습하여 마케팅 캠페인 효과 데이터를 온-프레미스 SQL Server 데이터베이스로 복사하는 파이프라인을 만드는 방법을 알아봅니다.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

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

<!---HONumber=Nov15_HO2-->
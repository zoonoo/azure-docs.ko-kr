<properties 
	pageTitle="데이터 팩터리 - 릴리스 정보 | Azure" 
	description="데이터 팩터리 릴리스 정보" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 릴리스 정보

## 데이터 팩터리의 04/10/2015 릴리스에 대 한 정보
나타납니다는 **조각 최근에 업데이트** 및 **최근에 조각 실패 한** 에 나열는 **테이블** 블레이드 이제 합니다. 이러한 목록은 조각의 업데이트 시간으로 정렬 됩니다. 다음과 같은 상황에서 조각의 업데이트 시간이 변경 됩니다.

-  수동으로 업데이트할 있습니다 조각의 상태, 예를 사용 하 여는 **집합 AzureDataFactorySliceStatus** (또는)를 클릭 하 여 **실행** 에 **조각** 블레이드는 조각에 대 한 합니다.
-  실행으로 인해 상태를 변경 하는 조각 (예: 시작을 실행 하는, 종료 하 고 실패 한 실행, 실행을 종료 하 고 성공, 등).

목록 또는 의 제목을 클릭 **... (타원)** 더 큰 조각 목록을 확인 합니다. 클릭 하 여 **필터** 슬라이스를 필터링 하려면 도구 모음입니다.
 
기준으로 정렬 slice 번 클릭 하 여 조각에서 볼 수 있습니다 **(시간별 slice) 데이터 조각이** 바둑판식으로 배열 합니다. 해당 컬렉션에서 조각 시간 조각으로 정렬 됩니다. 예를들어 매시간 일정 인 경우 조각이 것:-4/4/2015 진행중 2015 4/4/4-오후 5 시 성공-2015 4/4/3 오후 pm 실패

하지만 경우 다시 실행 되는 이전 조각 것은 표시 되지이 목록 맨 위의 대부분의 사용자가에 더 관심이 어떤 경우에 합니다.

## 데이터 팩터리의 3/31/2015 릴리스에 대 한 정보
- 업데이트 **데이터 관리 게이트웨이** 설치 패키지에 게시 되었음을 [Microsoft 다운로드 센터][adf-gateway-download].
- 복사 **온-프레미스 파일 Azure blob에 대 한 시스템** 현재 지원 됩니다. 자세한 내용은 다음 항목을 참조 하십시오.
	-  [온-프레미스 파일 시스템에 서비스 연결](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [표에 JSON OnPremisesFileSystemLocation 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [원본 및 싱크를 지원](https://msdn.microsoft.com/library/dn894007.aspx). 업데이트 된 복사본 매트릭스를 참조 하 고 **FileSystemSource** 속성입니다. 
-  복사 **Azure blob에 온-프레미스 Oracle 데이터베이스** 현재 지원 됩니다. 자세한 정보에 대 한 다음 항목을 참조 하십시오. 
	-  [온-프레미스 Oracle 연결 서비스](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [표에 JSON OnPremisesOracleTableLocation 속성](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [원본 및 싱크를 지원](https://msdn.microsoft.com/library/dn894007.aspx). 업데이트 된 복사본 매트릭스를 참조 하 고 **OracleSource** 속성입니다.
-  Azure Blob에서 텍스트 파일에 대 한 인코딩을 지정할 수 있습니다. 새 참조 [encodingName 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- SQL 싱크를 복사 하는 경우 추가 매개 변수가 있는 저장된 프로시저를 호출할 수 있습니다. 참조 [SQL 싱크에 대해 저장된 프로시저를 호출할][adf-copy-advanced] 대 한 자세한 내용은 합니다.   

게시 blob 참조: [Azure 데이터 팩터리 업데이트-새 데이터 저장소](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) 예제를 비롯 한 추가 정보에 대 한 합니다.

## 데이터 팩터리의 2/27/2015 릴리스에 대 한 정보

### 새롭게 향상된 기능
- **Azure 데이터 팩터리 편집기**. Azure 미리 보기 포털의 일부인 데이터 팩터리 편집기, 만들기, 편집 및 연결 된 서비스, 데이터 집합 및 파이프라인을 정의 하는 JSON 파일을 배포할 수 있습니다. 편집기의 주요 목표는 신속 하 고 간단한 사용자 인터페이스 (UI)에 설치 하려면 Azure PowerShell 및 램프 위로 PowerShell cmdlet을 사용 하지 않고도 Azure 데이터 팩터리 아티팩트를 만들려고 하면를 제공 하는 것입니다. 참조는 [Azure 데이터 팩터리 편집기-A Light 가중치 웹 편집기][adf-editor-blog] 간략 한 개요와 비디오 데이터 팩터리 편집기에 대 한 블로그 게시물. 편집기의 자세한 개요에 대 한 참조는 [데이터 팩터리 편집기][adf-editor] 문서입니다.          

### 변경 내용

## 데이터 팩터리의 1/26/2015 릴리스에 대 한 정보 ##

### 변경 내용
- 업데이트 **데이터 관리 게이트웨이** 설치 패키지에 게시 되었음을 [Microsoft 다운로드 센터][adf-gateway-download]. 이 릴리스에서 부터는이 다운로드 위치에서 Azure 데이터 팩터리로 사용 하 여 최신 데이터 관리 게이트웨이 찾을 수 있습니다. 이 설치 패키지는 Office 365 서비스에 대 한 Azure 데이터 팩터리와 Power BI를 모두 사용 됩니다. 두 서비스를 사용 하는 경우 note 데이터 팩터리 및 Power BI에 대 한 게이트웨이 서로 다른 컴퓨터에 설치 및 데이터 팩터리 또는 Power BI 설명서의 지침에 따라 다르게 구성 될 해야 합니다.
-  **복사 활동** 이제 지원 온-프레미스 SQL Server 데이터베이스 및 Azure SQL 데이터베이스 간에 데이터를 복사 합니다. 참조 [복사 활동][adf-copy-activity] 대 한 자세한 내용은 및 [GitHub][adf-github-samples] 에 대 한 JSON 예제.
- **SqlSink** 새 속성을 원하는: **WriteBatchTimeout**. 이 속성 작업 시간이 초과 되기 전에 완료 하려면 일괄 처리 삽입 작업에 대 한 대기 시간을 구성할 수 있는 유연성을 제공 합니다. 하이브리드 복사 (온-프레미스 데이터 원본과 클라우드 데이터 소스와 관련 된 복사 작업)에 대 한이 속성을 사용 하 여 1.4 이상 버전의 게이트웨이 있어야 합니다. 
- **SQL Server 연결 서비스** 이제 지원 **Windows 인증**. 
	- 포털을 사용 하 여 서비스를 연결 SQL 서버를 만드는 경우에 Windows 인증을 사용 하 고 적절 한 자격 증명을 설정 하려면 지금 선택할 수 있습니다. 그러려면 버전 1.4 이상의 게이트웨이 사용할 수 있습니다. 
	- Azure PowerShell을 사용 하 여 서비스를 연결 SQL 서버를 만드는 경우에 일반 텍스트로 연결 정보를 지정 하거나 암호화 된 연결 정보를 사용 하 여 업데이트 수 있습니다 [새로 AzureDataFactoryEncryptValue cmdlet][adf-encrypt-value-cmdlet] 다음 암호화 된 문자열을 사용 하 여 연결 문자열 속성에 연결 된 서비스 JSON 페이로드입니다. 참조 [연결 된 서비스][adf-msdn-linked-services] JSON에서 연결 된 서비스를 정의 하는 방법에 대 한 세부 정보에 대 한 합니다. 암호화 기능이 아직 새로 AzureDataFactoryEncryptValue cmdlet에서 지원 되지 않습니다. 

## 데이터 팩터리의 2014/12/11 릴리스에 대 한 정보 ##

### 새롭게 향상된 기능

- Azure 기계 학습 통합
	- 이 릴리스의 Azure 데이터 팩터리 서비스를 사용 하면 Azure 데이터 팩터리를 사용 하 여 Azure 컴퓨터 학습 (ML)와 통합 **AzureMLLinkedService** 및 **AzureMLBatchScoringActivity**. 참조 [데이터 팩터리 및 Azure 컴퓨터 학습을 사용 하 여 예측 파이프라인을 만들][adf-azure-ml] 대 한 자세한 내용은 합니다. 
- 게이트웨이 버전 상태가 제공됨
	- 현재 설치된 버전보다 최신 버전의 게이트웨이를 사용할 수 있는 경우 Azure 미리 보기 포털 및 Get-AzureDataFactoryGateway cmdlet의 출력에 "NewVersionAvailable" 상태가 표시됩니다. 그러면 포털 경로를 따라 새 설치 파일(.msi)을 다운로드한 다음 실행하여 최신 게이트웨이를 설치할 수 있습니다. 추가 구성은 필요하지 않습니다.

### 변경 내용

- HdInsightOnDemandLinkedService에서 JobsContainer가 제거되었습니다.
	- HDInsightOnDemandLinkedService에 대 한 JSON 정의에서 필요가 없습니다 지정 하려면 **jobsContainer** 더이상 속성입니다. 주문형 연결된 서비스에 대해 지정된 속성이 있는 경우 해당 속성은 무시됩니다. 연결된 서비스에 대한 JSON 정의에서 이 속성을 제거하고 New-AzureDataFactoryLinkedService cmdlet을 사용하여 연결된 서비스 정의를 업데이트할 수 있습니다.
- HDInsightOnDemandLinkedService에 대한 선택적 구성 매개 변수
	- 이 릴리스에는 HDInsightOnDemandLinked(주문형 HDInsight 클러스터)에 대한 몇 가지 선택적 구성 매개 변수 지원이 도입되었습니다. 참조 [ClusterCreateParameters 속성][on-demand-hdi-parameters] 대 한 자세한 내용은 합니다.
- 게이트웨이 위치가 제거됨
	- 포털 또는 PowerShell(New-AzureDataFactoryGateway)을 통해 Azure 데이터 팩터리 게이트웨이를 만들 때 더 이상 게이트웨이의 위치를 지정할 필요가 없습니다. 데이터 팩터리 영역이 상속됩니다. 마찬가지로, JSON을 사용하여 SQL Server 연결된 서비스를 구성할 때 더 이상 "gatewayLocation" 속성이 필요하지 않습니다. 데이터 팩터리 .NET SDK도 이러한 변경 내용을 반영하도록 업데이트되었습니다.
	- 이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 계속 위치 설정을 제공해야 합니다.
 
     

#### 주요 변경 내용
	
- CustomActivity가 DotNetActivity로 변경됨
	- **ICustomActivity** 인터페이스 이름이 **IDotNetActivity**. 데이터 팩터리 NuGet 패키지를 업데이트하고 사용자 지정 작업의 소스 코드에서 ICustomActivity를 IDotNetActivity로 변경해야 합니다.  
	- 사용자 지정 활동에 대 한 JSON 정의에 사용자 지정 활동의 형식에서 변경 해야 **CustomActivity** 를 **DotNetActivity**. 
	-  **CustomActivity** 및 **CustomActivityProperties** 클래스를 바뀐 **DotNetActivity** 및 **DotNetActivityProperties** 동일한 속성 집합을 사용 합니다.

		이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 DotNetActivity 대신 CustomActivity를 계속 사용할 수 있습니다.
    
  		참조 [는 Azure 데이터 팩터리 파이프라인의 사용자 지정 활동을 사용 하 여][adf-custom-activities] 사용자 지정 활동을 만들고는 Azure 데이터 팩터리 파이프라인에서 사용 하는 방법에 대 한 연습에 대 한 합니다.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx

<!---HONumber=GIT-SubDir--> 
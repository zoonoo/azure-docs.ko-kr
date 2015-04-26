<properties title="Azure Data Factory - Release Notes" pageTitle="데이터 팩터리 - 릴리스 정보 | Azure" description="데이터 팩터리 릴리스 정보" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Azure 데이터 팩터리 릴리스 정보

## 데이터 팩터리의 2014/12/11 릴리스 정보 ##

### 새롭게 향상된 기능

- Azure 기계 학습 통합
	- 이 릴리스의 Azure 데이터 팩터리 서비스를 통해 **AzureMLLinkedService** 및 **AzureMLBatchScoringActivity**를 사용하여 Azure 데이터 팩터리를 Azure ML(기계 학습)과 통합할 수 있습니다. 자세한 내용은 [데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기][adf-azure-ml]를 참조하세요. 
- 게이트웨이 버전 상태가 제공됨
	- 현재 설치된 버전보다 최신 버전의 게이트웨이를 사용할 수 있는 경우 Azure 미리 보기 포털 및 Get-AzureDataFactoryGateway cmdlet의 출력에 "NewVersionAvailable" 상태가 표시됩니다. 그러면 포털 경로를 따라 새 설치 파일(.msi)을 다운로드한 다음 실행하여 최신 게이트웨이를 설치할 수 있습니다. 추가 구성은 필요하지 않습니다.

### 변경 내용

- HdInsightOnDemandLinkedService에서 JobsContainer가 제거되었습니다.
	- HDInsightOnDemandLinkedService에 대한 JSON 정의에서 더 이상 **jobsContainer** 속성을 지정할 필요가 없습니다. 주문형 연결된 서비스에 대해 지정된 속성이 있는 경우 해당 속성은 무시됩니다. 연결된 서비스에 대한 JSON 정의에서 이 속성을 제거하고 New-AzureDataFactoryLinkedService cmdlet을 사용하여 연결된 서비스 정의를 업데이트할 수 있습니다.
- HDInsightOnDemandLinkedService에 대한 선택적 구성 매개 변수
	- 이 릴리스에는 HDInsightOnDemandLinked(주문형 HDInsight 클러스터)에 대한 몇 가지 선택적 구성 매개 변수 지원이 도입되었습니다. 자세한 내용은 [ClusterCreateParameters 속성][on-demand-hdi-parameters](영문)을 참조하세요.
- 게이트웨이 위치가 제거됨
	- 포털 또는 PowerShell(New-AzureDataFactoryGateway)을 통해 Azure 데이터 팩터리 게이트웨이를 만들 때 더 이상 게이트웨이의 위치를 지정할 필요가 없습니다. 데이터 팩터리 영역이 상속됩니다. 마찬가지로, JSON을 사용하여 SQL Server 연결된 서비스를 구성할 때 더 이상 "gatewayLocation" 속성이 필요하지 않습니다. 데이터 팩터리 .NET SDK도 이러한 변경 내용을 반영하도록 업데이트되었습니다.
	- 이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 계속 위치 설정을 제공해야 합니다.
 
     

#### 주요 변경 내용
	
- CustomActivity가 DotNetActivity로 변경됨
	- **ICustomActivity** 인터페이스 이름이 **IDotNetActivity**로 변경되었습니다. 데이터 팩터리 NuGet 패키지를 업데이트하고 사용자 지정 작업의 소스 코드에서 ICustomActivity를 IDotNetActivity로 변경해야 합니다.  
	- 사용자 지정 작업에 대한 JSON 정의에서 사용자 지정 작업의 형식을 **CustomActivity**에서 **DotNetActivity**로 변경해야 합니다. 
	- **CustomActivity** 및 **CustomActivityProperties** 클래스 이름이 **DotNetActivity** 및 **DotNetActivityProperties**로 변경되었으며 포함된 속성 집합은 동일합니다.

		이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 DotNetActivity 대신 CustomActivity를 계속 사용할 수 있습니다.
    
  		사용자 지정 작업을 만들어 Azure 데이터 팩터리 파이프라인에서 사용하는 방법에 대한 연습은 [Azure 데이터 팩터리 파이프라인에서 사용자 지정 작업 사용][adf-custom-activities](영문)을 참조하세요.  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/ko-kr/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->

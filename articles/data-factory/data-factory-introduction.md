<properties 
	pageTitle="Azure 데이터 팩터리 소개" 
	description="Azure 데이터 팩터리 서비스를 통해 데이터 처리, 데이터 저장 및 데이터 이동 서비스를 구성하여 신뢰할 수 있는 정보를 생성하는 파이프라인을 만드는 방법을 알아봅니다." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 서비스 소개
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


**Azure 데이터 팩터리** 서비스는 데이터 저장소, 처리 및 이동 서비스를 효율적이고 확장 가능하며 안정적인 데이터 프로덕션 파이프라인으로 구성하기 위한 완전히 관리되는 서비스입니다. 데이터 팩터리 서비스를 통해 다음을 할 수 있습니다.

- 온-프레미스 데이터 저장소, 클라우드 기반 데이터 저장소 및 인터넷 데이터 저장소에서 얻은 데이터를 결합, 집계 및 변환하는 데이터 기반 워크플로(파이프라인)를 구축합니다. 
- 다양한 데이터 원본의 반구조적, 비구조적 및 구조적 데이터를 신뢰할 수 있는 정보로 변환합니다.
- BI(비즈니스 인텔리전스), 분석 도구 및 기타 응용 프로그램을 사용하여 쉽게 사용할 수 있는 데이터를 생성합니다. 
- 복잡한 데이터 처리를 단순한 JSON 스크립팅을 통해 설정합니다.
- Azure Preview 포털을 통해 제공되는 다양한 시각적 환경에서 파이프라인을 한눈에 모니터링하고 관리합니다.  

다음 비디오는 Azure 데이터 팩터리 서비스에 대한 간단한 개요를 제공합니다.


- [비디오: Azure 데이터 팩터리 소개](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## 개요
일반적으로 데이터 통합 프로젝트는 조직 내의 다양한 데이터 원본에서 데이터를 추출하고, 데이터를 EDW(엔터프라이즈 데이터 웨어하우스)의 대상 스키마에 맞게 변환하고, 데이터를 EDW로 로드하는 ETL(추출-변환-로드) 프로세스를 만드는 일을 중심으로 돌아갔습니다. 그런 다음 BI 분석 솔루션에 필요한 정보의 단일 원본으로 EDW에 액세스합니다.

![기존의 ETL][image-data-factory-introduction-traditional-ETL]

오늘날의 기업 데이터 환경은 볼륨, 다양성 및 복잡성이 아주 빠르게 계속 증가하고 있습니다. 온-프레미스와 클라우드에서 생성된 형식과 속도가 다른 데이터로 그 어느 때보다 다양합니다. 데이터 처리는 여러 지역에서 수행해야 하고 오픈 소스 소프트웨어, 상용 솔루션 및 사용자 지정 처리 서비스를 함께 사용하여 이루어지므로 비용이 많이 들고 통합 및 유지 관리가 어렵습니다. 오늘날의 변화하는 빅데이터 환경에 민첩하게 대처해야 하는 상황은 기존 EDW와 최신 정보 생산 시스템에 필요한 기능과 결합할 기회가 될 수 있습니다.

![오늘날의 다양한 처리 환경][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure 데이터 팩터리** 서비스는 기존 EDW와 변화하는 데이터 환경 전반에서 작동하는 구성 플랫폼으로, 기업이 사용 가능한 모든 데이터를 활용하여 데이터를 기반으로 의사를 결정할 수 있도록 지원합니다. 이 서비스는 데이터 처리, 저장소 및 이동 서비스를 정보 생산 파이프라인으로 구성하고 신뢰할 수 있는 데이터 자산을 관리하는 플랫폼을 제공하여 기업이 이 다양성을 활용할 수 있도록 지원합니다.

Azure 데이터 팩터리 서비스를 통해 다음을 할 수 있습니다.

- **다양한 데이터 저장소 및 처리 시스템 쉽게 활용.** 데이터 팩터리 서비스를 통해 SQL Server와 같은 온-프레미스 데이터 및 Azure SQL 데이터베이스, Azure 테이블 및 Blob과 같은 클라우드 데이터 원본을 둘 다 이동하고 처리하는 정보 생산 파이프라인을 만들 수 있습니다. 
- **데이터를 신뢰할 수 있는 정보로 변환.** 데이터 팩터리 서비스는 Hive, Pig 및 C# 처리를 지원할 뿐 아니라 자동 Hadoop(HDInsight) 클러스터 관리, 일시적 오류에 대한 재시도, 구성 가능한 시간 제한 정책, 경고와 같은 주요 처리 기능도 지원합니다.  
- **한곳에서 데이터 파이프라인 모니터링.** 데이터 팩터리 서비스는 저장소, 처리 및 데이터 이동 서비스의 안정적이고 완전한 뷰를 제공합니다. 이 서비스는 신속하게 종단 간 데이터 파이프라인 상태를 평가하고 문제를 파악하여 필요한 경우 수정 동작을 수행할 수 있도록 도와줍니다. 또한 모든 원본에서 데이터 간의 관계 및 데이터 연결을 시각적으로 추적하고 단일 모니터링 대시보드에서 작업 실행, 시스템 상태 및 종속성의 전체 기록을 확인할 수도 있습니다.
- **변환된 데이터에서 다양한 통찰력 얻기** 데이터 팩터리 서비스를 통해 비즈니스 인텔리전스 및 분석 도구와 기타 응용 프로그램에서 사용할 수 있는 신뢰할 수 있는 데이터를 생성하는 데이터 파이프라인을 만들 수 있습니다.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## 응용 프로그램 모델
다음 다이어그램에서는 Azure 데이터 팩터리 서비스에서 지원하는 응용 프로그램 모델을 보여 줍니다.

![응용 프로그램 모델][image-data-factory-application-model]

Azure 데이터 팩터리의 정보 생산 단계는 다음 세 가지입니다.

- **연결 및 수집**. 이 단계에서는 다양한 데이터 원본의 데이터를 데이터 허브로 가져옵니다. 데이터 팩터리의 파이프라인에는 하나 이상의 작업이 포함될 수 있습니다. 데이터 파이프라인에 하나 이상의 **복사** 작업을 사용하여 추가 처리를 위해 원본 데이터 저장소의 데이터를 데이터 허브의 대상 데이터 저장소로 수집합니다. HDInsight 클러스터(계산) 및 연결된 Azure Blob 저장소(저장소)가 함께 데이터 허브인 HDInsight 데이터 허브를 생성합니다. HDInsight 데이터 허브를 사용하려면 HDInsight 클러스터가 데이터를 처리할 수 있도록 모든 원본 데이터를 HDInsight와 연결된 Azure Blob 저장소로 복사합니다. 파이프라인은 HDInsight 클러스터와 같은 데이터 허브의 계산 리소스에서 실행됩니다.      
- **변환 및 보강**. 이 단계에서는 수집된 데이터를 처리합니다. 예를 들어 파이프라인의 **HDInsight 작업**은 Hive/Pig 스크립트를 사용한 변환을 통해 연결된 Azure Blob 저장소에 저장된 데이터를 처리하여 신뢰할 수 있는 정보를 생성할 수 있습니다. 한 파이프라인의 출력 데이터 집합이 동일한 데이터 허브나 다른 데이터 허브에 있는 다른 파이프라인의 입력 데이터 집합이 될 수 있도록 파이프라인을 연결할 수 있습니다(다이어그램 참조).  
- **게시**. 이 단계에서는 데이터를 BI 도구, 분석 도구 및 기타 응용 프로그램에서 사용할 수 있도록 게시합니다. 예를 들어 파이프라인의 복사 작업은 변환 및 보강 단계에서 수행된 처리의 출력 데이터를 비즈니스 인텔리전스 솔루션이 구축될 수 있는 데이터 저장소(예: 온-프레미스 SQL Server)로 복사할 수 있습니다.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##다음 단계
1. [데이터 팩터리 시작][datafactory-getstarted]. 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 Azure 데이터 팩터리를 만드는 방법을 보여 주는 종단 간 자습서를 제공합니다.
2. [자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] 이 문서에서는 Azure 데이터 팩터리를 사용하는 **실제 시나리오**를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 **종단 간 연습**을 제공합니다

## 참고 항목
- [데이터 팩터리-용어][adf-terminology]. 이 문서에서는 Azure 데이터 팩터리 서비스를 사용하여 데이터 팩터리를 만드는 데 사용되는 용어를 소개합니다. 
- [데이터 팩터리-질문과 대답][adf-faq]. 이 문서에서는 자주 묻는 질문과 대답 목록을 제공합니다.
- [Azure 데이터 팩터리를 사용하는 것에 대 한 일반적인 시나리오][adf-common-scenarios]. 이 문서에서는 Azure 데이터 팩터리 서비스 사용에 대한 몇 가지 일반적인 시나리오를 설명합니다. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=July15_HO4-->
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
	ms.date="03/09/2015" 
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


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## 개요
일반적으로 데이터 통합 프로젝트는 조직 내의 다양한 데이터 원본에서 데이터를 추출하고, 데이터를 EDW(엔터프라이즈 데이터 웨어하우스)의 대상 스키마에 맞게 변환하고, 데이터를 EDW로 로드하는 ETL(추출-변환-로드) 프로세스를 만드는 일을 중심으로 돌아갔습니다. 그런 다음 BI 분석 솔루션에 필요한 정보의 단일 원본으로 EDW에 액세스합니다. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

오늘날의 기업 데이터 환경은 볼륨, 다양성 및 복잡성이 아주 빠르게 계속 증가하고 있습니다. 온-프레미스와 클라우드에서 생성된 형식과 속도가 다른 데이터로 그 어느 때보다 다양합니다.  데이터 처리는 여러 지역에서 수행해야 하고 오픈 소스 소프트웨어, 상용 솔루션 및 사용자 지정 처리 서비스를 함께 사용하여 이루어지므로 비용이 많이 들고 통합 및 유지 관리가 어렵습니다.  오늘날의 변화하는 빅데이터 환경에 민첩하게 대처해야 하는 상황은 기존 EDW와 최신 정보 생산 시스템에 필요한 기능과 결합할 기회가 될 수 있습니다.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure 데이터 팩터리** 서비스는 기존 EDW와 변화하는 데이터 환경 전반에서 작동하는 구성 플랫폼으로, 기업이 사용 가능한 모든 데이터를 활용하여 데이터를 기반으로 의사를 결정할 수 있도록 지원합니다. 이 서비스는 데이터 처리, 저장소 및 이동 서비스를 정보 생산 파이프라인으로 구성하고 신뢰할 수 있는 데이터 자산을 관리하는 플랫폼을 제공하여 기업이 이 다양성을 활용할 수 있도록 지원합니다.

Azure 데이터 팩터리 서비스를 통해 다음을 할 수 있습니다.

- **다양한 데이터 저장소 및 처리 시스템 쉽게 활용.** 
데이터 팩터리 서비스를 통해 SQL Server와 같은 온-프레미스 데이터 및 Azure SQL 데이터베이스, Azure 테이블 및 Blob과 같은 클라우드 데이터 원본을 둘 다 이동하고 처리하는 정보 생산 파이프라인을 만들 수 있습니다. 
- **데이터를 신뢰할 수 있는 정보로 변환.** 
데이터 팩터리 서비스는 Hive, Pig 및 C# 처리를 지원할 뿐 아니라 자동 Hadoop(HDInsight) 클러스터 관리, 일시적 오류에 대한 재시도, 구성 가능한 시간 제한 정책, 경고와 같은 주요 처리 기능도 지원합니다.  
- **한곳에서 데이터 파이프라인 모니터링.** 
데이터 팩터리 서비스는 저장소, 처리 및 데이터 이동 서비스의 안정적이고 완전한 뷰를 제공합니다.  이 서비스는 신속하게 종단 간 데이터 파이프라인 상태를 평가하고 문제를 파악하여 필요한 경우 수정 동작을 수행할 수 있도록 도와줍니다. 또한 모든 원본에서 데이터 간의 관계 및 데이터 연결을 시각적으로 추적하고 단일 모니터링 대시보드에서 작업 실행, 시스템 상태 및 종속성의 전체 기록을 확인할 수도 있습니다.
- **변환된 데이터에서 다양한 통찰력 얻기.**
데이터 팩터리 서비스를 통해 비즈니스 인텔리전스 및 분석 도구와 기타 응용 프로그램에서 사용할 수 있는 신뢰할 수 있는 데이터를 생성하는 데이터 파이프라인을 만들 수 있습니다.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## 응용 프로그램 모델
다음 다이어그램에서는 Azure 데이터 팩터리 서비스에서 지원하는 응용 프로그램 모델을 보여 줍니다.

![Application Model][image-data-factory-application-model]

Azure 데이터 팩터리의 정보 생산 단계는 다음 세 가지입니다.

- **연결 및 수집**. 이 단계에서는 다양한 데이터 원본의 데이터를 데이터 허브로 가져옵니다. 데이터 팩터리의 파이프라인에는 하나 이상의 작업이 포함될 수 있습니다. 데이터 파이프라인에 하나 이상의 **복사** 작업을 사용하여 추가 처리를 위해 원본 데이터 저장소의 데이터를 데이터 허브의 대상 데이터 저장소로 수집합니다. HDInsight 클러스터(계산) 및 연결된 Azure Blob 저장소(저장소)가 함께 데이터 허브인 HDInsight 데이터 허브를 생성합니다. HDInsight 데이터 허브를 사용하려면 HDInsight 클러스터가 데이터를 처리할 수 있도록 모든 원본 데이터를 HDInsight와 연결된 Azure Blob 저장소로 복사합니다. 파이프라인은 HDInsight 클러스터와 같은 데이터 허브의 계산 리소스에서 실행됩니다.      
- **변환 및 보강**. 이 단계에서는 수집된 데이터를 처리합니다. 예를 들어 파이프라인의 **HDInsight 작업**은 Hive/Pig 스크립트를 사용한 변환을 통해 연결된 Azure Blob 저장소에 저장된 데이터를 처리하여 신뢰할 수 있는 정보를 생성할 수 있습니다. 한 파이프라인의 출력 데이터 집합이 동일한 데이터 허브나 다른 데이터 허브에 있는 다른 파이프라인의 입력 데이터 집합이 될 수 있도록 파이프라인을 연결할 수 있습니다(다이어그램 참조).  
- **게시**. 이 단계에서는 데이터를 BI 도구, 분석 도구 및 기타 응용 프로그램에서 사용할 수 있도록 게시합니다. 예를 들어 파이프라인의 복사 작업은 변환 및 보강 단계에서 수행된 처리의 출력 데이터를 비즈니스 인텔리전스 솔루션이 구축될 수 있는 데이터 저장소(예: 온-프레미스 SQL Server)로 복사할 수 있습니다.   

<!--

데이터 팩터리를 사용하여 개발자는 파이프라인을 만들 수 있습니다. 파이프라인은 하나 이상의 입력 데이터 집합을 받아 하나 이상의 출력 데이터 집합을 생성하는 데이터 이동 및/또는 처리 작업의 그룹입니다. 파이프라인은 한 번 실행하거나 유연한 범위의 일정(매시간, 매일, 매주 등)에 따라 실행할 수 있습니다. 데이터 집합은 명명된 데이터 뷰입니다. 설명되는 데이터는 단순한 바이트, CSV 파일과 같은 반구조적 데이터에서 테이블 또는 모델에 이르기까지 다양할 수 있습니다.

파이프라인이 데이터 이동 작업(예: 복사 작업)으로 구성될 경우 조직에서 사용하는 모든 데이터 원본(데이터베이스, 파일, SaaS 서비스 등)의 데이터를 데이터 허브로 가져오거나 내보내는 데 주로 사용됩니다.
 
데이터가 **허브**에 있으면, 허브의 계산 서비스에서 호스트하는 **파이프라인**을 사용하여 데이터를 BI 도구, 응용 프로그램, 고객 등이 사용하기 적합한 형식으로 변환합니다.  
  
끝으로, 다이어그램에 표시된 대로 **파이프라인**을 연결하여 한 파이프라인의 출력 **데이터 집합**이 다른 파이프라인의 입력이 되게 합니다.  이렇게 하면 데이터 허브 내에서 실행되거나 여러 허브를 걸치는 **파이프라인**에 복잡한 데이터 흐름을 포함할 수 있습니다.  **파이프라인**을 이런 방식으로 사용하면 최상의 온-프레미스, 클라우드 및 SaaS(Software-as-a-Service) 서비스를 쉽게 관리되는 단일 데이터 팩터리의 관점에서 모두 구축하는 구성 요소를 조직에 제공할 수 있습니다.
--> 

## 작성 환경

다음 중 하나를 사용하여 데이터 팩터리를 작성/생성할 수 있습니다.

- **Azure Preview 포털**. Azure Preview 포털의 데이터 팩터리 블레이드는 데이터 팩터리 및 연결된 서비스를 만들기 위한 풍부한 사용자 인터페이스를 제공합니다. 포털에 포함된 **데이터 팩터리 편집기**에서 이러한 아티팩트에 대한 JSON 정의를 지정하여 연결된 서비스, 테이블, 데이터 집합 및 파이프라인을 쉽게 만들 수 있습니다. 편집기 개요는 [데이터 팩터리 편집기][data-factory-editor]를 참조하고, 포털/편집기를 사용하여 데이터 팩터리를 만들고 배포하는 예제는 [데이터 팩터리 시작][datafactory-getstarted]을 참조하세요.   
- **Azure PowerShell**. PowerShell 사용자이며 포털 UI 대신 PowerShell을 사용하려는 경우 Azure PowerShell의 일부로 제공되는 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들고 배포할 수 있습니다. 간단한 예제는 [Azure PowerShell을 사용하여 Azure 데이터 팩터리 만들기 및 모니터링][create-data-factory-using-powershell]을 참조하고, PowerShell cmdlet을 사용하여 데이터 팩터리를 만들고 배포하는 고급 예제는 [자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial]를 참조하세요. 데이터 팩터리 cmdlet의 포괄적인 설명서는 MSDN 라이브러리의 [데이터 팩터리 Cmdlet 참조][adf-powershell-reference] 콘텐츠를 참조하세요.  
- **.NET 클래스 라이브러리**. 데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 데이터 팩터리를 만들 수 있습니다. .NET SDK를 사용하여 데이터 팩터리를 만드는 연습은 [.NET SDK를 사용하여 데이터 팩터리 만들기, 모니터링 및 관리][create-factory-using-dotnet-sdk]를 참조하세요. 데이터 팩터리 .NET SDK의 포괄적인 설명서는 [데이터 팩터리 클래스 라이브러리 참조][msdn-class-library-reference]를 참조하세요.  
- **REST API**. Azure 데이터 팩터리 서비스에 의해 노출된 REST API를 사용하여 데이터 팩터리를 만들고 배포할 수도 있습니다. 데이터 팩터리 REST API의 포괄적인 설명서는 [데이터 팩터리 REST API 참조][msdn-rest-api-reference]를 참조하세요. 


##용어
이 섹션에서는 Azure 데이터 팩터리와 관련된 용어를 소개합니다.

### 데이터 팩터리
**Azure 데이터 팩터리**에는 Azure HDInsight와 같은 연결된 계산 서비스를 사용하여 연결된 데이터 저장소(Azure 저장소, Azure SQL 데이터베이스, 온-프레미스 SQL Server 등)의 데이터를 처리하는 파이프라인이 하나 이상 포함되어 있습니다. Azure 데이터 팩터리 자체에는 데이터가 없습니다. 데이터는 위에서 언급한 데이터 저장소에 저장됩니다.  

### 연결된 서비스
**연결된 서비스**는 Azure 데이터 팩터리에 연결된 서비스입니다. 연결된 서비스는 다음 중 하나일 수 있습니다.

- Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스와 같은 **데이터 저장소** 서비스. 데이터 저장소는 입출력 데이터 집합의 컨테이너입니다.    
- Azure HDInsight 및 Azure 기계 학습과 같은 **계산** 서비스. 계산 서비스는 입력 데이터를 처리하고 출력 데이터를 생성합니다.  

### 데이터 집합
**데이터 집합**은 명명된 데이터 뷰입니다. 설명되는 데이터는 단순한 바이트, CSV 파일과 같은 반구조적 데이터에서 관계형 테이블 또는 모델에 이르기까지 다양할 수 있습니다. 데이터 팩터리 **테이블**은 스키마가 있는 데이터 집합이며 사각형입니다. 데이터 저장소에 데이터 저장소를 참조하는 연결된 서비스를 만든 후 데이터 저장소에 저장된 입출력 데이터를 나타내는 데이터 집합을 정의합니다. 


###파이프라인
Azure 데이터 팩터리의 **파이프라인**은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 예를 들어 **복사 작업**은 원본 저장소의 데이터를 대상 저장소에 복사하고 **HDInsight 작업**은 Azure HDInsight 클러스터를 사용하여 Hive 쿼리나 Pig 스크립트로 데이터를 처리합니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 

Azure 데이터 팩터리 인스턴스를 만드는 단계는 일반적으로 다음과 같습니다.

1. **데이터 팩터리**를 만듭니다.
2. 각 데이터 저장소 또는 계산 서비스에 대한 **연결된 서비스**를 만듭니다.
3. 입력 및 출력 **데이터 집합**을 만듭니다.
4. **파이프라인**을 만듭니다. 

###작업
파이프라인의 데이터 처리 단계로, 하나 이상의 입력된 데이터 집합을 가져와 하나 이상의 출력 데이터 집합을 생성합니다.  작업은 실행 환경(예: Azure HDInsight 클러스터)에서 실행되고 Azure 데이터 팩터리와 연결된 데이터 저장소의 데이터를 읽거나 씁니다. 

Azure 데이터 팩터리 서비스는 파이프라인에서 다음 작업을 지원합니다. 

- **복사 작업**은 데이터 저장소의 데이터를 다른 데이터 저장소로 복사합니다. 복사 작업이 지원하는 데이터 저장소에 대한 자세한 내용은 [Azure 데이터 팩터리를 사용하여 데이터 복사][copy-data-with-adf]를 참조하세요. 
- **HDInsight 작업**은 HDInsight 클러스터에 대해 Hive/Pig 스크립트 또는 MapReduce 프로그램을 실행하여 데이터를 처리합니다. 자세한 내용은 [데이터 팩터리와 함께 Pig 및 Hive 사용][use-pig-hive] 및 [데이터 팩터리에서 MapReduce 프로그램 호출][run-map-reduce]을 참조하세요. 
- **Azure 기계 학습 배치 평가 작업**은 Azure 기계 학습 배치 평가 API를 호출합니다. 자세한 내용은 [Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기][azure-ml-adf]를 참조하세요. 
- **저장 프로시저 작업**은 Azure SQL 데이터베이스의 저장 프로시저를 호출합니다. 자세한 내용은 MSDN 라이브러리의 [저장 프로시저 작업][msdn-stored-procedure-activity]을 참조하세요.   

###조각
Azure 데이터 팩터리의 테이블은 조각으로 구성됩니다. 조각의 너비는 일정(매시간/매일)에 따라 결정됩니다. 일정이 "매시간"이면 파이프라인의 시작 시간 및 종료 시간을 사용하여 조각이 매시간 생성됩니다. 예를 들어 파이프라인 시작 날짜/시간이 03/03/2015 06:00:00(오전 6)이고 종료 날짜/시간이 03-03/2015 09:00:00(같은 날 오전 9)이면 1시간 간격마다 한 조각씩 3개의 데이터 조각(오전 6-7, 오전 7-8 및 오전 8-9)이 생성됩니다.    

조각을 사용하면 특정 기간 동안 전체 데이터의 하위 집합을 사용할 수 있습니다(예: 오후 1:00~오후 2:00 기간(시간) 동안 생성된 조각). 

### 조각에 대한 작업 실행
**실행** 또는 작업 실행은 조각에 대한 처리 단위입니다. 다시 시도하는 경우나 오류가 발생하여 조각을 다시 실행하는 경우 조각에 대한 실행이 하나 이상 있을 수 있습니다. 조각은 시작 시간으로 식별합니다.

###데이터 관리 게이트웨이
Microsoft **데이터 관리 게이트웨이**는 온-프레미스 데이터 원본을 사용하기 위해 클라우드 서비스에 연결하는 소프트웨어입니다. 회사 환경에 게이트웨이를 하나 이상 설치했어야 하며 게이트웨이를 Azure 데이터 팩터리 포털에 등록해야만 온-프레미스 데이터 원본을 연결된 서비스로 추가할 수 있습니다.
 
###데이터 허브
**데이터 허브**는 데이터 저장소 및 계산 서비스에 대한 논리적 컨테이너입니다. 예를 들어 HDFS를 저장소로, Hive/Pig를 계산(처리) 서비스로 사용하는 Hadoop 클러스터가 데이터 허브입니다. 마찬가지로 EDW(엔터프라이즈 데이터웨어하우스)를 데이터 허브로 모델링할 수 있습니다(데이터베이스를 저장소로, 저장 프로시저 및/또는 ETL 도구를 계산 서비스로 사용).  파이프라인은 데이터 저장소를 사용하고 데이터 허브의 계산 리소스에서 실행됩니다. 현재는 HDInsight 허브만 지원됩니다.

데이터 허브를 사용하면 데이터 팩터리를 논리 또는 도메인별 그룹으로 나눌 수 있습니다. 예를 들어 미서부 데이터 센터에 집중된 모든 연결된 서비스(데이터 저장소 및 계산) 및 파이프라인을 관리하는 "미서부 Azure 허브)나 판매 EDW에 대한 데이터 채우기 및 처리와 관련된 모든 연결된 서비스 및 파이프라인을 관리하는 "판매 EDW 허브"로 나눌 수 있습니다.

허브의 중요한 특징으로 파이프라인이 단일 허브에서 실행된다는 점을 둘 수 있습니다. 따라서 파이프라인을 정의할 때 해당 파이프라인 내의 테이블 또는 작업에서 참조되는 모든 연결된 서비스의 허브 이름이 파이프라인 자체의 허브 이름과 같아야 합니다. 연결된 서비스에 대해 HubName 속성을 지정하지 않으면 연결된 서비스는 "기본" 허브에 배치됩니다.

##다음 단계

1. [데이터 팩터리 시작][datafactory-getstarted]. 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 Azure 데이터 팩터리를 만드는 방법을 보여 주는 종단 간 자습서를 제공합니다.
2. [자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial]. 이 문서에서는 Azure 데이터 팩터리를 사용하는 **실제 시나리오**를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 **종단 간 연습**을 제공합니다.
3. [데이터 팩터리 - 질문과 대답][adf-faq]. 이 문서에서는 자주 묻는 질문과 대답 목록을 제공합니다. 
3. [Azure 데이터 팩터리 사용에 대한 일반적인 시나리오][adf-common-scenarios]. 이 문서에서는 Azure 데이터 팩터리 서비스 사용에 대한 몇 가지 일반적인 시나리오를 설명합니다.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

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

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->
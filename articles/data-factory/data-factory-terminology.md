<properties 
	pageTitle="Azure 데이터 팩터리 - 용어" 
	description="이 문서에서는 Azure 데이터 팩터리 서비스를 사용하여 데이터 팩터리를 만드는 데 사용되는 용어를 소개합니다." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

#Azure 데이터 팩터리 - 용어

## 데이터 팩터리
**Azure 데이터 팩터리**에는 Azure HDInsight와 같은 연결된 계산 서비스를 사용하여 연결된 데이터 저장소(Azure 저장소, Azure SQL 데이터베이스, 온-프레미스 SQL Server 등)의 데이터를 처리하는 파이프라인이 하나 이상 포함되어 있습니다. Azure 데이터 팩터리 자체에는 데이터가 없습니다. 데이터는 위에서 언급한 데이터 저장소에 저장됩니다.

## 연결된 서비스
**연결된 서비스**는 Azure 데이터 팩터리에 연결된 서비스입니다. 연결된 서비스는 다음 중 하나일 수 있습니다.

- Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스와 같은 **데이터 저장소** 서비스. 데이터 저장소는 입출력 데이터 집합의 컨테이너입니다.    
- Azure HDInsight, Azure 기계 학습 및 Azure 배치와 같은 **계산** 서비스. 계산 서비스는 입력 데이터를 처리하고 출력 데이터를 생성합니다.  

## 데이터 집합
**데이터 집합**은 명명된 데이터 뷰입니다. 설명되는 데이터는 단순한 바이트, CSV 파일과 같은 반구조적 데이터에서 관계형 테이블 또는 모델에 이르기까지 다양할 수 있습니다. 데이터 팩터리 **테이블**은 스키마가 있는 데이터 집합이며 사각형입니다. 데이터 저장소에 데이터 저장소를 참조하는 연결된 서비스를 만든 후 데이터 저장소에 저장된 입출력 데이터를 나타내는 데이터 집합을 정의합니다.


##파이프라인
Azure 데이터 팩터리의 **파이프라인**은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 예를 들어 **복사 작업**은 원본 저장소의 데이터를 대상 저장소에 복사하고 **HDInsight 작업**은 Azure HDInsight 클러스터를 사용하여 Hive 쿼리나 Pig 스크립트로 데이터를 처리합니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.

Azure 데이터 팩터리 인스턴스를 만드는 단계는 일반적으로 다음과 같습니다.

1. **데이터 팩터리**를 만듭니다.
2. 각 데이터 저장소 또는 계산 서비스에 대한 **연결된 서비스**를 만듭니다.
3. 입력 및 출력 **데이터 집합**을 만듭니다.
4. **파이프라인**을 만듭니다. 

##작업
파이프라인의 데이터 처리 단계로, 하나 이상의 입력된 데이터 집합을 가져와 하나 이상의 출력 데이터 집합을 생성합니다. 작업은 실행 환경(예: Azure HDInsight 클러스터)에서 실행되고 Azure 데이터 팩터리와 연결된 데이터 저장소의 데이터를 읽거나 씁니다.

Azure 데이터 팩터리 서비스는 파이프라인에서 다음 작업을 지원합니다.

- **복사 작업**은 데이터 저장소의 데이터를 다른 데이터 저장소로 복사합니다. 복사 작업이 지원하는 데이터 저장소에 대한 자세한 내용은 [Azure 데이터 팩터리를 사용하여 데이터 복사][copy-data-with-adf]를 참조하십시오. 
- **HDInsight 작업**은 HDInsight 클러스터에 대해 Hive/Pig 스크립트 또는 MapReduce 프로그램을 실행하여 데이터를 처리합니다. 자세한 내용은 [데이터 팩터리와 함께 Pig 및 Hive 사용][use-pig-hive] 및 [데이터 팩터리에서 MapReduce 프로그램 호출][run-map-reduce]을 참조하십시오. 
- **Azure 기계 학습 배치 평가 작업**은 Azure 기계 학습 배치 평가 API를 호출합니다. 자세한 내용은 [Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기][azure-ml-adf]를 참조하십시오. 
- **저장 프로시저 작업**은 Azure SQL 데이터베이스의 저장 프로시저를 호출합니다. 자세한 내용은 MSDN 라이브러리의 [저장 프로시저 작업][msdn-stored-procedure-activity]을 참조하십시오.   

##조각
Azure 데이터 팩터리의 테이블은 조각으로 구성됩니다. 조각의 너비는 일정(매시간/매일)에 따라 결정됩니다. 일정이 "매시간"이면 파이프라인의 시작 시간 및 종료 시간을 사용하여 조각이 매시간 생성됩니다. 예를 들어 파이프라인 시작 날짜/시간이 2015/03/03 06:00:00(오전 6시)이고 종료 날짜/시간이 2015/03-03 09:00:00(같은 날 오전 9시)이면 1시간마다 한 조각씩 3개의 데이터 조각(오전 6-7시, 오전 7-8시 및 오전 8-9시)이 생성됩니다.

조각을 사용하면 특정 기간 동안 전체 데이터의 하위 집합을 사용할 수 있습니다(예: 오후 1:00~오후 2:00 기간(시간) 동안 생성된 조각).

## 조각에 대한 작업 실행
**실행** 또는 작업 실행은 조각에 대한 처리 단위입니다. 다시 시도하는 경우나 오류가 발생하여 조각을 다시 실행하는 경우 조각에 대한 실행이 하나 이상 있을 수 있습니다. 조각은 시작 시간으로 식별합니다.

##데이터 관리 게이트웨이
Microsoft **데이터 관리 게이트웨이**는 온-프레미스 데이터 원본을 사용하기 위해 클라우드 서비스에 연결하는 소프트웨어입니다. 회사 환경에 게이트웨이를 하나 이상 설치했어야 하며 게이트웨이를 Azure 데이터 팩터리 포털에 등록해야만 온-프레미스 데이터 원본을 연결된 서비스로 추가할 수 있습니다.
 
##데이터 허브
**데이터 허브**는 데이터 저장소 및 계산 서비스에 대한 논리적 컨테이너입니다. 예를 들어 HDFS를 저장소로, Hive/Pig를 계산(처리) 서비스로 사용하는 Hadoop 클러스터가 데이터 허브입니다. 마찬가지로 EDW(엔터프라이즈 데이터웨어하우스)를 데이터 허브로 모델링할 수 있습니다(데이터베이스를 저장소로, 저장 프로시저 및/또는 ETL 도구를 계산 서비스로 사용). 파이프라인은 데이터 저장소를 사용하고 데이터 허브의 계산 리소스에서 실행됩니다. 현재는 HDInsight 허브만 지원됩니다.

데이터 허브를 사용하면 데이터 팩터리를 논리 또는 도메인별 그룹으로 나눌 수 있습니다. 예를 들어 미서부 데이터 센터에 집중된 모든 연결된 서비스(데이터 저장소 및 계산) 및 파이프라인을 관리하는 "미서부 Azure 허브)나 판매 EDW에 대한 데이터 채우기 및 처리와 관련된 모든 연결된 서비스 및 파이프라인을 관리하는 "판매 EDW 허브"로 나눌 수 있습니다.

허브의 중요한 특징으로 파이프라인이 단일 허브에서 실행된다는 점을 둘 수 있습니다. 따라서 파이프라인을 정의할 때 해당 파이프라인 내의 테이블 또는 작업에서 참조되는 모든 연결된 서비스의 허브 이름이 파이프라인 자체의 허브 이름과 같아야 합니다. 연결된 서비스에 대해 HubName 속성을 지정하지 않으면 연결된 서비스는 "기본" 허브에 배치됩니다.

## 참고 항목

1. [Azure 데이터 팩터리 소개][adf-intro]. 이 문서에서는 Azure 데이터 팩터리 서비스에 대한 개요, 이 서비스가 제공하는 가치 및 지원하는 시나리오에 대해 다룹니다.
2. [데이터 팩터리 시작][datafactory-getstarted]. 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 Azure 데이터 팩터리를 만드는 방법을 보여 주는 종단 간 자습서를 제공합니다.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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



 

<!---HONumber=July15_HO3-->
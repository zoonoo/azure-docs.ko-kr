<properties 
	pageTitle="Azure 데이터 팩터리-용어" 
	description="이 문서에서는 Azure 데이터 팩터리 서비스를 사용 하 여 데이터 팩터리를 만드는데 사용 되는 용어를 소개" 
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

#Azure 데이터 팩터리-용어

## 데이터 팩터리
 **Azure 데이터 팩터리** Azure HDInsight 같은 연결 된 계산 서비스를 사용 하 여 연결 된 데이터 저장소 (Azure 저장소, Azure SQL 데이터베이스, 온-프레미스 SQL Server 등...)에서 데이터를 처리 하는 하나 이상의 파이프라인을 했습니다. Azure 데이터 팩터리 자체에는 데이터가 없습니다. 데이터는 위에서 언급한 데이터 저장소에 저장됩니다.

## 연결된 서비스
A **서비스 연결** 는 Azure 데이터 팩터리에 연결 된 서비스입니다. 연결된 서비스는 다음 중 하나일 수 있습니다.

- A **데이터 저장소** Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스와 같은 서비스입니다. 데이터 저장소는 입출력 데이터 집합의 컨테이너입니다.    
- A **계산** Azure HDInsight, Azure 기계 학습 및 Azure 일괄 처리와 같은 서비스입니다. 계산 서비스는 입력 데이터를 처리하고 출력 데이터를 생성합니다.  

## 데이터 집합
A **데이터 집합** 데이터의 명명 된 뷰입니다. 설명되는 데이터는 단순한 바이트, CSV 파일과 같은 반구조적 데이터에서 관계형 테이블 또는 모델에 이르기까지 다양할 수 있습니다. 데이터 팩터리 **테이블** 사각형이 및 스키마가 있는 데이터 집합입니다. 데이터 저장소에 데이터 저장소를 참조하는 연결된 서비스를 만든 후 데이터 저장소에 저장된 입출력 데이터를 나타내는 데이터 집합을 정의합니다.


##파이프라인
A **파이프라인** 연결 된 저장소 서비스의 데이터에에서 연결 된 계산 서비스를 사용 하 여 데이터 팩터리는 Azure에서 처리 합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 예는 **복사 활동** 원본 저장소에서 데이터를 대상 저장소에 복사 및 **HDInsight 활동** 하이브 쿼리 또는 Pig 스크립트를 사용 하 여 데이터를 처리 하는 Azure HDInsight 클러스터를 사용 합니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.

Azure 데이터 팩터리 인스턴스를 만드는 단계는 일반적으로 다음과 같습니다.

1. 만들기는 **데이터 팩터리**.
2. 만들기는 **서비스 연결** 각 데이터에 대 한 저장 하거나 서비스를 계산 합니다.
3. 입력 및 출력을 만드는 **데이터 집합**.
4. 만들기는 **파이프라인**. 

##작업
파이프라인의 데이터 처리 단계로, 하나 이상의 입력된 데이터 집합을 가져와 하나 이상의 출력 데이터 집합을 생성합니다. 활동이 실행 환경에서 실행 (예: Azure HDInsight 클러스터) 및 읽기/쓰기 데이터를 데이터 저장소는 Azure 데이터 팩터리와 연결/연결 합니다.

Azure 데이터 팩터리 서비스는 파이프라인에서 다음 작업을 지원합니다.

- **복사 활동** 다른 데이터 저장소에 데이터 저장소에서 데이터를 복사 합니다. 참조 [Azure 데이터 팩터리를 사용 하 여 데이터를 복사][copy-data-with-adf] 데이터 복사 작업을 저장 하는 방법에 대 한 세부 정보에 대 한 지원 합니다. 
- **HDInsight 활동** HDInsight 클러스터에서 Hive/Pig 스크립트 또는 MapReduce 프로그램을 실행 하 여 데이터를 처리 합니다. 참조 [사용 하 여 Pig 및 Hive 데이터 팩터리로][use-pig-hive] 및 [데이터 공장에서 MapReduce 프로그램 호출][run-map-reduce] 대 한 자세한 내용은 합니다. 
- **Azure 컴퓨터 학습 일괄 처리 상태 평가 활동** API 점수 매기기 Azure 기계 학습 일괄 처리를 호출 합니다. 참조 [Azure 데이터 팩터리 및 Azure 컴퓨터 학습을 사용 하 여 만드는 예측 파이프라인][azure-ml-adf] 대 한 자세한 내용은 합니다. 
- **저장 프로시저 활동** Azure SQL 데이터베이스의 저장된 프로시저를 호출 합니다. 참조는 [저장 프로시저 활동][msdn-stored-procedure-activity] 대 한 자세한 내용은 MSDN Library에 있습니다.   

##조각
Azure 데이터 팩터리의 테이블은 조각으로 구성됩니다. 조각의 너비는 일정(매시간/매일)에 따라 결정됩니다. 일정이 "매시간"이면 파이프라인의 시작 시간 및 종료 시간을 사용하여 조각이 매시간 생성됩니다. 예를들어, 시작 날짜-시간 파이프라인은 2015 03/03/06시: 00 (오전 6 시) 및 종료 날짜-시간 03-03/2015 09시: 00 (오전 9 시 하루에), 3 개의 데이터 조각이 생성 되지 않으면 각 1 시간 간격에 대 한 슬라이스는: 오전 6-오전 7, 7-8을 고 오전 8-9.

특정 시간 창에 대 한 전체 데이터의 하위 집합을 사용 하는 기능을 제공 하는 분할 영역 (예: 기간 (시간)에 대해 생성 되는 조각을: 오후 1시 ~ 오후 2시).

## 조각에 대한 작업 실행
 **실행** 또는 실행 하는 활동에는 조각에 대 한 처리의 단위입니다. 다시 시도하는 경우나 오류가 발생하여 조각을 다시 실행하는 경우 조각에 대한 실행이 하나 이상 있을 수 있습니다. 조각은 시작 시간으로 식별합니다.

##데이터 관리 게이트웨이
Microsoft **데이터 관리 게이트웨이** 소비에 대 한 서비스를 클라우드로 온-프레미스 데이터 원본에 연결 하는 소프트웨어입니다. 회사 환경에 게이트웨이를 하나 이상 설치했어야 하며 게이트웨이를 Azure 데이터 팩터리 포털에 등록해야만 온-프레미스 데이터 원본을 연결된 서비스로 추가할 수 있습니다.
 
##데이터 허브
A **데이터 허브** 데이터 계산 및 저장소 서비스에 대 한 논리적 컨테이너입니다. 예를 들어 HDFS를 저장소로, Hive/Pig를 계산(처리) 서비스로 사용하는 Hadoop 클러스터가 데이터 허브입니다. 마찬가지로 EDW(엔터프라이즈 데이터웨어하우스)를 데이터 허브로 모델링할 수 있습니다(데이터베이스를 저장소로, 저장 프로시저 및/또는 ETL 도구를 계산 서비스로 사용). 파이프라인은 데이터 저장소를 사용하고 데이터 허브의 계산 리소스에서 실행됩니다. 현재는 HDInsight 허브만 지원됩니다.

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

<!---HONumber=GIT-SubDir-->
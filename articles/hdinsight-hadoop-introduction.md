<properties 
	pageTitle="HDInsight의 Hadoop이란: 클라우드 빅 데이터 분석 | Microsoft Azure" 
	description="HDInsight의 클라우드에 있는 Hadoop 구성 요소를 소개합니다. HDInsight에서 Hadoop 클러스터를 사용하여 빅 데이터를 관리, 분석 및 보고하는 방법을 알아봅니다." 
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="cgronlun"/>


# HDInsight의 Hadoop 소개: 클라우드에서 빅 데이터 분석 및 처리

Azure HDInsight의 Hadoop, 에코 시스템 및 빅 데이터에 대한 소개: HDInsight에서의 Hadoop 정의, Hadoop의 구성 요소, 일반적인 용어 및 빅 데이터 분석 시나리오를 가져옵니다. 또한, HDInsight에서 Hadoop을 사용하는 데 관한 문서, 자습서 및 리소스에 대해 알아봅니다.

## HDInsight의 Hadoop은 무엇인가요?

Azure HDInsight는 빅 데이터를 관리, 분석 및 보고하기 위한 소프트웨어 프레임워크를 제공하여 클라우드에서 Apache Hadoop 클러스터를 배포하고 프로비전합니다. Hadoop 코어는 HDFS(Hadoop Distributed File System)을 사용하는 안정적인 데이터 저장소와 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다.


## 빅 데이터란?
광범위한 비구조적 형식과 가변 의미 체계 컨텍스트에 대해 갈수록 많은 양이 더 빠르게 수집되는 데이터를 지칭합니다.

빅 데이터는 Twitter 피드의 텍스트, 산업 장비의 센서 정보, 온라인 카탈로그의 고객 검색 및 구매 관련 정보에 이르기까지 모든 디지털 정보의 본문을 설명하며 기록 데이터, 즉 저장된 데이터일 수도 있고 실시간 데이터, 즉 원본에서 직접 스트리밍되는 데이터일 수도 있습니다.

빅 데이터를 통해 작업 가능한 정보를 파악하려면 적절한 질문을 하고 문제와 관련된 데이터를 수집해야 할 뿐 아니라 데이터를 액세스, 정리, 분석하고 유용한 방식으로 표시해야 합니다. 이 경우 HDInsight의 Hadoop에 대한 빅 데이터 분석이 유용할 수 있습니다.


## <a name="overview"></a>HDInsight의 Hadoop 에코 시스템 개요

HDInsight는 빅 데이터 분석을 위해 가장 많이 사용되는 솔루션이며 빠르게 확장되는 Apache Hadoop 기술 스택인 Microsoft Azure를 기반으로 하는 클라우드 구현입니다. Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari 등의 구현이 포함됩니다. 또한 HDInsight는 Excel, SQL Server Analysis Services, SQL Server Reporting Services 등의 BI(비즈니스 인텔리전스) 도구와도 통합됩니다.


* Azure HDInsight에서는 **Linux** 또는 **Windows**를 기본 OS로 사용하여 클라우드에 Hadoop 클러스터를 배포하고 프로비저닝합니다.

	* **Linux용 HDInsight(미리 보기)** - Ubuntu의 Hadoop 클러스터. Linux 또는 Unix에 익숙하거나, 기존 Linux 기반 Hadoop 솔루션에서 마이그레이션하거나, Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합하려는 경우에 사용합니다.

	* **Windows용 HDInsight** -Windows 서버용 Hadoop 클러스터. Windows에 익숙하거나, 기존 Windows 기반 Hadoop 솔루션에서 마이그레이션하거나, .NET 또는 다른 Windows 기능과 통합하려는 경우에 사용합니다.

	다음 표에서는 이 둘을 비교 내용을 제공합니다.

	Category | Linux용 HDInsight | Windows용 HDInsight 
	---------| -------------------| --------------------
	**클러스터 OS** | Ubuntu 12.04 장기 지원(LTS) | Windows Server 2012 R2
	**클러스터 유형** | Hadoop은 | Hadoop, HBase, Storm
	**개발** | Azure 관리 포털, 크로스 플랫폼 명령줄, Azure PowerShell | Azure 관리 포털, 크로스 플랫폼 명령줄, Azure PowerShell, HDInsight .NET SDK
	**클러스터 UI** | Ambari | 클러스터 대시보드
	**원격 액세스** | SSH(secure Shell) | 원격 데스크톱 프로토콜(RDP)
	

* HDInsight에서는 **HDP(Hortonworks Data Platform)** Hadoop 배포판을 사용합니다.

* Apache Hadoop은 빅 데이터 관리 및 분석용 소프트웨어 프레임워크입니다. HDInsight에서는 특정 작업에 대한 여러 구성을 제공합니다. 또는 사용자가 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">스크립트 동작을 사용하여 클러스터를 사용자 지정</a>할 수 있습니다.

	* **Hadoop**: 데이터를 병렬로 처리하고 분석하도록 [HDFS](#HDFS)를 통해 신뢰할 수 있는 데이터 저장소와 간단한 [MapReduce](#mapreduce) 프로그래밍 모델을 제공합니다.
	
	* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: 구조화되지 않은/반구조화된 대량 데이터(잠재적으로 수십억 개의 행과 수십억 개의 열로 구성됨)에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 NoSQL 데이터베이스입니다. [HDInsight의 HBase 개요](hdinsight-hbase-overview.md)를 참조하세요.
	
	* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: 대규모 데이터 스트림을 빠르게 처리하기 위한 분산형 실시간 계산 시스템으로입니다. HDInsight에서 관리 클러스터로 제공됩니다. [Storm 및 Hadoop을 사용하여 실시간 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md)을 참조하세요.

## Hadoop의 구성 요소는 무엇인가요?

이전의 전체 구성 외에도 다음과 같은 개별 구성 요소가 HDInsight 클러스터에 포함되어 있습니다.

* **[Ambari](#ambari)** - 클러스터 프로비전, 관리 및 모니터링. 

* **[Avro](#avro)** (Microsoft .NET Library for Avro) - Microsoft .NET 환경을 위한 데이터 직렬화.

* **[Hive](#hive)** - SQL(Structured Query Language)과 같이 쿼리.

* **[Mahout](#mahout)** - 기계 학습.

* **[MapReduce and YARN](#mapreduce)** - 분산형 처리 및 리소스 관리.

* **[Oozie](#oozie)** - 워크플로 관리.

* **[Pig](#pig)** - MapReduce 변환을 위한 간단한 스크립팅.

* **[Sqoop](#sqoop)** -데이터 가져오기 및 내보내기.

* **[ZooKeeper](#zookeeper)** - 분산 시스템의 프로세스 조정.

> [AZURE.NOTE]특정 구성 요소 및 버전에 대한 자세한 내용은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][component-versioning]을 참조하세요.

### <a name="ambari"></a>Ambari

Apache Ambari는 Apache Hadoop 클러스터를 프로비전하고 관리 및 모니터링합니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. [Ambari를 사용하여 HDInsight 클러스터링 관리](hdinsight-hadoop-manage-ambari.md)(Linux만 해당), [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링](hdinsight-monitor-use-ambari-api.md) 및 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 참조</a>를 참조하세요.

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro는 Microsoft .NET 환경용 직렬화를 위한 Apache Avro 압축 이진 데이터 교환 형식을 구현합니다. It uses <a target="_blank" href="http://www.json.org/">JSON(JavaScript Object Notation)</a>을 사용하여 언어 상호 운용성을 따르는 언어 중립적 스키마를 정의합니다. 따라서 특정 언어로 직렬화된 데이터를 다른 언어에서 읽을 수 있습니다. 이 형식에 대한 자세한 내용은 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 사양</a>(영문)에서 확인할 수 있습니다. 
Avro 파일의 형식은 분산된 MapReduce 프로그래밍 모델을 지원합니다. 파일은 "분할 가능"하므로 파일에서 임의의 지점을 찾고 특정 블록부터 읽기 시작할 수 있습니다. 해당 방법은 [Microsoft .NET Library for Avro로 데이터 직렬화](hdinsight-dotnet-avro-serialization.md)를 참조하세요.


### <a name="hdfs"></a>HDFS

HDFS(Hadoop Distributed File System)는 MapReduce 및 YARN과 함께 Hadoop 에코시스템의 핵심 구성 요소인 분산 파일 시스템이자 HDInsight의 Hadoop 클러스터용 표준 파일 시스템입니다.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a>는 Hadoop을 기반으로 작성되는 데이터 웨어하우스 소프트웨어로, HiveQL이라는 SQL 스타일 언어를 사용하여 분산 저장소에서 대형 데이터 집합을 쿼리 및 관리하는 데 사용할 수 있습니다. Pig와 마찬가지로 Hive는 MapReduce 위에 있는 추상화 계층입니다. Hive를 실행하면 일련의 MapReduce 작업을 쿼리합니다. 개념상 Hive는 Pig보다 관계형 데이터베이스 관리 시스템에 더 가까우므로 보다 구조적인 데이터에 사용하기에 적합합니다. 구조화되지 않은 데이터의 경우에는 Pig가 더 적합합니다. [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)을 참조하세요.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a>는 Hadoop에서 실행되는 기계 학습 알고리즘의 확장 가능한 라이브러리입니다. 기계 학습 응용 프로그램은 통계 원칙을 사용하여 시스템이 데이터로부터 학습을 하고 이전 결과를 사용해 이후 동작을 결정하도록 가르칩니다. [Hadoop에서 Mahout를 사용하여 영화 추천 생성](hdinsight-mahout.md)을 참조하세요.

### <a name="mapreduce"></a>MapReduce 및 YARN
Hadoop MapReduce는 빅 데이터 집합을 병렬로 처리하는 응용 프로그램 작성용 소프트웨어 프레임워크로, 이 작업에서는 큰 데이터 집합을 분할한 다음 처리를 위해 데이터를 키-값 쌍으로 구성합니다.

Apache YARN은 JobTracker의 두 가지 주요 작업, 즉 리소스 관리 및 작업 일정 계획/모니터링을 개별 엔터티로 분할하는 차세대 MapReduce(MapReduce 2.0, MRv2)입니다.

MapReduce에 대한 자세한 내용은 Hadoop Wiki에서 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>를 참조하세요. YARN에 대한 자세한 내용은 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce(YARN)</a>를 참조하세요.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a>는 Hadoop 작업을 관리하는 워크플로 코디네이션 시스템입니다. Hadoop 스택과 통합되며 MapReduce, Pig, Hive 및 Sqoop용 Hadoop 작업을 지원합니다. 또한 Java 프로그램이나 셸 스크립트와 같이 시스템에 고유한 작업을 예약하는 데 사용할 수 있습니다. [HDInsight에서 Hadoop과 시간 기준 Oozie 코디네이터 사용](hdinsight-use-oozie-coordinator-time.md)을 참조하세요.

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a>는 Pig Latin이라는 단순한 스크립팅 언어를 사용하여 매우 큰 데이터 집합에 대해 복잡한 MapReduce 변환을 수행하는 데 사용할 수 있는 고급 플랫폼입니다. Pig는 Hadoop 내에서 실행되도록 Pig Latin 스크립트를 변환합니다. UDF(사용자 정의 함수)를 만들어 Pig Latin을 확장할 수 있습니다. [Hadoop에서 Pig를 사용하여 Apache 로그 파일 분석](hdinsight-use-pig.md)을 참조하세요.

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a>은 Hadoop과 관계형 데이터베이스(예: SQL) 또는 기타 구조적 데이터 저장소 간에 대량 데이터를 최대한 효율적으로 전송하는 도구입니다. [Hadoop과 함께 Sqoop 사용](hdinsight-use-sqoop.md)을 참조하세요.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a>는 데이터 레지스터의 공유 계층 구조 네임스페이스(znode)를 통해 대규모 분산 시스템의 프로세스를 조정합니다. Znode에는 상태, 위치, 구성 등 프로세스를 조정하기 위해 필요한 소량의 메타 정보가 들어 있습니다.

## <a name="advantage"></a>클라우드에서 Hadoop이 제공하는 이점

HDInsight의 Hadoop은 Azure 클라우드 에코시스템의 일부분으로 다음과 같은 여러 가지 이점을 제공합니다.

* Hadoop 클러스터 자동 프로비저닝 수동으로 Hadoop 클러스터를 구성하는 것보다 HDInsight 클러스터를 만들기가 훨씬 쉽습니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

* 최신 Hadoop 구성 요소. 자세한 내용은
* [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][구성 요소 버전 관리]를 참조하세요.

* 클러스터의 고가용성 및 안정성. 자세한 내용은 [HDInsight에서 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability.md)을 참조하세요.

* Hadoop 호환 옵션인 Azure Blob 저장소를 통한 효율적이고 경제적인 데이터 저장. 자세한 내용은 [HDInsight에서 Hadoop과 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

* [웹 사이트](../documentation/services/websites/) 및 [SQL 데이터베이스](../documentation/services/sql-database/)를 비롯한 기타 Azure 서비스와의 통합

* 저렴한 초기 비용. [무료 평가판](/pricing/free-trial/)으로 시작하거나 [HDInsight 가격 정보](/pricing/details/hdinsight/)를 확인해 보세요.


HDInsight의 Hadoop이 제공하는 이점에 대한 자세한 내용은 [HDInsight의 Azure 기능 페이지][marketing-page]를 참조하세요.



## <a id="resources"></a>빅 데이터 분석, Hadoop 및 HDInsight에 대해 자세히 알아볼 수 있는 리소스

아래 리소스를 사용하여 HDInsight의 Hadoop과 빅 데이터 분석에 대한 이 소개를 확장합니다.


### Linux용 HDInsight(미리 보기)

* [Linux용 HDInsight 시작](hdinsight-hadoop-linux-get-started.md) - Linux에 HDInsight Hadoop 클러스터를 프로비전하고 샘플 Hive 쿼리를 실행하는 데 관한 빠른 시작 자습서입니다.

* [사용자 지정 옵션을 사용하여 Linux에 HDInsight 프로비전](hdinsight-hadoop-provision-linux-clusters.md) - Azure 관리 포털, Azure 플랫폼 간 명령줄 또는 Azure PowerShell을 통해 사용자 지정 옵션을 사용하여 Linux에 HDInsight Hadoop 클러스터를 프로비전하는 방법에 대해 알아봅니다.

* [Linux에서 HDInsight 작업](hdinsight-hadoop-linux-information.md) - Azure에 프로비전된 Hadoop Linux 클러스터 작업에 대한 간단한 팁을 얻습니다.

* [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md) - Ambari 웹 또는 Ambari REST API를 사용하여 HDInsight 클러스터의 Linux 기반 Hadoop을 모니터링하고 관리하는 방법에 대해 알아봅니다.


### Windows용 HDInsight

* [HDInsight 설명서](http://azure.microsoft.com/documentation/services/hdinsight/) - 문서, 비디오 및 추가 리소스에 대한 링크가 있는 Azure HDInsight용 설명서 페이지입니다.

* [HDInsight용 학습 맵](hdinsight-learn-map.md) - HDInsight용 Hadoop 설명서를 둘러볼 수 있습니다.

* [Azure HDInsight 시작](hdinsight-get-started.md) - HDInsight에서 Hadoop을 시작하기 위한 빠른 시작 자습서입니다.

* [HDInsight 샘플 실행](hdinsight-run-samples.md) - HDInsight와 함께 제공된 샘플을 실행하는 방법에 대한 자습서입니다.
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx) - HDInsight SDK의 참조 설명서입니다.


### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> - 전체 컴퓨터의 클러스터에 대해 큰 데이터 집합을 분산 처리할 수 있는 프레임워크인 Apache Hadoop 소프트웨어 라이브러리에 대해 자세히 알아 봅니다.	

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a> -Hadoop 응용 프로그램이 사용하는 주 저장 시스템인 Hadoop 분산 파일 시스템의 아키텍처 및 설계에 대해 자세히 알아 봅니다.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 자습서</a> - 컴퓨터 노드의 대형 클러스터에서 엄청난 양의 데이터를 병렬로 빠르게 처리하는 Hadoop 응용 프로그램을 작성하기 위한 프로그래밍 프레임워크에 대해 자세히 알아봅니다.

### Azure의 SQL 데이터베이스	
		
* [Azure SQL 데이터베이스](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx) - SQL 데이터베이스용 MSDN 설명서입니다.
	
* [SQL 데이터베이스용 관리 포털](https://msdn.microsoft.com/library/azure/dn771027.aspx) - 클라우드에서 SQL 데이터베이스를 관리하기 위한 간단하고 사용하기 쉬운 데이터베이스 관리 도구입니다.

* [SQL 데이터베이스용 Adventure Works](http://msftdbprodsamples.codeplex.com/releases/view/37304) - SQL 데이터베이스 샘플 데이터베이스를 다운로드하는 페이지입니다.

### Microsoft 비즈니스 인텔리전스(Windows용 HDInsight)

익숙한 BI(비즈니스 인텔리전스) 도구(예: Excel, PowerPivot, SQL Server Analysis Services 및 SQL Server Reporting Services)는 파워 쿼리 추가 기능이나 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight와 통합된 데이터를 가져오고 분석하고 보고합니다.

이러한 BI 도구를 통해 빅 데이터를 보다 쉽게 분석할 수 있습니다.
 
* [파워 쿼리로 Hadoop에 Excel 연결](hdinsight-connect-excel-power-query.md) - HDInsight 클러스터와 연결된 데이터를 저장하는 Azure 저장소 계정에 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 연결하는 방법을 알아 봅니다. 

* [Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결](hdinsight-connect-excel-hive-ODBC-driver.md) - Microsoft Hive ODBC 드라이버를 사용하여 HDInsight에서 데이터를 가져오는 방법에 대해 알아봅니다.

* [Microsoft 클라우드 플랫폼](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx) - Office 365용 Power BI에 대해 알아보고 SQL Server 평가판을 다운로드하고 SharePoint Server 2013 및 SQL Server BI를 설치합니다.

* <a target="_blank" https://msdn.microsoft.com/library/hh231701.aspx">SQL Server Analysis Services에 대해 자세히 알아보기</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">SQL Server Reporting Services에 대해 알아보기</a>.


### 빅 데이터 분석을 위해 Hadoop 솔루션 시험 사용(Windows용 HDInsight)

조직의 데이터에 대한 빅 데이터 분석을 사용하여 비즈니스에 대한 통찰력을 얻습니다. 다음은 몇 가지 예입니다.

* [Analyze HVAC 센서 데이터](hdinsight-hive-analyze-sensor-data.md) - HDInsight(Hadoop)에서 Hive를 사용하여 센서 데이터를 분석한 다음 Microsoft Excel에서 데이터를 시각화하는 방법에 대해 알아봅니다. 이 샘플에서는 Hive를 사용하여 HVAC 시스템이 생성한 기록 데이터를 처리해 설정된 온도를 안정적으로 유지 관리할 수 없는 시스템을 확인합니다.

* [HDInsight와 Hive를 사용하여 웹 사이트 로그 분석](hdinsight-hive-analyze-website-log.md) - HDInsight에서 HiveQL을 사용하여 웹 사이트 로그를 분석해 외부 웹 사이트로부터의 일일 방문 빈도를 파악하고 사용자에게 발생하는 웹 사이트 오류의 요약을 확인하는 방법에 대해 알아봅니다.

* [HDInsight(Hadoop)에서 Storm 및 HBase를 사용하여 실시간으로 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md) - HDInsight에서 Storm 클러스터를 사용하여 Azure 이벤트 허브의 센서 데이터를 처리한 다음 처리된 센서 데이터를 웹 기반 대시보드에 거의 실시간 정보로 표시하는 솔루션을 빌드하는 방법에 대해 알아봅니다.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[구성 요소 버전 관리]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!--HONumber=54-->
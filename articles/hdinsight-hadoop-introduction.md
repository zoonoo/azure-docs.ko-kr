<properties urlDisplayName="HDInsight Introduction" pageTitle="HDInsight의 Hadoop 소개: 클라우드의 빅 데이터 분석 | Azure" metaKeywords="" description="Azure HDInsight가 클라우드에서 Apache Hadoop 클러스터를 사용하여 빅 데이터를 관리, 분석 및 보고하기 위한 소프트웨어 프레임워크를 제공하는 방법에 대해 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight의 Hadoop 소개: 클라우드의 빅 데이터 처리 및 분석" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# HDInsight의 Hadoop 소개: 클라우드의 빅 데이터 처리 및 분석

HDInsight 구성 요소, 일반적인 용어 및 시나리오에 대해 간략하게 설명하고 HDInsight에서 Hadoop을 사용하기 위한 리소스에 대해 알아봅니다.

Azure HDInsight는 빅 데이터를 관리, 분석 및 보고하기 위한 소프트웨어 프레임워크를 제공하여 클라우드에서 Apache Hadoop 클러스터를 배포하고 프로비전합니다. Hadoop 코어는 HDFS(Hadoop Distributed File System)을 사용하는 안정적인 데이터 저장소와 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다.

### 빅 데이터란?

광범위한 비구조적 형식과 가변 의미 체계 컨텍스트에 대해 갈수록 많은 양이 더 빠르게 수집되는 데이터를 지칭합니다.

빅 데이터는 Twitter 피드의 텍스트, 산업 장비의 센서 정보, 온라인 카탈로그의 고객 검색 및 구매 관련 정보에 이르기까지 모든 디지털 정보의 본문을 설명하며 기록 데이터, 즉 저장된 데이터일 수도 있고 실시간 데이터, 즉 원본에서 직접 스트리밍되는 데이터일 수도 있습니다.

빅 데이터를 통해 작업 가능한 정보를 파악하려면 적절한 질문을 하고 문제와 관련된 데이터를 수집해야 할 뿐 아니라 데이터를 액세스, 정리, 분석하고 유용한 방식으로 표시해야 합니다. HDInsight의 Hadoop에서 이러한 작업을 수행할 수 있습니다.

## 이 문서에서는 다음을 수행합니다.

이 문서에서는 HDInsight의 Hadoop에 대해 간략하게 설명합니다. 여기에는 다음 사항이 포함됩니다.

-   **[HDInsight의 Hadoop 에코시스템 개요:][HDInsight의 Hadoop 에코시스템 개요:]** HDInsight는 Azure의 Hadoop 솔루션이며 Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari 등의 구현을 제공합니다. 또한 HDInsight는 Excel, SQL Server Analysis Services, SQL Server Reporting Services 등의 BI(비즈니스 인텔리전스) 도구와도 통합됩니다.
-   **[클라우드에서 Hadoop이 제공하는 이점:][클라우드에서 Hadoop이 제공하는 이점:]** HDInsight의 Hadoop 클라우드 구현을 고려해야 하는 이유에 대해 설명합니다.
-   **[빅 데이터 분석용 HDInsight 솔루션:][빅 데이터 분석용 HDInsight 솔루션:]** HDInsight를 사용하여 Twitter 문장 분석, HVAC 시스템 효율성 분석 등 조직과 관련된 질문에 대답할 수 있는 몇 가지 실용적인 방식에 대해 설명합니다.
-   **[빅 데이터 분석, Hadoop 및 HDInsight에 대해 자세히 알아볼 수 있는 리소스:][빅 데이터 분석, Hadoop 및 HDInsight에 대해 자세히 알아볼 수 있는 리소스:]** 추가 정보의 링크를 제공합니다.

## <a name="overview"></a>HDInsight의 Hadoop 에코시스템 개요

Hadoop은 빠르게 확장되는 기술 스택이자 빅 데이터 분석에 적합한 솔루션입니다. HDInsight는 Hadoop의 Microsoft Azure 클라우드 구현을 위한 프레임워크입니다.

HDInsight의 Hadoop 클러스터는 HDP(Hortonworks Data Platform) 분산 버전과 해당 분산 내의 Hadoop 구성 요소 집합을 사용합니다. HDInsight 클러스터의 구성 요소 및 해당 버전에 대한 자세한 내용은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능]을 참조하세요.

Apache Hadoop은 빅 데이터 관리 및 분석용 소프트웨어 프레임워크입니다. Apache Hadoop 코어는 신뢰할 만한 데이터 저장소에 HDFS(Hadoop Distributed File System) 및 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다. HDFS는 데이터 복제를 사용하여 고도의 분산 시스템을 배포할 때 발생하는 하드웨어 실패 문제를 처리합니다.

HDInsight의 Hadoop 기술은 다음과 같습니다.

-   **[Ambari][Ambari]:** 클러스터 프로비전, 관리 및 모니터링
-   **[Avro][Avro]**(Microsoft .NET Library for Avro): Microsoft .NET 환경용 데이터 직렬화
-   **[HBase][HBase]:** 매우 큰 테이블용 비관계형 데이터베이스
-   **[HDFS][HDFS]:** Hadoop Distributed File System
-   **[Hive][Hive]:** SQL 스타일 쿼리
-   **[Mahout][Mahout]:** 기계 학습
-   **[MapReduce 및 YARN][MapReduce 및 YARN]:** 분산형 처리 및 리소스 관리
-   **[Oozie][Oozie]:** 워크플로 관리
-   **[Pig][Pig]:** MapReduce 변환을 위한 간단한 스크립팅
-   **[Sqoop][Sqoop]:** 데이터 가져오기 및 내보내기
-   **[Storm][Storm]:** 고속 대규모 데이터 스트림 실시간 처리
-   **[Zookeeper][Zookeeper]:** 분산 시스템의 프로세스 조정

또한 HDInsight와 함께 사용할 수 있는 **[비즈니스 인텔리전스 도구][비즈니스 인텔리전스 도구]**에 대해서도 살펴보세요.

### <a name="ambari"></a>Ambari

Apache Ambari는 Apache Hadoop 클러스터를 프로비전하고 관리 및 모니터링합니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링][Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링] 및 [Apache Ambari API 참조][Apache Ambari API 참조]를 참조하세요.

### <a name="avro"></a>Avro(Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro는 Microsoft .NET 환경용 직렬화를 위한 Apache Avro 압축 이진 데이터 교환 형식을 구현합니다. 또한 [JSON][JSON]을 사용하여 언어 상호 운용성을 따르는 언어 중립적 스키마를 정의합니다. 따라서 특정 언어로 직렬화된 데이터를 다른 언어에서 읽을 수 있습니다. 이 형식에 대한 자세한 내용은 [Apache Avro 사양][Apache Avro 사양]에서 확인할 수 있습니다.
Avro 파일의 형식은 분산 MapReduce 프로그래밍 모델을 지원합니다. 파일은 "분할 가능"하므로 파일에서 임의의 지점을 찾고 특정 블록부터 읽기 시작할 수 있습니다. 해당 방법은 [Microsoft .NET Library for Avro로 데이터 직렬화][Microsoft .NET Library for Avro로 데이터 직렬화]를 참조하세요.

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase]는 Hadoop을 기반으로 작성되는 비관계형 데이터베이스이며, 대량의 비구조적 및 반구조적 데이터(행 수십억 개x열 수백만 개가 포함될 수 있음)에 사용할 수 있습니다. HDInsight의 HBase 클러스터는 Azure Blob 저장소에 직접 데이터를 저장하도록 구성되므로 대기 시간이 짧고 탄력성이 보다 우수합니다. [HDInsight의 HBase 개요][HDInsight의 HBase 개요]를 참조하세요.

### <a name="hdfs"></a>HDFS

HDFS(Hadoop Distributed File System)는 MapReduce 및 YARN과 함께 Hadoop 에코시스템의 핵심 구성 요소인 분산 파일 시스템이자 HDInsight의 Hadoop 클러스터용 표준 파일 시스템입니다.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive]는 Hadoop을 기반으로 작성되는 데이터 웨어하우스 소프트웨어로, HiveQL이라는 SQL 스타일 언어를 사용하여 분산 저장소에서 대형 데이터 집합을 쿼리 및 관리하는 데 사용할 수 있습니다. Pig처럼 Hive도 MapReduce 상위의 추상화 계층이며, 실행 시 쿼리를 MapReduce 작업 계열로 변환합니다. 개념상 Hive는 Pig보다 관계형 데이터베이스 관리 시스템에 더 가까우므로 보다 구조적인 데이터에 사용하기에 적합합니다. 구조화되지 않은 데이터의 경우에는 Pig가 더 적합합니다. [HDInsight에서 Hadoop과 Hive 사용][HDInsight에서 Hadoop과 Hive 사용]을 참조하세요.

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout]는 Hadoop에서 실행되는 기계 학습 알고리즘의 확장 가능한 라이브러리입니다. 기계 학습 응용 프로그램은 통계 원칙을 사용하여 시스템이 데이터로부터 학습을 하고 이전 결과를 사용해 이후 동작을 결정하도록 가르칩니다. [Hadoop에서 Mahout를 사용하여 영화 추천 생성][Hadoop에서 Mahout를 사용하여 영화 추천 생성]을 참조하세요.

### <a name="mapreduce"></a>MapReduce 및 YARN

Hadoop MapReduce는 빅 데이터 집합을 병렬로 처리하는 응용 프로그램 작성용 소프트웨어 프레임워크로, 큰 데이터 집합을 분할한 다음 처리를 위해 데이터를 키-값 쌍으로 구성합니다.

Apache YARN은 JobTracker의 두 가지 주요 작업, 즉 리소스 관리 및 작업 일정 계획/모니터링을 개별 엔터티로 분할하는 차세대 MapReduce(MapReduce 2.0, MRv2)입니다.

MapReduce에 대한 자세한 내용은 Hadoop Wiki에서 [MapReduce][MapReduce]를 참조하세요. YARN에 대한 자세한 내용은 [Apache Hadoop NextGen MapReduce(YARN)][Apache Hadoop NextGen MapReduce(YARN)]를 참조하세요.

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie]는 Hadoop 작업을 관리하는 워크플로 조정 시스템으로, Hadoop 스택과 통합되며 MapReduce, Pig, Hive 및 Sqoop용 Hadoop 작업을 지원합니다. Java 프로그램이나 셸 스크립트와 같이 시스템에 특정한 작업을 예약하는 데에도 사용할 수 있습니다. [HDInsight에서 Hadoop과 시간 기준 Oozie 코디네이터 사용][HDInsight에서 Hadoop과 시간 기준 Oozie 코디네이터 사용]을 참조하세요.

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig]는 Pig Latin이라는 단순한 스크립팅 언어를 사용하여 매우 큰 데이터 집합에 대해 복잡한 MapReduce 변환을 수행하는 데 사용할 수 있는 고급 플랫폼입니다. Pig는 Hadoop 내에서 실행되도록 Pig Latin 스크립트를 변환합니다. UDF(사용자 정의 함수)를 만들어 Pig Latin을 확장할 수 있습니다. [Hadoop에서 Pig를 사용하여 Apache 로그 파일 분석][Hadoop에서 Pig를 사용하여 Apache 로그 파일 분석]을 참조하세요.

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop]은 Hadoop과 관계형 데이터베이스(예: SQL) 또는 기타 구조적 데이터 저장소 간에 대량 데이터를 최대한 효율적으로 전송하는 도구입니다. [Hadoop과 함께 Sqoop 사용][Hadoop과 함께 Sqoop 사용]을 참조하세요.

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm]은 대규모 데이터 스트림을 빠르게 처리하기 위한 분산형 실시간 계산 시스템으로, HDInsight에서 관리 클러스터로 제공됩니다. [Storm 및 Hadoop을 사용하여 실시간 센서 데이터 분석][Storm 및 Hadoop을 사용하여 실시간 센서 데이터 분석]을 참조하세요.

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper]는 데이터 레지스터의 공유 계층 구조 네임스페이스(znode)를 통해 대규모 분산 시스템의 프로세스를 조정합니다. znode는 상태, 위치, 구성 등 프로세스를 조정하는 데 필요한 소량의 메타 정보를 포함합니다.

### <a name="bi"></a>비즈니스 인텔리전스 도구

익숙한 BI(비즈니스 인텔리전스) 도구(예: Excel, PowerPivot, SQL Server Analysis Services 및 Reporting Services)는 파워 쿼리 추가 기능이나 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight와 통합된 데이터를 가져오고 분석하고 보고합니다.

이러한 BI 도구를 통해 빅 데이터를 보다 쉽게 분석할 수 있습니다.

-   [Microsoft Excel용 파워 쿼리 다운로드][Microsoft Excel용 파워 쿼리 다운로드]
-   [Microsoft Hive ODBC 드라이버 다운로드][Microsoft Hive ODBC 드라이버 다운로드]
-   [SQL Server Analysis Services에 대해 자세히 알아보기][SQL Server Analysis Services에 대해 자세히 알아보기]
-   [SQL Server Reporting Services에 대해 알아보기][SQL Server Reporting Services에 대해 알아보기]

## <a name="advantage"></a>클라우드에서 Hadoop이 제공하는 이점

HDInsight의 Hadoop은 Azure 클라우드 에코시스템의 일부분으로 다음과 같은 여러 가지 이점을 제공합니다.

-   최신 Hadoop 구성 요소. 자세한 내용은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능]을 참조하세요.
-   클러스터의 고가용성 및 안정성. 자세한 내용은 [HDInsight에서 Hadoop 클러스터의 가용성 및 안정성][HDInsight에서 Hadoop 클러스터의 가용성 및 안정성]을 참조하세요.
-   Hadoop 호환 옵션인 Azure Blob 저장소를 통한 효율적이고 경제적인 데이터 저장. 자세한 내용은 [HDInsight에서 Hadoop과 Azure Blob 저장소 사용][HDInsight에서 Hadoop과 Azure Blob 저장소 사용]을 참조하세요.
-   [웹 사이트][웹 사이트] 및 [SQL 데이터베이스][SQL 데이터베이스]를 비롯한 기타 Azure 서비스와의 통합
-   저렴한 초기 비용. [무료 평가판][무료 평가판]으로 시작하거나 [HDInsight 가격 정보][HDInsight 가격 정보]를 확인해 보세요.

HDInsight의 Hadoop이 제공하는 이점에 대한 자세한 내용은 [HDInsight의 Azure 기능 페이지][HDInsight의 Azure 기능 페이지]를 참조하세요.

## <a name="solutions"></a>빅 데이터 분석에 HDInsight 솔루션 활용

조직의 데이터를 분석하면 업무 관련 정보를 파악할 수 있습니다. 다음은 몇 가지 예제입니다.

-   [HVAC 센서 데이터 분석][HVAC 센서 데이터 분석]: HDInsight(Hadoop)에서 Hive를 사용하여 센서 데이터를 분석한 다음 Microsoft Excel에서 데이터를 시각화하는 방법에 대해 알아봅니다. 이 샘플에서는 Hive를 사용하여 HVAC 시스템이 생성한 기록 데이터를 처리해 설정된 온도를 안정적으로 유지 관리할 수 없는 시스템을 확인합니다.
-   [HDInsight에서 Hive를 사용하여 웹 사이트 로그 분석][HDInsight에서 Hive를 사용하여 웹 사이트 로그 분석]: HDInsight에서 HiveQL을 사용하여 웹 사이트 로그를 분석해 외부 웹 사이트로부터의 일일 방문 빈도를 파악하고 사용자에게 발생하는 웹 사이트 오류의 요약을 확인하는 방법에 대해 알아봅니다.
-   [HDInsight(Hadoop)에서 Storm 및 HBase를 사용하여 실시간으로 센서 데이터 분석][Storm 및 Hadoop을 사용하여 실시간 센서 데이터 분석]: HDInsight에서 Storm 클러스터를 사용하여 Azure 이벤트 허브의 센서 데이터를 처리한 다음 처리된 센서 데이터를 웹 기반 대시보드에 거의 실시간 정보로 표시하는 솔루션을 빌드하는 방법에 대해 알아봅니다.

HDInsight에서 Hadoop을 사용해 보려면 [HDInsight 설명서 페이지][HDInsight 설명서 페이지]의 살펴보기 섹션에서 "시작" 문서를 참조하세요. 고급 예제를 살펴보려면 분석 섹션에서 아래쪽으로 스크롤하세요.

## <a name="resources"></a>빅 데이터 분석, Hadoop 및 HDInsight에 대해 자세히 알아볼 수 있는 리소스

### 시작

-   [HDInsight 설명서][HDInsight 설명서 페이지]: 문서, 비디오 및 추가 리소스에 대한 링크가 있는 Azure HDInsight용 설명서 페이지입니다.

-   [HDInsight의 Hadoop 학습 지도][HDInsight의 Hadoop 학습 지도]: HDInsight용 Hadoop 설명서를 둘러볼 수 있습니다.

-   [Azure HDInsight 시작][Azure HDInsight 시작]: HDInsight에서 Hadoop을 시작하기 위한 빠른 시작 자습서입니다.

-   [HDInsight 샘플 실행][HDInsight 샘플 실행]: HDInsight와 함께 제공된 샘플을 실행하는 방법에 대한 자습서입니다.

-   [Azure HDInsight SDK][Azure HDInsight SDK]: HDinsight SDK의 참조 설명서입니다.

### Azure의 SQL 데이터베이스

-   [Azure SQL 데이터베이스][Azure SQL 데이터베이스]: SQL 데이터베이스용 MSDN 설명서입니다.

-   [SQL 데이터베이스용 관리 포털][SQL 데이터베이스용 관리 포털]: 클라우드에서 SQL 데이터베이스를 관리하기 위한 간단하고 사용하기 쉬운 데이터베이스 관리 도구입니다.

-   [SQL 데이터베이스용 Adventure Works][SQL 데이터베이스용 Adventure Works]: SQL 데이터베이스 샘플 데이터베이스를 위한 다운로드 페이지입니다.

### Microsoft 비즈니스 인텔리전스

-   [파워 쿼리로 Hadoop에 Excel 연결][파워 쿼리로 Hadoop에 Excel 연결]: HDInsight 클러스터와 연결된 데이터를 저장하는 Azure 저장소 계정에 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 연결하는 방법을 알아 봅니다.

-   [HDInsight에 Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결][HDInsight에 Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결]: Microsoft Hive ODBC 드라이버로 데이터를 HDInsight에서 가져오는 방법을 알아봅니다.

-   [Microsoft BI(비즈니스 인텔리전스)][Microsoft BI(비즈니스 인텔리전스)]: Office 365용 Power BI에 대해 알아보고 SQL Server 평가판을 다운로드하고 SharePoint Server 2013 및 SQL Server BI를 설치합니다.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: 전체 컴퓨터의 클러스터에 대해 큰 데이터 집합을 분산 처리할 수 있는 프레임워크인 Apache Hadoop 소프트웨어 라이브러리에 대해 자세히 알아 봅니다.

-   <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: Hadoop 응용 프로그램이 사용하는 주 저장 시스템인 HDFS(Hadoop Distributed File System)의 아키텍처 및 설계에 대해 자세히 알아 봅니다.

-   <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: 컴퓨터 노드의 대형 클러스터에서 엄청난 양의 데이터를 병렬로 빠르게 처리하는 Hadoop 응용 프로그램을 작성하기 위한 프로그래밍 프레임워크에 대해 자세히 알아 봅니다.

  [HDInsight의 Hadoop 에코시스템 개요:]: #overview
  [클라우드에서 Hadoop이 제공하는 이점:]: #advantage
  [빅 데이터 분석용 HDInsight 솔루션:]: #solutions
  [빅 데이터 분석, Hadoop 및 HDInsight에 대해 자세히 알아볼 수 있는 리소스:]: #resources
  [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce 및 YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [비즈니스 인텔리전스 도구]: #bi
  [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링]: ../hdinsight-monitor-use-ambari-api/
  [Apache Ambari API 참조]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Apache Avro 사양]: http://avro.apache.org/docs/current/spec.html
  [Microsoft .NET Library for Avro로 데이터 직렬화]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [HDInsight의 HBase 개요]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [HDInsight에서 Hadoop과 Hive 사용]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Hadoop에서 Mahout를 사용하여 영화 추천 생성]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [Apache Hadoop NextGen MapReduce(YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [HDInsight에서 Hadoop과 시간 기준 Oozie 코디네이터 사용]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Hadoop에서 Pig를 사용하여 Apache 로그 파일 분석]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Hadoop과 함께 Sqoop 사용]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Storm 및 Hadoop을 사용하여 실시간 센서 데이터 분석]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Microsoft Excel용 파워 쿼리 다운로드]: http://www.microsoft.com/ko-kr/download/details.aspx?id=39379
  [Microsoft Hive ODBC 드라이버 다운로드]: http://www.microsoft.com/ko-kr/download/details.aspx?id=40886
  [SQL Server Analysis Services에 대해 자세히 알아보기]: http://www.microsoft.com/ko-kr/server-cloud/solutions/business-intelligence/analysis.aspx
  [SQL Server Reporting Services에 대해 알아보기]: http://msdn.microsoft.com/ko-kr/library/ms159106.aspx
  [HDInsight에서 Hadoop 클러스터의 가용성 및 안정성]: ../hdinsight-high-availability/
  [HDInsight에서 Hadoop과 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [웹 사이트]: ../documentation/services/websites/
  [SQL 데이터베이스]: ../documentation/services/sql-database/
  [무료 평가판]: /pricing/free-trial/
  [HDInsight 가격 정보]: ../pricing/details/hdinsight/
  [HDInsight의 Azure 기능 페이지]: ../services/hdinsight/
  [HVAC 센서 데이터 분석]: ../hdinsight-hive-analyze-sensor-data/
  [HDInsight에서 Hive를 사용하여 웹 사이트 로그 분석]: ../hdinsight-hive-analyze-website-log/
  [HDInsight 설명서 페이지]: ../documentation/services/hdinsight/
  [HDInsight의 Hadoop 학습 지도]: ../hdinsight-learn-map
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 샘플 실행]: ../hdinsight-run-samples/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
  [Azure SQL 데이터베이스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336279.aspx
  [SQL 데이터베이스용 관리 포털]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg442309.aspx
  [SQL 데이터베이스용 Adventure Works]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [파워 쿼리로 Hadoop에 Excel 연결]: ../hdinsight-connect-excel-power-query/
  [HDInsight에 Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI(비즈니스 인텔리전스)]: http://www.microsoft.com/ko-kr/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/

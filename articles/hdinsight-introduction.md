<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />



# HDInsight의 Hadoop 소개

## 개요
Azure HDInsight는 빅데이터에서 관리, 분석 및 보고하도록 설계된 소프트웨어 프레임워크를 제공하여 클라우드에서 Apache™ Hadoop® 클러스터를 배포하고 프로비전하는 서비스입니다.

### 빅데이터
데이터는 계속 커지는 데이터 양, 증가하는 빠른 속도, 구조화되지 않은 형식 및 다양한 맥락의 폭넓은 다양성에서 수집되므로 "빅데이터"라고 표현합니다. 빅데이터는 수집만으로는 엔터프라이즈에 가치가 제공되지 않습니다. 실행 가능한 인텔리전스 또는 의견 형식으로 가치를 제공하기 위해 빅데이터에 대해 올바른 질문을 해야 하고 문제와 관련된 데이터가 수집되어야 할 뿐만 아니라 해당 데이터를 액세스, 정리, 분석한 후 이제 매시업으로 참조될 수 있는 관점 및 맥락을 만드는 다양한 다른 소스의 데이터와 함께 유용한 방법으로 데이터를 제공해야 합니다.

### Apache Hadoop
Apache Hadoop은 빅데이터를 쉽게 관리하고 분석하게 해주는 소프트웨어 프레임워크입니다. Apache Hadoop 코어는 신뢰할 만한 데이터 저장소에 HDFS(Hadoop Distributed File System) 및 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다. HDFS는 데이터 복제를 사용하여 고도의 분산 시스템을 배포할 때 발생하는 하드웨어 실패 문제를 처리합니다.

### MapReduce 및 YARN
다양한 소스에서 구조화되지 않은 데이터를 분석할 때 복잡성을 단순화하기 위해 MapReduce 프로그래밍 모델은 map 및 reduce 작업의 끝 부분에 쓰는 코어 추상화 계층을 제공합니다. MapReduce 프로그래밍 모델은 모든 작업을 키-값 쌍으로 구성된 데이터 집합에 대한 계산으로 봅니다. 따라서 입력 및 출력 파일은 둘 다 키-값 쌍으로만 구성된 데이터 집합을 포함해야 합니다. 이 제약 조건에서 주요한 내용은 MapReduce 작업은 결과적으로 구성 가능하다는 점입니다.

Pig 및 Hive와 같은 다른 Hadoop 관련 프로젝트는 HDFS 및 MapReduce 프레임워크를 기반으로 구축됩니다. 이러한 프로젝트는 클러스터를 관리하는 데 MapReduce 프로그램으로 직접 작업하는 것보다 더 간단한 방법을 제공합니다. 예를 들어 Pig를 통해 클러스터에서 MapReduce 프로그램으로 컴파일되는 Pig Latin이라는 절차형 언어를 사용하여 프로그램을 작성할 수 있습니다. 데이터 흐름 관리도 유연하게 제어합니다. Hive는 HiveQL이라는 선언형 언어로 SQL과 유사한 문을 사용하여 쿼리될 수 있는 클러스터에 저장된 파일의 데이터에 대해 테이블 추상화 계층을 제공하는 데이터 웨어하우스 인프라입니다.

### HDInsight
Azure HDInsight는 클라우드에서 Apache Hadoop을 서비스로 사용할 수 있게 해줍니다. HDFS/MapReduce 소프트웨어 프레임워크와 Pig, Hive 및 Oozie와 같은 관련 프로젝트를 더 간단하고 확장 가능하며 비용 효율적인 환경에서 사용할 수 있게 해줍니다.

HDInsight에서 배포한 Hadoop 클러스터에는 서비스 가용성을 높이기 위해 두 번째 헤드 노드가 추가되었습니다. 일반적으로 표준 방식으로 구현된 Hadoop 클러스터에는 헤드 노드가 1개뿐입니다. HDInsight는 보조 헤드 노드를 추가하여 이러한 단일 오류 지점을 없앱니다. 새로운 HA 클러스터 구성으로 전환해도 고객이 훨씬 더 큰 헤드 노드로 클러스터를 프로비전하지 않는다면 클러스터 가격은 달라지지 않습니다.

HDInsight에서 소개한 주요 효율성 중 하나는 데이터를 관리 및 저장하는 방법입니다. HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. Blob 저장소와 HDFS는 데이터에서의 계산 및 데이터 저장소에 대해 각각 최적화되는 별개의 파일 시스템입니다.

- Azure Blob 저장소는 HDInsight를 사용하여 처리할 데이터에 뛰어난 확장성과 가용성, 낮은 비용, 대용량 및 공유 가능한 저장소 옵션을 제공합니다.
- HDFS에서 HDInsight가 배포한 Hadoop 클러스터는 데이터에서 MapReduce 전산 작업을 실행하도록 최적화됩니다.

HDInsight 클러스터는 MapReduce 작업을 실행하도록 컴퓨터 노드의 Azure에서 배포되며 이러한 작업이 완료되면 사용자가 삭제할 수 있습니다. 계산이 완료된 후 HDFS 클러스터에 데이터를 유지하면 데이터 저장에 비용이 많이 들 수 있습니다. Blob 저장소는 강력한 범용 Azure 저장소 솔루션입니다. 따라서 데이터를 Blob 저장소에 저장하면 사용자 데이터의 손실 없이 계산에 사용되는 클러스터를 안전하게 삭제할 수 있습니다. 하지만 Blob 저장소는 경제적인 솔루션만은 아닙니다. 관리하는 데이터에서 바로 작동하기 위해(기본값) Hadoop 에코시스템에서 전체 구성 요소를 사용하도록 설정하여 고객에게 원활한 환경을 제공하는 완전한 기능을 갖춘 HDFS 파일 시스템 인터페이스를 제공합니다.

HDInsight는 Azure PowerShell을 사용하여 Hadoop 작업을 구성, 실행 및 후처리합니다. 또한 HDInsight는 데이터를 Azure SQL 데이터베이스에서 HDFS로 가져오거나 HDFS에서 Azure SQL 데이터베이스로 내보내는 데 사용할 수 있는 Sqoop 커넥터도 제공합니다.

HDInsight는 YARN도 사용할 수 있도록 합니다. 기존 Apache Hadoop MapReduce 프레임워크를 대체하여 Hadoop 클러스터에서 데이터를 처리하는 새로운 범용 분산 응용 프로그램 관리 프레임워크. Hadoop 운영 체제의 역할을 효과적으로 하며, 일괄 처리를 위한 일회용 데이터 플랫폼에서 일괄, 대화형, 온라인 및 스트림 처리를 가능하게 하는 다중 사용 플랫폼으로 Hadoop을 전환시켜 줍니다. 이 새로운 관리 프레임워크는 용량 보증, 공정성 및 서비스 수준 계약과 같은 기준에 따라 확장성 및 클러스터 사용률을 개선합니다.

Microsoft Excel용 파워 쿼리는 데이터를 Azure HDInsight 또는 HDFS에서 Excel로 가져오는 데 사용할 수 있습니다. 이 추가 기능은 광범위한 데이터 소스에 대한 데이터 검색 및 액세스를 간소화하여 셀프 서비스 BI 환경을 향상시킵니다. 파워 쿼리뿐만 아니라 Microsoft Hive ODBC 드라이버는 종단 간 데이터 분석을 쉽게 하고 간소화하여 Excel, SQL Server Analysis Services 및 Reporting Services와 같은 BI(비즈니스 인텔리전스) 도구를 통합하는 데 사용할 수 있습니다.

### 개요
이 항목은 HDInsight에서 지원하는 Hadoop 에코시스템, HDInsight를 위한 주요 사용 시나리오 및 추가 리소스에 대한 가이드를 설명합니다. 다음 섹션이 포함되어 있습니다.

 * [HDInsight의 Hadoop 에코시스템][HDInsight의 Hadoop 에코시스템]</a>: HDInsight는 Pig, Hive, Sqoop, Oozie 및 Ambari 구현을 제공하고, 파워 쿼리나 Microsoft Hive ODBC 드라이버를 사용하여 Blob 저장소/HDFS 및 MapReduce 프레임워크와 통합된 Excel, SQL Server Analysis Services 및 Reporting Services와 같은 다른 BI 도구를 지원합니다. 이 섹션에서는 Hadoop 에코시스템의 이러한 프로그램이 처리하도록 설계된 작업에 대해 설명합니다.

 * [HDInsight용 빅데이터 시나리오][HDInsight용 빅데이터 시나리오]: 이 섹션에서는 HDInsight 기술이 적합한 작업 유형에 대한 질문을 처리합니다.

 * [HDInsight용 리소스][HDInsight용 리소스]: 이 섹션에서는 추가 정보에 대한 관련 리소스를 찾을 수 있는 위치를 표시합니다.


<h2 id="ecosystem"> <a name="Ecosystem">Azure의 Hadoop 에코시스템 </a></h2>

### 소개

HDInsight는 빅데이터를 처리하기 위해 Microsoft의 클라우드 기반 솔루션을 구현하는 프레임워크를 제공합니다. 이 페더레이션 에코시스템은 MapReduce 프로그래밍 모델의 병렬 처리 기능을 활용하여 대용량의 데이터를 관리하고 분석합니다. HDInsight와 함께 사용할 수 있는 Apache 호환 Hadoop 기술이 이 섹션에서 항목별로 간략하게 설명되어 있습니다.

HDInsight는 데이터 처리와 웨어하우징 기능을 통합하기 위해 Hive 및 Pig 구현을 제공합니다. Microsoft의 빅데이터 솔루션은 SQL Server Analysis Services, Reporting Services, PowerPivot 및 Excel과 같은 Microsoft의 BI 도구와 통합됩니다. 이를 통해 Blob 저장소의 HDInsight가 저장하고 관리하는 데이터에서 간단한 BI를 수행할 수 있습니다.

Hadoop 에코시스템의 일부이며 Hadoop 클러스터 상위에서 실행되도록 구축된 다른 Apache 호환 기술 및 자매 기술도 다운로드하여 HDInsight와 함께 사용할 수 있습니다. 여기에는 HDFS를 관계형 데이터 저장소와 통합하는 Sqoop과 같은 오픈 소스 기술이 포함됩니다.

### Pig

Pig는 Hadoop 클러스터에서 빅데이터를 처리하기 위한 고급 플랫폼입니다. Pig는 Pig Latin이라는 데이터 흐름 언어로 구성되며, 큰 데이터 집합에서 쿼리를 작성하고 콘솔에서 프로그램을 실행하는 실행 환경을 지원합니다. Pig Latin 프로그램은 MapReduce 프로그램 계열로 변환된 데이터 집합 변환 계열로 구성되어 있습니다. Pig Latin 추상화 계층은 MapReduce보다 풍부한 데이터 구조를 제공하며, SQL이 RDBMS(관계형 데이터베이스 관리 시스템)에 대해 수행하는 Hadoop에 대해 수행합니다. Pig Latin은 완전히 확장 가능합니다. 분석을 작성할 때 각 처리 경로 단계를 사용자 지정하도록 Java, Python, Ruby, C#, JavaScript 등으로 작성된 UDF(사용자 정의 함수)를 호출할 수 있습니다. 자세한 내용은 [Apache Pig 시작][Apache Pig 시작](영문)을 참조하세요.

### Hive

Hive는 HDFS에 저장된 데이터를 관리하는 분산 데이터 웨어하우스이며, Hadoop 쿼리 엔진입니다. Hive는 강력한 SQL 기술을 가진 분석가에게 SQL과 유사한 인터페이스 및 관계형 데이터 모델을 제공합니다. Hive는 일종의 SQL인 HiveQL이라는 언어를 사용합니다. Pig처럼 Hive도 MapReduce 상위의 추상화 계층이며, 실행 시 쿼리를 MapReduce 작업 계열로 변환합니다. Hive용 시나리오는 RDBMS 시나리오 개념에 더 가까우므로 좀더 구조화된 데이터의 사용에 적합합니다. 구조화되지 않은 데이터의 경우에는 Pig가 더 적합합니다. 자세한 내용은 [Apache Hive 시작][Apache Hive 시작](영문)을 참조하세요.

### Sqoop

Sqoop은 Hadoop과 관계형 데이터베이스(예: SQL) 또는 구조화된 다른 데이터 저장소 간에 대량 데이터를 최대한 효율적으로 전송하는 도구입니다. Sqoop을 사용하여 데이터를 외부 구조화된 데이터 저장소에서 HDFS 또는 Hive와 같은 관계형 시스템으로 가져옵니다. 또한 Sqoop은 Hadoop에서 데이터를 추출하여 추출한 데이터를 외부 관계형 데이터베이스, 엔터프라이즈 데이터 웨어하우스 또는 다른 구조화된 데이터 저장소 형식으로 내보낼 수도 있습니다. 자세한 내용은 [Apache Sqoop][Apache Sqoop](영문) 웹 사이트를 참조하세요.

### Oozie

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/조정 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive 및 Apache Sqoop용 Hadoop 작업을 지원합니다. Java 프로그램이나 셸 스크립트와 같이 시스템에 특정한 작업을 예약하는 데에도 사용할 수 있습니다.

### Ambari

Apache Ambari는 Apache Hadoop 클러스터를 프로비전하고 관리 및 모니터링합니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. API에 대한 자세한 내용은 [Ambari API 참조][Ambari API 참조](영문)를 참조하세요. HDInsight는 현재 Ambari 모니터링 기능만 지원합니다. Ambari API 버전 1.0은 HDInsight 클러스터 버전 2.1 및 3.0에서 지원됩니다. Ambari에 대한 자세한 내용은 [Apache Ambari][Apache Ambari](영문) 웹 사이트를 참조하세요.


### Microsoft Avro 라이브러리

Microsoft Avro 라이브러리는 Microsoft.NET 환경을 위한 Apache Avro 데이터 직렬화 시스템을 구현합니다. Apache Avro는 직렬화를 위한 압축 이진 데이터 교환 형식을 제공합니다. 또한 [JSON][JSON]을 사용하여 언어 상호 운용성을 따르는 언어 중립적 스키마를 정의합니다. 한 언어로 직렬화된 데이터는 다른 언어로 읽을 수 있습니다. 현재 C, C++, C#, Java, PHP, Python 및 Ruby가 지원됩니다. 이 형식에 대한 자세한 내용은 [Apache Avro 사양][Apache Avro 사양](영문)에서 확인할 수 있습니다. 현재 Microsoft Avro 라이브러리 버전에서는 이 사양의 RPC(원격 프로시저 호출)를 지원하지 않습니다.

Apache Avro 직렬화 형식은 Azure HDInsight 및 기타 Apache Hadoop 환경에서 널리 사용됩니다. Avro는 Hadoop MapReduce 작업 내에서 복잡한 데이터 구조를 나타내는 편리한 방법을 제공합니다. Avro 파일의 형식은 분산 MapReduce 프로그래밍 모델을 지원하도록 디자인되었습니다. 이러한 분산을 가능하게 하는 핵심 기능은 파일이 “분할 가능"하여 파일의 임의 지점을 찾고 특정 블록부터 읽기 시작할 수 있다는 데 있습니다. 자세한 내용은 [Microsoft Avro 라이브러리로 데이터 직렬화][Microsoft Avro 라이브러리로 데이터 직렬화](영문)를 참조하세요.

### 비즈니스 인텔리전스 도구 및 커넥터

익숙한 BI(비즈니스 인텔리전스) 도구(예: Excel, PowerPivot, SQL Server Analysis Services 및 Reporting Services)는 파워 쿼리 추가 기능이나 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight와 통합된 데이터를 가져오고 분석하고 보고합니다.

 * Microsoft Excel용 파워 쿼리는 [Microsoft 다운로드 센터][Microsoft 다운로드 센터]에서 다운로드할 수 있습니다.

 * Microsoft Hive ODBC 드라이버는 이 [다운로드 사이트][다운로드 사이트](영문)에서 다운로드할 수 있습니다.

 * Analysis Services에 대한 내용은 [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services]를 참조하세요.

 * Reporting Services에 대한 내용은 [SQL Server 2012 Reporting][SQL Server 2012 Reporting]을 참조하세요.


<h2> <a name="Scenarios"></a>HDInsight용 빅데이터 시나리오</h2>
HDInsight 사용 사례를 제공하는 모범 시나리오는 애드혹 분석이고 일괄 방식으로 되어 있으며 Azure 노드에 저장된 구조화되지 않은 모든 데이터 집합으로서 업데이트를 자주할 필요는 없습니다.

이러한 조건은 비즈니스, 과학 및 거버넌스의 다양한 활동에 적용됩니다. 예를 들어 여기에는 소매 분야의 공급망 모니터링, 의심스러운 재정 거래 형태, 공공 설비 및 서비스의 요구 형태, 환경 감지 장치의 대기 및 수질, 수도권의 범죄 형태 등이 포함됩니다.

HDInsight(및 주로 Hadoop 기술)는 일단 작성된 후에는 자주 업데이트할 필요는 없지만 자주 읽혀지는 많은 양의 로깅되거나 보관된 데이터를 일반적으로 완전히 분석하도록 처리하는 데 가장 적합합니다. 이 시나리오는 데이터가 더 적게 필요하고(페타바이트 대신 기가바이트) 전체 데이터 집합에서 특정 데이터 요소에 대해 끊임없이 업데이트하고 쿼리해야 하는 RDBMS에 의해 더 적절하게 처리되는 데이터와 상호 보완합니다. RDBMS는 고정 스키마에 따라 구성되고 저장된 구조화된 데이터와 가장 잘 작동합니다. MapReduce는 데이터가 처리될 때 해당 데이터를 해석할 수 있으므로 스키마가 미리 정의되지 않았으며 구조화되지 않은 데이터와 잘 작동합니다.

<h2> <a name="Resources"></a>다음 단계: HDInsight용 리소스</h2>
**Microsoft: HDInsight**

* [HDInsight 설명서][HDInsight 설명서]: 문서, 비디오 및 추가 리소스에 대한 링크가 있는 Azure HDInsight용 설명서 페이지입니다.

* [HDInsight 릴리스 정보][HDInsight 릴리스 정보](영문): 최신 릴리스에 대한 참고 사항입니다.

* [Azure HDInsight 시작][Azure HDInsight 시작]: HDInsight를 빠르게 시작할 수 있는 자습서입니다.

* [HDInsight 샘플 실행][HDInsight 샘플 실행]: HDInsight와 함께 제공된 샘플을 실행하는 방법에 대한 자습서입니다.

* [빅데이터 및 Azure][빅데이터 및 Azure]: Azure로 구축할 수 있는 것을 보여 주는 빅데이터 시나리오입니다.

* [Azure HDInsight SDK][Azure HDInsight SDK]: HDinsight SDK의 참조 설명서입니다.

**Microsoft: Windows 및 SQL 데이터베이스**

* [Azure 홈페이지][Azure 홈페이지]: 응용 프로그램을 구축하는 데 필요한 시나리오, 무료 평가판 등록, 개발 도구 및 설명서입니다.

* [Azure SQL 데이터베이스][Azure SQL 데이터베이스]: SQL 데이터베이스용 MSDN 설명서입니다.

* [SQL 데이터베이스용 관리 포털][SQL 데이터베이스용 관리 포털]: 클라우드에서 SQL 데이터베이스를 관리하기 위한 간단하고 사용하기 쉬운 데이터베이스 관리 도구입니다.

* [SQL 데이터베이스용 Adventure Works][SQL 데이터베이스용 Adventure Works]: SQL 데이터베이스 샘플 데이터베이스를 위한 다운로드 페이지입니다.

**Microsoft: 비즈니스 인텔리전스**

* [HDInsight에 파워 쿼리로 Excel 연결][HDInsight에 파워 쿼리로 Excel 연결]: HDInsight 클러스터와 연결된 데이터를 저장하는 Azure 저장소 계정에 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 연결하는 방법을 알아 봅니다.

* [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결]: Microsoft Hive ODBC 드라이버로 데이터를 Azure HDInsight에서 가져오는 방법을 알아 봅니다.

* [Microsoft BI PowerPivot][Microsoft BI PowerPivot]: 강력한 데이터 매시업 및 탐색 도구에 대한 정보를 다운로드하여 가져옵니다.

* [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services]: SQL Server 2012 평가판을 다운로드하고 실행 가능한 의견을 제공하는 포괄적이고 엔터프라이즈급인 분석 솔루션을 구축하는 방법을 알아 봅니다.

* [SQL Server 2012 Reporting][1]: SQL Server 2012 평가판을 다운로드하고 엔터프라이즈 전체에서 실시간 의사 결정이 이루어질 수 있도록 하는 포괄적이고 확장성이 뛰어난 솔루션을 만드는 방법을 알아 봅니다.

**Apache Hadoop**:

* [Apache Hadoop][Apache Hadoop]: 전체 컴퓨터의 클러스터에 대해 큰 데이터 집합을 분산 처리할 수 있는 프레임워크인 Apache Hadoop 소프트웨어 라이브러리에 대해 자세히 알아 봅니다.

* [HDFS][HDFS]: Hadoop 응용 프로그램이 사용하는 주 저장 시스템인 HDFS(Hadoop Distributed File System)의 아키텍처 및 설계에 대해 자세히 알아 봅니다.

* [MapReduce][MapReduce]: 컴퓨터 노드의 대형 클러스터에서 엄청난 양의 데이터를 병렬로 빠르게 처리하는 Hadoop 응용 프로그램을 작성하기 위한 프로그래밍 프레임워크에 대해 자세히 알아 봅니다.

  [HDInsight의 Hadoop 에코시스템]: #Ecosystem
  [HDInsight용 빅데이터 시나리오]: #Scenarios
  [HDInsight용 리소스]: #Resources
  [Apache Pig 시작]: http://pig.apache.org/
  [Apache Hive 시작]: http://hive.apache.org/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Ambari API 참조]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Apache Ambari]: http://ambari.apache.org/
  [JSON]: http://www.json.org
  [Apache Avro 사양]: http://avro.apache.org/docs/current/spec.html
  [Microsoft Avro 라이브러리로 데이터 직렬화]: ../hdinsight-dotnet-avro-serialization/
  [Microsoft 다운로드 센터]: http://go.microsoft.com/fwlink/?LinkID=286689
  [다운로드 사이트]: http://go.microsoft.com/fwlink/?LinkID=286698
  [SQL Server 2012 Analysis Services]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx
  [SQL Server 2012 Reporting]: http://www.microsoft.com/ko-kr/sqlserver/solutions-technologies/business-intelligence/reporting.aspx
  [HDInsight 설명서]: http://go.microsoft.com/fwlink/?LinkID=285601
  [HDInsight 릴리스 정보]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-release-notes/
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 샘플 실행]: ../hdinsight-run-samples/
  [빅데이터 및 Azure]: http://azure.microsoft.com/ko-kr/solutions/big-data/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
  [Azure 홈페이지]: http://azure.microsoft.com/ko-kr/
  [Azure SQL 데이터베이스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336279.aspx
  [SQL 데이터베이스용 관리 포털]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg442309.aspx
  [SQL 데이터베이스용 Adventure Works]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [HDInsight에 파워 쿼리로 Excel 연결]: ../hdinsight-connect-excel-power-query/
  [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI PowerPivot]: http://www.microsoft.com/ko-kr/bi/PowerPivot.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDFS]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [MapReduce]: http://mapreduce.org/
 
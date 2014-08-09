<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Azure HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Azure HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Azure HDInsight 소개
====================

개요
----

Azure HDInsight는 빅데이터에서 관리, 분석 및 보고하도록 설계된 소프트웨어 프레임워크를 제공하여 클라우드에서 Apache™ Hadoop® 클러스터를 배포하고 프로비전하는 서비스입니다.

### 빅데이터

데이터는 계속 커지는 데이터 양, 증가하는 빠른 속도, 구조화되지 않은 형식 및 다양한 맥락의 폭넓은 다양성에서 수집되므로 "빅데이터"라고 표현합니다. 빅데이터는 수집만으로는 엔터프라이즈에 가치가 제공되지 않습니다. 실행 가능한 인텔리전스 또는 의견 형식으로 가치를 제공하기 위해 빅데이터에 대해 올바른 질문을 해야 하고 문제와 관련된 데이터가 수집되어야 할 뿐만 아니라 해당 데이터를 액세스, 정리, 분석한 후 이제 매시업으로 참조될 수 있는 관점 및 맥락을 만드는 다양한 다른 소스의 데이터와 함께 유용한 방법으로 데이터를 제공해야 합니다.

### Apache Hadoop

Apache Hadoop은 빅데이터를 쉽게 관리하고 분석하게 해주는 소프트웨어 프레임워크입니다. Apache Hadoop 코어는 신뢰할 만한 데이터 저장소에 HDFS(Hadoop Distributed File System) 및 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다. HDFS는 데이터 복제를 사용하여 고도의 분산 시스템을 배포할 때 발생하는 하드웨어 실패 문제를 처리합니다.

### MapReduce

다양한 소스에서 구조화되지 않은 데이터를 분석할 때 복잡성을 단순화하기 위해 MapReduce 프로그래밍 모델은 map 및 reduce 작업의 끝 부분에 쓰는 코어 추상화 계층을 제공합니다. MapReduce 프로그래밍 모델은 모든 작업을 키-값 쌍으로 구성된 데이터 집합에 대한 계산으로 봅니다. 따라서 입력 및 출력 파일은 둘 다 키-값 쌍으로만 구성된 데이터 집합을 포함해야 합니다. 이 제약 조건에서 주요한 내용은 MapReduce 작업은 결과적으로 구성 가능하다는 점입니다.

Pig 및 Hive와 같은 다른 Hadoop 관련 프로젝트는 HDFS 및 MapReduce 프레임워크를 기반으로 구축됩니다. 이러한 프로젝트는 클러스터를 관리하는 데 MapReduce 프로그램으로 직접 작업하는 것보다 더 간단한 방법을 제공합니다. 예를 들어 Pig를 통해 클러스터에서 MapReduce 프로그램으로 컴파일되는 Pig Latin이라는 절차형 언어를 사용하여 프로그램을 작성할 수 있습니다. 데이터 흐름 관리도 유연하게 제어합니다. Hive는 HiveQL이라는 선언형 언어로 SQL과 유사한 문을 사용하여 쿼리될 수 있는 클러스터에 저장된 파일의 데이터에 대해 테이블 추상화 계층을 제공하는 데이터 웨어하우스 인프라입니다.

### HDInsight

Azure HDInsight는 클라우드에서 Apache Hadoop을 서비스로 사용할 수 있게 해줍니다. HDFS/MapReduce 소프트웨어 프레임워크와 Pig 및 Hive와 같은 관련 프로젝트를 더 간단하고 확장 가능하며 비용 효율적인 환경에서 사용할 수 있게 해줍니다.

HDInsight에서 소개한 주요 효율성 중 하나는 데이터를 관리 및 저장하는 방법입니다. HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. Blob 저장소와 HDFS는 데이터에서의 계산 및 데이터 저장소에 대해 각각 최적화되는 별개의 파일 시스템입니다.

-   Azure Blob 저장소는 HDInsight를 사용하여 처리할 데이터에 뛰어난 확장성과 가용성, 낮은 비용, 대용량 및 공유 가능한 저장소 옵션을 제공합니다.
-   HDFS에서 HDInsight가 배포한 Hadoop 클러스터는 데이터에서 MapReduce 전산 작업을 실행하도록 최적화됩니다.

HDInsight 클러스터는 MapReduce 작업을 실행하도록 컴퓨터 노드의 Azure에서 배포되며 이러한 작업이 완료되면 사용자가 삭제할 수 있습니다. 계산을 완료한 후 HDFS 클러스터에 데이터를 보관하면 이 데이터를 저장하는 데 비용이 많이 듭니다. Blob 저장소는 강력하고 일반적인 용도의 Azure 저장소 솔루션입니다. 따라서 데이터를 Blob 저장소에 저장하면 사용자 데이터의 손실 없이 계산에 사용되는 클러스터를 안전하게 삭제할 수 있습니다. 하지만 Blob 저장소는 경제적인 솔루션만은 아닙니다. 관리하는 데이터에서 바로 작동하기 위해(기본값) Hadoop 에코시스템에서 전체 구성 요소를 사용하도록 설정하여 고객에게 원활한 환경을 제공하는 완전한 기능을 갖춘 HDFS 파일 시스템 인터페이스를 제공합니다.

HDInsight는 Azure PowerShell을 사용하여 Hadoop 작업을 구성, 실행 및 후처리합니다. 또한 HDInsight는 데이터를 Azure SQL 데이터베이스에서 HDFS로 가져오거나 HDFS에서 Azure SQL 데이터베이스로 내보내는 데 사용할 수 있는 Sqoop 커넥터도 제공합니다.

Microsoft Excel용 파워 쿼리는 데이터를 Azure HDInsight 또는 HDFS에서 Excel로 가져오는 데 사용할 수 있습니다. 이 추가 기능은 광범위한 데이터 소스에 대한 데이터 검색 및 액세스를 간소화하여 셀프 서비스 BI 환경을 향상시킵니다. 파워 쿼리뿐만 아니라 Microsoft Hive ODBC 드라이버는 종단 간 데이터 분석을 쉽게 하고 간소화하여 Excel, SQL Server Analysis Services 및 Reporting Services와 같은 BI(비즈니스 인텔리전스) 도구를 통합하는 데 사용할 수 있습니다.

### 개요

이 항목은 HDInsight에서 지원하는 Hadoop 에코시스템, HDInsight를 위한 주요 사용 시나리오 및 추가 리소스에 대한 가이드를 설명합니다. 다음 섹션이 포함되어 있습니다.

-   [HDInsight의 Hadoop 에코시스템](#Ecosystem): HDInsight는 Pig, Hive 및 Sqoop 구현을 제공하고, 파워 쿼리나 Microsoft Hive ODBC 드라이버를 사용하여 Blob 저장소/HDFS 및 MapReduce 프레임워크와 통합된 Excel, SQL Server Analysis Services 및 Reporting Services와 같은 다른 BI 도구를 지원합니다. 이 섹션에서는 Hadoop 에코시스템의 이러한 프로그램이 처리하도록 설계된 작업에 대해 설명합니다.

-   [HDInsight용 빅데이터 시나리오](#Scenarios): 이 섹션에서는 HDInsight 기술이 적합한 작업 유형에 대한 질문을 처리합니다.

-   [HDInsight용 리소스](#Resources): 이 섹션에서는 추가 정보에 대한 관련 리소스를 찾을 수 있는 위치를 표시합니다.


<h2 id="ecosystem"> <a name="Ecosystem">Azure의 Hadoop 에코시스템 </a></h2>

### 소개

HDInsight는 빅데이터를 처리하기 위해 Microsoft의 클라우드 기반 솔루션을 구현하는 프레임워크를 제공합니다. 이 페더레이션 에코시스템은 MapReduce 프로그래밍 모델의 병렬 처리 기능을 활용하여 대용량의 데이터를 관리하고 분석합니다. HDInsight와 함께 사용할 수 있는 Apache 호환 Hadoop 기술이 이 섹션에서 항목별로 간략하게 설명되어 있습니다.

HDInsight는 데이터 처리와 웨어하우징 기능을 통합하기 위해 Hive 및 Pig 구현을 제공합니다. Microsoft의 빅데이터 솔루션은 SQL Server Analysis Services, Reporting Services, PowerPivot 및 Excel과 같은 Microsoft의 BI 도구와 통합됩니다. 이를 통해 Blob 저장소의 HDInsight가 저장하고 관리하는 데이터에서 간단한 BI를 수행할 수 있습니다.

Hadoop 에코시스템의 일부이며 Hadoop 클러스터 상위에서 실행되도록 구축된 다른 Apache 호환 기술 및 자매 기술도 다운로드하여 HDInsight와 함께 사용할 수 있습니다. 여기에는 HDFS를 관계형 데이터 저장소와 통합하는 Sqoop과 같은 오픈 소스 기술이 포함됩니다.

### Pig

Pig는 Hadoop 클러스터에서 빅데이터를 처리하기 위한 고급 플랫폼입니다. Pig는 Pig Latin이라는 데이터 흐름 언어로 구성되며, 큰 데이터 집합에서 쿼리를 작성하고 콘솔에서 프로그램을 실행하는 실행 환경을 지원합니다. Pig Latin 프로그램은 MapReduce 프로그램 계열로 변환된 데이터 집합 변환 계열로 구성되어 있습니다. Pig Latin 추상화 계층은 MapReduce보다 풍부한 데이터 구조를 제공하며, SQL이 RDBMS(관계형 데이터베이스 관리 시스템)에 대해 수행하는 Hadoop에 대해 수행합니다. Pig Latin은 완전히 확장 가능합니다. 분석을 작성할 때 각 처리 경로 단계를 사용자 지정하도록 Java, Python, Ruby, C\#, JavaScript 등으로 작성된 UDF(사용자 정의 함수)를 호출할 수 있습니다. 자세한 내용은 [Apache Pig 시작](http://pig.apache.org/)(영문)을 참조하십시오.

### Hive

Hive는 HDFS에 저장된 데이터를 관리하는 분산 데이터 웨어하우스이며, Hadoop 쿼리 엔진입니다. Hive는 강력한 SQL 기술을 가진 분석가에게 SQL과 유사한 인터페이스 및 관계형 데이터 모델을 제공합니다. Hive는 일종의 SQL인 HiveQL이라는 언어를 사용합니다. Pig처럼 Hive도 MapReduce 상위의 추상화 계층이며, 실행 시 쿼리를 MapReduce 작업 계열로 변환합니다. Hive용 시나리오는 RDBMS 시나리오 개념에 더 가까우므로 좀더 구조화된 데이터의 사용에 적합합니다. 구조화되지 않은 데이터의 경우에는 Pig가 더 적합합니다. 자세한 내용은 [Apache Hive 시작](http://hive.apache.org/)(영문)을 참조하십시오.

### Sqoop

Sqoop은 Hadoop과 관계형 데이터베이스(예: SQL) 또는 구조화된 다른 데이터 저장소 간에 대량 데이터를 최대한 효율적으로 전송하는 도구입니다. Sqoop을 사용하여 데이터를 외부 구조화된 데이터 저장소에서 HDFS 또는 Hive와 같은 관계형 시스템으로 가져옵니다. 또한 Sqoop은 Hadoop에서 데이터를 추출하여 추출한 데이터를 외부 관계형 데이터베이스, 엔터프라이즈 데이터 웨어하우스 또는 다른 구조화된 데이터 저장소 형식으로 내보낼 수도 있습니다. 자세한 내용은 [Apache Sqoop](http://sqoop.apache.org/)(영문) 웹 사이트를 참조하십시오.

### 비즈니스 인텔리전스 도구 및 커넥터

익숙한 BI(비즈니스 인텔리전스) 도구(예: Excel, PowerPivot, SQL Server Analysis Services 및 Reporting Services)는 파워 쿼리 추가 기능이나 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight와 통합된 데이터를 가져오고 분석하고 보고합니다.

-   Microsoft Excel용 파워 쿼리는 [Microsoft 다운로드 센터](http://go.microsoft.com/fwlink/?LinkID=286689)에서 다운로드할 수 있습니다.

-   Microsoft Hive ODBC 드라이버는 이 [다운로드 사이트](http://go.microsoft.com/fwlink/?LinkID=286698)(영문)에서 다운로드할 수 있습니다.

-   Analysis Services에 대한 내용은 [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx)를 참조하십시오.

-   Reporting Services에 대한 내용은 [SQL Server 2012 Reporting](http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx)을 참조하십시오.

HDInsight용 빅데이터 시나리오
-----------------------------

HDInsight 사용 사례를 제공하는 모범 시나리오는 애드혹 분석이고 일괄 방식으로 되어 있으며 Azure 노드에 저장된 구조화되지 않은 모든 데이터 집합으로서 업데이트를 자주할 필요는 없습니다.

이러한 조건은 비즈니스, 과학 및 거버넌스의 다양한 활동에 적용됩니다. 예를 들어 여기에는 소매 분야의 공급망 모니터링, 의심스러운 재정 거래 형태, 공공 설비 및 서비스의 요구 형태, 환경 감지 장치의 대기 및 수질, 수도권의 범죄 형태 등이 포함됩니다.

HDInsight(및 주로 Hadoop 기술)는 일단 작성된 후에는 자주 업데이트할 필요는 없지만 자주 읽혀지는 많은 양의 로깅되거나 보관된 데이터를 일반적으로 완전히 분석하도록 처리하는 데 가장 적합합니다. 이 시나리오는 데이터가 더 적게 필요하고(페타바이트 대신 기가바이트) 전체 데이터 집합에서 특정 데이터 요소에 대해 끊임없이 업데이트하고 쿼리해야 하는 RDBMS에 의해 더 적절하게 처리되는 데이터와 상호 보완합니다. RDBMS는 고정 스키마에 따라 구성되고 저장된 구조화된 데이터와 가장 잘 작동합니다. MapReduce는 데이터가 처리될 때 해당 데이터를 해석할 수 있으므로 스키마가 미리 정의되지 않았으며 구조화되지 않은 데이터와 잘 작동합니다.

다음 단계: HDInsight용 리소스
-----------------------------

**Microsoft: HDInsight**

-   [HDInsight 설명서](http://go.microsoft.com/fwlink/?LinkID=285601): 문서, 비디오 및 추가 리소스에 대한 링크가 있는 Azure HDInsight용 설명서 페이지입니다.

-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/): HDInsight를 빠르게 시작할 수 있는 자습서입니다.

-   [HDInsight 샘플 실행](/en-us/manage/services/hdinsight/howto-run-samples/): HDInsight와 함께 제공된 샘플을 실행하는 방법에 대한 자습서입니다.

-   [빅데이터 및 Azure](http://www.windowsazure.com/ko-kr/home/scenarios/big-data/): Azure로 구축할 수 있는 것을 보여 주는 빅데이터 시나리오입니다.

-   [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx): HDinsight SDK의 참조 설명서입니다.

**Microsoft: Windows 및 SQL 데이터베이스**

-   [Azure 홈페이지](https://www.windowsazure.com/en-us/): 응용 프로그램을 구축하는 데 필요한 시나리오, 무료 평가판 등록, 개발 도구 및 설명서입니다.

-   [Azure SQL 데이터베이스](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336279.aspx): SQL 데이터베이스용 MSDN 설명서입니다.

-   [SQL 데이터베이스용 관리 포털](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg442309.aspx): 클라우드에서 SQL 데이터베이스를 관리하기 위한 간단하고 사용하기 쉬운 데이터베이스 관리 도구입니다.

-   [SQL 데이터베이스용 Adventure Works](http://msftdbprodsamples.codeplex.com/releases/view/37304): SQL 데이터베이스 샘플 데이터베이스를 위한 다운로드 페이지입니다.

**Microsoft: 비즈니스 인텔리전스**

-   [HDInsight에 파워 쿼리로 Excel 연결](/en-us/manage/services/hdinsight/connect-excel-with-power-query/): HDInsight 클러스터와 연결된 데이터를 저장하는 Azure 저장소 계정에 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 연결하는 방법을 알아 봅니다.

-   [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/): Microsoft Hive ODBC 드라이버로 데이터를 Azure HDInsight에서 가져오는 방법을 알아 봅니다.

-   [Microsoft BI PowerPivot](http://www.microsoft.com/en-us/bi/PowerPivot.aspx): 강력한 데이터 매시업 및 탐색 도구에 대한 정보를 다운로드하여 가져옵니다.

-   [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx): SQL Server 2012 평가판을 다운로드하고 실행 가능한 의견을 제공하는 포괄적이고 엔터프라이즈급인 분석 솔루션을 구축하는 방법을 알아 봅니다.

-   [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx): SQL Server 2012 평가판을 다운로드하고 엔터프라이즈 전체에서 실시간 의사 결정이 이루어질 수 있도록 하는 포괄적이고 확장성이 뛰어난 솔루션을 만드는 방법을 알아 봅니다.

**Apache Hadoop**:

-   [Apache Hadoop](http://hadoop.apache.org/): 전체 컴퓨터의 클러스터에 대해 큰 데이터 집합을 분산 처리할 수 있는 프레임워크인 Apache Hadoop 소프트웨어 라이브러리에 대해 자세히 알아 봅니다.

-   [HDFS](http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html): Hadoop 응용 프로그램이 사용하는 주 저장 시스템인 HDFS(Hadoop Distributed File System)의 아키텍처 및 설계에 대해 자세히 알아 봅니다.

-   [MapReduce](http://mapreduce.org/): 컴퓨터 노드의 대형 클러스터에서 엄청난 양의 데이터를 병렬로 빠르게 처리하는 Hadoop 응용 프로그램을 작성하기 위한 프로그래밍 프레임워크에 대해 자세히 알아 봅니다.



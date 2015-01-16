<properties urlDisplayName="HDInsight Hadoop Version" pageTitle="HDInsight의 Hadoop 클러스터 버전 내 새로운 기능 | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 이 문서에서는 지원되는 Hadoop 및 HDP(HortonWorks Data Platform) 배포 버전에 대해 알아봅니다." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="bradsev" />


#HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능

##HDInsight 버전
HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포에 포함된 구성 요소 집합이 프로비전됩니다. 각 HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 항목별로 구분되어 있습니다. Azure HDInsight에서 사용하는 기본 클러스터 버전은 현재 3.1이고, 2014년 11월 7일 기준 HDP 2.1.7에 기반을 둡니다.


<table border="1">
<tr><th>구성 요소</th><th>HDInsight 버전 3.1(기본값)</th><th>HDInsight 버전 3.0</th><th>HDIinsight 버전 2.1</th><th>HDInsight 버전 1.6</th></tr>
<tr><td>HDP(Hortonworks Data Platform)</td><td>2.1.7</td><td>2.0</td><td>1.3</td><td>1.1</td></tr>
<tr><td>Apache Hadoop & YARN</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td><td>1.0.3</td></tr>
<tr><td>Tez</td><td>0.4.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.3</td></tr>
<tr><td>Apache Hive & HCatalog</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.0</td></tr>
<tr><td>분석 </td><td>0.98.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>Hive와 병합된 버전</td><td>Hive와 병합된 버전</td><td>Hive와 병합된 버전</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>Hive와 병합된 버전</td><td>Hive와 병합된 버전</td><td>Hive와 병합된 버전</td><td>0.1.4</td></tr>
<tr><td>Ambari</td><td>>=1.5.1</td><td>1.4.1</td><td>API v1.0</td><td></td></tr>
<tr><td>Zookeeper</td><td>3.4.5</td><td>3.4.5</td><td></td><td></td></tr>
<tr><td>Storm</td><td>0.9.1</td><td></td><td></td><td></td></tr>
<tr><td>Mahout</td><td>0.9.0</td><td></td><td></td><td></td></tr>
<tr><td>Phoenix</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td><td></td></tr>
</table>


**현재 구성 요소 버전 정보 가져오기**

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 HDInsight에 대한 향후 업데이트에서 변경될 수 있습니다. 사용 가능한 구성 요소를 알아보고 사용 중인 버전을 확인하는 방법은 클러스터에서 Ambari REST API를 사용하는 것입니다. 또한 **GetComponentInformation** 명령을 사용하여 서비스 구성 요소에 대한 정보를 검색할 수도 있습니다. 자세한 내용은 [Ambari 설명서][ambari-docs](영문)를 참조하세요. 이 정보를 가져오는 다른 방법은 원격 데스크톱을 사용하여 클러스터에 로그인하고 "C:\apps\dist\" 디렉터리의 콘텐츠를 직접 검토하는 것입니다.


**릴리스 정보**	

최신 HDInsight 버전과 관련된 추가 릴리스 정보에 대해서는 [HDInsight 릴리스 정보](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-release-notes/) 를 참조하세요.

### HDInsight 클러스터를 프로비전할 경우 버전 선택

HDInsight PowerShell Cmdlet 또는 HDInsight .NET SDK를 통해 클러스터를 만들 경우 "Version" 매개 변수를 사용하여 HDInsight Hadoop 클러스터의 버전을 선택할 수 있습니다.

**빠른 생성** 옵션을 사용하는 경우 기본적으로 Hadoop 클러스터를 만드는 HDInsight 버전 3.1이 제공됩니다. Azure 포털에서 **사용자 지정 만들기** 옵션을 사용하는 경우 **클러스터 세부 정보** 페이지의 **HDInsight 버전** 드롭다운에서 배포할 클러스터의 버전을 선택할 수 있습니다. 

##주요 기능
현재 Azure HDInsight는 기본적으로 HDInsight 클러스터 버전 3.1(Hortonworks Data Platform 2.1.7 사용)의 Hadoop 2.4를 지원합니다. 이 제품은 이 플랫폼을 최대한 활용하여 고객에게 매우 다양한 이점을 제공합니다. 이러한 혜택에는 특히 다음이 포함됩니다.

- **스크립트 동작(미리 보기)**: 이 클러스터 사용자 지정 기능의 미리 보기를 통해 사용자 지정 스크립트를 사용하여 임의 방식으로 Hadoop 클러스터를 수정할 수 있습니다. 이 새로운 기능을 통해 사용자는 Apache Hadoop 에코시스템에서 사용할 수 있는 프로젝트를 실험하고 Azure HDInsight 클러스터에 배포할 수 있습니다. 이 사용자 지정 기능은 Hadoop, HBase, Storm을 비롯한 모든 HDInsight 유형에서 사용할 수 있습니다.

- **HBase**: HBase는 빅 데이터를 온라인으로 트랜잭션 처리할 수 있게 하는 짧은 대기 시간의 NoSQL 데이터베이스입니다. HBase는 Azure 환경에 통합된 관리 클러스터로 제공합니다. 이 클러스터는 Azure Blob 저장소에 직접 데이터를 저장하도록 구성되며, 그러면 대기 시간이 짧고 성능/비용 선택 시 탄력성이 높습니다. 따라서 고객은 대규모 데이터 집합으로 작업하는 대화형 웹 사이트를 구축하고, 수백만 개의 끝점에서 발생하는 센서 및 원격 분석 데이터를 저장하는 서비스를 구축하고, Hadoop 작업을 사용해 이 데이터를 분석할 수 있습니다.

- **Apache Phoenix**: Apache Phoenix는 HBase에 기반을 둔 SQL 쿼리 계층입니다. 보조 인덱스 지원을 포함하여 SQL 쿼리 언어 사양의 제한된 하위 집합을 지원합니다. HBase 데이터에 대한 낮은 지연 쿼리를 목표로 하는 클라이언트에 포함된 JDBC 드라이버로 제공됩니다. Apache Phoenix는 SQL 쿼리를 수신하고, 일련의 HBase 스캔 및 보조 프로세서 호출로 컴파일하고, 일반 JDBC 결과 집합을 생성합니다. Apache Phoenix는 HBase에 기반을 둔 관계형 데이터베이스 계층입니다. HBase 데이터에 대한 낮은 지연 쿼리를 목표로 하는 클라이언트에 포함된 JDBC 드라이버로 제공됩니다. Apache Phoenix는 SQL 쿼리를 수신하고, 일련의 HBase 스캔으로 컴파일하고, 해당 스캔 실행을 오케스트레이션하여 일반 JDBC 결과 집합을 생성합니다.

- **클러스터 대시보드**: HDInsight 클러스터에 배포되는 새로운 웹 응용 프로그램으로,  Hive 쿼리를 실행하고 작업 로그를 확인하고 Azure Blob 저장소를 찾는 데 사용됩니다. 웹 응용 프로그램에 액세스하는 데 사용되는 URL은 <*ClusterName*>.azurehdinsight.net입니다.

- **Microsoft Avro 라이브러리**: 이 라이브러리는 Microsoft.NET 환경을 위한 Apache Avro 데이터 직렬화 시스템을 구현합니다. Apache Avro는 직렬화를 위한 압축 이진 데이터 교환 형식을 제공합니다. 또한 JSON을 사용하여 언어 상호 운용성을 따르는 언어 중립적 스키마를 정의합니다. 한 언어로 직렬화된 데이터는 다른 언어로 읽을 수 있습니다. 현재 C, C++, C#, Java, PHP, Python 및 Ruby가 지원됩니다. Apache Avro 직렬화 형식은 Hadoop MapReduce 작업 내의 복잡한 데이터 구조를 나타내기 위해 Azure HDInsight에서 폭넓게 사용됩니다.

- **YARN**: 기존 Apache Hadoop MapReduce 프레임워크를 대체하여 Hadoop 클러스터에서 데이터를 처리하는 새로운 범용 분산 응용 프로그램 관리 프레임워크. Hadoop 운영 체제의 역할을 효과적으로 하며, 일괄 처리를 위한 일회용 데이터 플랫폼에서 일괄, 대화형, 온라인 및 스트림 처리를 가능하게 하는 다중 사용 플랫폼으로 Hadoop을 전환시켜 줍니다. 이 새로운 관리 프레임워크는 용량 보증, 공정성 및 서비스 수준 계약과 같은 기준에 따라 확장성 및 클러스터 사용률을 개선합니다.

- **Tez(HDInsight 3.1만 해당)**: Hadoop의 소규모 및 대규모 작업에서 간소화된 데이터 처리 작업을 만드는 일반 용도의 사용자 지정 가능 프레임워크입니다. Tez는 단일 작업에 대해 복잡한 작업 DAG(Directed Acyclic Graph)을 실행할 수 있도록 하여 Apache Hive 및 Apache Pig와 같은 Apache Hadoop 에코시스템의 프로젝트에서 사용자가 조작 가능한 응답 시간 및 페타바이트 크기의 까다로운 처리량 요구를 충족하도록 합니다. Hive 0.13에서는 Hive 쿼리가 MapReduce가 아닌 Tez에서 실행될 수 있습니다.

- **고가용성**: HDInsight에서 배포한 Hadoop 클러스터에는 서비스 가용성을 높이기 위해 두 번째 헤드 노드가 추가되었습니다. 일반적으로 표준 방식으로 구현된 Hadoop 클러스터에는 헤드 노드가 1개뿐입니다. HDInsight는 보조 헤드 노드를 추가하여 이러한 단일 오류 지점을 없앱니다. 새로운 HA 클러스터 구성으로 전환해도 고객이 기본 크기의 큰 노드 대신 훨씬 더 큰 헤드 노드로 클러스터를 프로비전하지 않는다면 클러스터 가격은 달라지지 않습니다.

- **Hive 성능**: ORC(**Optimized Row Columnar**) 형식을 사용하여 Hive 쿼리 응답 시간(최대 40배) 및 데이터 압축(최대 80%)의 대폭적인 개선

- **Pig, Sqoop, Oozie, Ambari**: HDInsight 클러스터 버전 2.1(HDP 1.3/Hadoop 1.2))에 패리티를 제공하는 HDInsight 클러스터 버전 3.0(HDP 2.0/Hadoop 2.2)의 구성 요소 버전 업그레이드. 사양에 대해서는 아래의 버전 표를 참조하세요.

- **Mahout**: 이러한 확장 가능한 기계 학습 알고리즘의 라이브러리는 HDInsight 3.1 Hadoop 클러스터에 미리 설치됩니다. 따라서 추가 클러스터 구성 없이도 Mahout 작업을 실행할 수 있습니다.

- **가상 네트워크 지원**: HDInsight를 Azure 가상 네트워크와 함께 사용하여 데이터 센터의 리소스와 클라우드 리소스를 연결하는 하이브리드 시나리오 또는 클라우드 리소스 격리를 지원할 수 있습니다.


## 지원되는 버전
다음 표는 현재 사용 가능한 HDInsight 버전, 버전에서 사용하는 해당 HDP(Hortonworks Data Platform) 버전 및 릴리스 날짜를 나열합니다. 알려진 경우 지원 만료 날짜와 사용이 중단된 날짜도 나와 있습니다. 다음 사항에 유의하세요.

* 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 2.1, 3.0 및 3.1 클러스터에 대해 배포됩니다. HDInsight 1.6 클러스터에서는 사용할 수 없습니다.
* 특정 버전에 대한 지원이 만료되면 Azure 관리 포털을 통해 사용할 수 없게 됩니다. 다음 표에는 Azure 관리 포털에서 사용할 수 있는 버전이 나와 있습니다. 클러스터 버전은 사용이 중단되는 날짜까지 PowerShell [New-AzureHDInsightCluster](http://msdn.microsoft.com/ko-kr/library/dn593744.aspx) 명령 및 .NET SDK에서 `Version` 매개 변수를 통해 계속 사용할 수 있습니다. 

<table border="1">
<tr><th>HDInsight 버전</th><th>HDP 버전</a><th>고가용성</th></th><th>릴리스 날짜</th><th>Azure 관리 포털에서 사용 가능 여부</th><th>지원 만료 날짜</th><th>사용 중단 날짜</th></tr>
<tr><td>HDI 3.1</td><td>HDP 2.1</td><td>예</td><td></td><td>예</td><td></td><td></td></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>예</td><td>2014/02/11</td><td>예</td><td>2014/09/17</td><td>2015/06/30</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>예</td><td>2013/10/28</td><td>아니요</td><td>2014/05/12</td><td>2015/05/31</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>아니요</td><td>2013/10/28</td><td>아니요</td><td>2014/04/26</td><td>2015/05/31</td></tr>
</table><br>

**기본이 아닌 클러스터의 배포**	

Azure 포털, HDinsight SDK 및 Azure PowerShell에서는 Hadoop 2.4에서 HDInsight 3.1 클러스터 만들기를 지원합니다. HDInsight 3.1 클러스터는 기본적으로 Hadoop 2.4에서 만들어지므로 추가 클러스터를 만들어야 하는 경우 포털의 **사용자 지정 만들기** 옵션을 사용하여 다른 HDInsight 클러스터 버전을 지정해야 합니다.

### HDInsight 클러스터 버전의 SLA(서비스 수준 계약)

SLA는 "지원 기간" 면에서 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 클러스터 버전을 지원하는 기간을 나타냅니다.  HDInsight 클러스터는 해당 버전의 **지원 만료 날짜**가 현재 날짜를 지난 경우 지원 기간을 넘긴 것입니다.  지원되는 HDInsight 클러스터 버전 목록은 위의 표에서 찾아볼 수 있습니다.  새로운 X+1 버전이 사용 가능해질 경우 지정된 HDInsight 버전의 지원 만료 날짜는 다음 수식 중 후자로 계산됩니다.  

- 수식 1:  HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
- 수식 2: HDInsight 클러스터 버전 X+1(X 이후의 후속 버전)을 Azure 관리 포털에서 사용할 수 있는 날짜에 90일을 추가합니다.

**사용 중단 날짜**는 HDInsight에서 해당 클러스터 버전을 만들 수 없게 되는 날짜입니다.

> [WACOM.NOTE] HDInsight 2.1 및 3.0 클러스터는 모두 64비트 버전의 Windows Server 2012 R2를 사용하고 .NET Framework 4.0, 4.5 및 4.5.1을 지원하는 Azure Guest OS [Family 4](http://msdn.microsoft.com/ko-kr/library/azure/ee924680.aspx#explanation) (영문)에서 실행됩니다. 

## HDInsight 버전과 관련된 Hortonworks 릴리스 정보##

* HDInsight 클러스터 버전 3.1에서는 [Hortonworks Data Platform 2.1.7][hdp-2-1-7]을 기반으로 하는 Hadoop 배포를 사용합니다. 이는 2014년 11월 7일 이후 Azure HDInsight 포털을 사용할 때 만든 기본 Hadoop 클러스터입니다. 2014년 11월 7일 전에 만들어진 HDInsight 3.1 클러스터는 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]에 기반을 둡니다. 

* HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8](영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0](영문)을 기반으로 하는 Hadoop 배포를 사용합니다. 

* HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0](영문)을 기반으로 하는 Hadoop 배포를 사용합니다. 


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/ko-kr/support/forums/

[connect-excel-with-hive-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/ 

<!--HONumber=35.1-->

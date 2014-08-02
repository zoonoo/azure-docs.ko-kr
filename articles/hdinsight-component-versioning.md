<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

HDInsight에서 제공하는 클러스터 버전의 새로운 기능
==================================================

Azure HDInsight에서는 이제 HDinsight 클러스터 버전 3.0과 함께 Hadoop 2.2를 지원하며, 이 플랫폼을 완벽히 활용하여 고객에게 다양한 중요 혜택을 제공합니다. 이러한 혜택에는 특히 다음이 포함됩니다.

-   Hive: ORC(Optimized Row Columnar) 형식을 사용하여 Hive 쿼리 응답 시간(최대 40배) 및 데이터 압축(최대 80%)의 대폭적인 개선

-   YARN: 기존 Apache Hadoop MapReduce 프레임워크를 대체하여 Hadoop 클러스터에서 데이터를 처리하는 새로운 범용 분산 응용 프로그램 관리 프레임워크. Hadoop 운영 체제의 역할을 효과적으로 하며, 일괄 처리를 위한 일회용 데이터 플랫폼에서 일괄, 대화형, 온라인 및 스트림 처리를 가능하게 하는 다중 사용 플랫폼으로 Hadoop을 전환시켜 줍니다. 이 새로운 관리 프레임워크는 용량 보증, 공정성 및 서비스 수준 계약과 같은 기준에 따라 확장성 및 클러스터 사용률을 개선합니다.

-   Pig, Sqoop, Qozie, Ambari: HDinsight 클러스터 버전 2.1(HDP 1.3)에 패리티를 제공하는 HDinsight 클러스터 버전 3.0(HDP 2.0)의 구성 요소 버전 업그레이드. 사양에 대해서는 아래의 버전 표를 참조하십시오. Hbase, Mahout 및 Flume은 포함되지 않습니다.

**배포** Azure 포털, HDinsight SDK 및 Azure PowerShell에서는 Hadoop 2.2에서 HDInsight 3.0 클러스터 만들기를 지원합니다.

**글로벌 가용성** Microsoft는 Hadoop 2.2의 Azure HDInsight 릴리스를 통해 중화권을 제외한 모든 주요 Azure 지역에서 HDInsight를 사용할 수 있도록 설정했습니다. 특별히 서유럽과 동남아시아 데이터 센터를 온라인으로 전환했습니다. 따라서 고객은 준수 요구 사항이 비슷한 영역에 있는, 가까운 데이터 센터에서 클러스터를 찾을 수 있습니다.

**주요 변경 내용** HDInsight 3.0 클러스터에서는 "wasb://" 구문만 지원됩니다. 이전 "asv://" 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않습니다. 즉, 정확히 "asv://" 구문을 명시적으로 사용하며 HDInsight 3.0 클러스터에 제출하는 작업은 실패하게 됩니다. 대신 wasb:// 구문을 사용해야 합니다. 또한 asv:// 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 메타스토어로 만들어 HDInsight 3.0 클러스터에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 wasb://를 사용하여 이러한 메타스토어를 다시 만들어야 합니다.

HDInsight 버전
--------------

HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(HortonWorks Data Platform) 배포 및 해당 배포 내에 포함된 구성 요소 집합이 프로비전됩니다.

### 클러스터 버전 3.0

Azure HDInsight는 현재 Hadoop 2.2를 지원합니다. Hortonworks Data Platform 버전 2.0을 기반으로 하며, 다음 표의 항목별 구성 요소 버전을 Hadoop 서비스에 제공합니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">구성 요소</th><th data-morhtml="true">버전</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">2.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.12.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.12</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">4.0.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Hive와 병합된 버전</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Hive와 병합된 버전</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### 클러스터 버전 2.1

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601)에서 사용하는 기본 클러스터 버전은 2.1입니다. Hortonworks Data Platform 버전 1.3.0을 기반으로 하며, 다음 표의 항목별 구성 요소 버전을 Hadoop 서비스에 제공합니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">구성 요소</th><th data-morhtml="true">버전</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.11.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.11</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Hive와 병합된 버전</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Hive와 병합된 버전</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### 클러스터 버전 1.6

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) 클러스터 버전 1.6도 사용할 수 있습니다. Hortonworks Data Platform 버전 1.1.0을 기반으로 하며, 다음 표의 항목별 구성 요소 버전을 Hadoop 서비스에 제공합니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">구성 요소</th><th data-morhtml="true">버전</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.0.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.9.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.9.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">0.4.1</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">0.1.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">SQL Server JDBC 드라이버</td><td data-morhtml="true">3.0</td></tr>
</table>

### HDInsight 클러스터를 프로비전할 경우 버전 선택

HDInsight PowerShell Cmdlet 또는 HDInsight .NET SDK를 통해 클러스터를 만들 경우 "Version" 매개 변수를 사용하여 HDInsight Hadoop 클러스터의 버전을 선택할 수 있습니다.

**빠른 생성** 옵션을 사용하는 경우 기본적으로 버전 2.1의 HDInsight Hadoop 클러스터를 가져옵니다. Azure 포털에서 **사용자 지정 만들기** 옵션을 사용하는 경우 **클러스터 세부 정보** 페이지의 **HDInsight 버전** 드롭다운에서 배포할 클러스터의 버전을 선택할 수 있습니다. 버전 3.0의 HDInsight Hadoop 클러스터는 **사용자 지정 만들기** 마법사의 옵션으로만 사용할 수 있습니다.

![HDI.Versioning.VersionScreen](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

지원되는 버전
-------------

다음 표는 현재 사용 가능한 HDInsight 버전, 버전에서 사용하는 해당 HDP(Hortonworks Data Platform) 버전 및 릴리스 날짜를 나열합니다. 알려진 경우 사용이 중단된 날짜도 나와 있습니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">HDInsight 버전</th><th data-morhtml="true">HDP 버전</th><th data-morhtml="true">릴리스 날짜</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 3.0</td><td data-morhtml="true">HDP 2.0</td><td data-morhtml="true">02/11/2014</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 2.1</td><td data-morhtml="true">HDP 1.3</td><td data-morhtml="true">10/28/2013</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 1.6</td><td data-morhtml="true">HDP 1.1</td><td data-morhtml="true">10/28/2013</td></tr>
</table>

### HDInsight 클러스터 버전의 SLA(서비스 수준 계약)

SLA는 "지원 기간" 면에서 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 클러스터 버전을 지원하는 기간을 나타냅니다. HDInsight 클러스터는 해당 버전의 지원 만료 날짜가 현재 날짜를 지난 경우 지원 기간을 넘긴 것입니다. 지원되는 HDInsight 클러스터 버전 목록은 위의 표에서 찾아볼 수 있습니다. 지정된 HDInsight 버전(버전 X로 표시)의 지원 만료 날짜는 다음 수식 중 후자로 계산됩니다.

-   수식 1: HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
-   수식 2: HDInsight 클러스터 버전 X+1(X 이후의 후속 버전)을 Azure 관리 포털에서 사용할 수 있는 날짜에 90일을 추가합니다.

> [WACOM.NOTE] HDInsight 2.1 및 3.0은 모두 64비트 버전의 Windows 2008 R2 SP1과 .NET Framework 4.0을 사용합니다.

**버전 관리에 대한 추가 사항 및 정보**

-   SQL Server JDBC 드라이버는 HDInsight에서 내부적으로 사용되며 외부 작업에는 사용되지 않습니다. ODBC를 사용하여 HDInsight에 연결하려는 경우 Microsoft Hive ODBC 드라이버를 사용하십시오. Hive ODBC 사용에 대한 자세한 내용은 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결](/en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver)을 참조하십시오.

-   HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html)(영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

-   HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html)(영문)을 기반으로 하는 Hadoop 배포를 사용합니다. 이는 Azure HDInsight 포털을 사용할 때 만든 기본 Hadoop 클러스터입니다.

-   HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html)(영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

-   HDInsight 클러스터 버전과 연결된 구성 요소 버전은 HDInsight에 대한 향후 업데이트에서 변경될 수 있습니다. 사용 가능한 구성 요소를 알아보고 사용 중인 버전을 확인하는 방법은 클러스터에서 Ambari REST API를 사용하는 것입니다. 또한 GetComponentInformation 명령을 사용하여 서비스 구성 요소에 대한 정보를 검색할 수도 있습니다. 자세한 내용은 [Ambari 설명서](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)(영문)를 참조하십시오. 이 정보를 가져오는 다른 방법은 원격 데스크톱을 사용하여 클러스터에 로그인하고 "C:\\apps\\dist" 디렉터리의 콘텐츠를 직접 검토하는 것입니다.



<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Microsoft HDInsight 릴리스 정보

## 2014/8/21 릴리스 정보

-   Templeton 컨트롤러 작업에 대한 기본 메모리 제한을 1GB(이전 기본값은 512MB)로 설정하는 다음과 같은 새로운 WebHCat 구성(HIVE-7155)이 추가됩니다.

    -   templeton.mapper.memory.mb (=1024)
    -   이 변경은 낮은 메모리 제한으로 인해 특정 Hive 쿼리가 발생하는 다음 오류를 해결합니다. “컨테이너가 실제 메모리 제한 이상으로 실행합니다.”
    -   이전 기본값으로 다시 돌리려면 클러스터 생성 시 다음 명령을 사용하여 PowerShell SDK를 통해 이 구성 값을 512로 설정하면 됩니다.

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   zookeeper 역할의 호스트 이름이 zookeeper로 변경되었습니다. 이 변경은 클러스터 내의 이름 확인에 영향을 주지만 외부 REST API에는 영향을 주지 않습니다. zookeepernode 호스트 이름을 사용하는 구성 요소가 있는 경우 새로운 이름을 사용하도록 해당 구성 요소를 업데이트해야 합니다. 3개 zookeeper 노드의 새로운 이름은 다음과 같습니다.

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   HBase 버전 지원 매트릭스가 업데이트됩니다. 프로덕션 HBase 작업에는 버전 HDInsight 3.1(HBase 버전 0.98)만 지원됩니다. 미리 보기에 사용 가능한 버전 3.0은 앞으로 지원되지 않습니다. 전환 기간 중 고객은 계속 3.0 버전의 클러스터를 만들 수 있습니다.

## 2014/7/28 릴리스 정보

-   **새로운 지역에서 사용 가능한 HDInsight**: 이 릴리스에서는 HDInsight 제공 지역을 새로운 3개 지역으로 확장했습니다. 이제 HDInsight 고객은 다음 지역에 클러스터를 만들 수 있습니다.

    -   동아시아
    -   미국 중북부
    -   미국 중남부
-   이 릴리스에서 HDInsight v1.6(HDP1.1, Hadoop 1.0.3) 및 HDInsight v2.1(HDP1.3, Hadoop 1.2)이 Azure 관리 포털에서 제거됩니다. HDInsight PowerShell cmdlet([New-AzureHDInsightCluster][New-AzureHDInsightCluster])을 사용하거나 [HDInsight SDK][HDInsight SDK]를 사용하여 계속 Hadoop 클러스터를 만들 수 있습니다. 자세한 내용은 [HDInsight 구성 요소 버전][HDInsight 구성 요소 버전] 페이지를 참조하세요.
-   이 릴리스의 HDP(Hortonworks Data Platform) 변경 내용:

| HDP               | 변경 내용                                                    |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | 변경 내용 없음                                               |
| HDP 2.0 / HDI 3.0 | 변경 내용 없음                                               |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## 2014/6/24 릴리스 정보

이 릴리스에는 HDInsight 서비스의 새로운 개선 기능이 포함되어 있습니다.

-   **HDP 2.1 사용 가능**: 이제 HDP 2.1을 포함하는 HDInsight 3.1이 일반 공급되며 새로운 클러스터의 기본 버전입니다.
-   **HBase – Azure 관리 포털 개선 사항**: 미리 보기에서 HBase 클러스터를 사용할 수 있습니다. 이제 3번의 클릭으로 포털에서 HBase 클러스터를 만들 수 있습니다.

![][0]

HBase에서는, 수백만 개의 끝점에서 들어오는 센서 및 원격 분석 데이터를 저장하는 서비스의 대형 데이터 집합을 사용하는 대화형 웹 사이트에서 HDInsight에 대한 여러 실시간 작업을 구성할 수 있습니다. 다음 단계는 Hadoop 작업으로 이러한 작업의 데이터를 분석하는 것이며, PowerShell 및 Hive 클러스터 대시보드와 같이 제공된 환경을 통해 HDInsight에서 즉시 수행할 수 있습니다.

### 이제 HDInsight 3.1에 사전 설치되는 Apache Mahout

[Mahout][Mahout]은 HDInsight 3.1 Hadoop 클러스터에 사전 설치됩니다. 따라서 추가 클러스터 구성 없이도 Mahout 작업을 실행할 수 있습니다. 예를 들어 RDP(원격 데스크톱 프로토콜)를 사용하면 Hello world Mahout 명령을 실행하는 추가 단계 없이도 Hadoop 클러스터에 원격으로 연결할 수 있습니다.

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

이 절차에 대한 자세한 설명은 Apache Mahout 웹 사이트의 [Breiman 예제][Breiman 예제](영문) 설명서를 참조하세요.

### Hive 쿼리는 HDinsight 3.1에서 Tez를 사용할 수 있음

이제 Hive 0.13은 HDInsight 3.1에서 사용 가능하며 Tez를 사용하여 쿼리를 실행할 수 있으므로, 실질적인 성능 개선을 이룰 수 있습니다.
Tez는 기본적으로 Hive 쿼리에 사용할 수 없습니다. Tez를 사용하려면 옵트인해야 합니다. 다음 코드 조각을 실행하여 Tez를 사용하도록 설정할 수 있습니다.

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks에서 표준 벤치마크로 전달되는 Tez와 함께 사용하는 Hive 쿼리 성능 개선 사항의 자세한 분석을 게시했습니다. 자세한 내용은 [Enterprise Hadoop용 Apache Hive 13 벤치마킹][Enterprise Hadoop용 Apache Hive 13 벤치마킹]을 참조하세요.

Tez와 함께 Hive 사용에 대한 자세한 내용은 [Tez의 Hive 위키 페이지][Tez의 Hive 위키 페이지](영문)를 참조하세요.

### 전 세계 이용 가능 여부

Hadoop 2.2의 Azure HDInsight 릴리스부터 모든 주요 Azure 지역에서 HDInsight를 사용할 수 있게 되었습니다. 특별히 서유럽과 동남아시아 데이터 센터를 온라인으로 전환했습니다. 따라서 고객은 준수 요구 사항이 비슷한 영역에 있는, 가까운 데이터 센터에서 클러스터를 찾을 수 있습니다.

### 주요 변경 내용

**접두사 구문**:
 HDInsight 3.0 및 3.1 클러스터에서는 "wasb://" 구문만 지원됩니다. 이전 "asv://" 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터 이상 버전에서는 지원되지 않습니다. 즉, 정확히 "asv://" 구문을 명시적으로 사용하며 HDInsight 3.0 또는 3.1 클러스터에 제출하는 작업은 실패하게 됩니다. 대신 wasb:// 구문을 사용해야 합니다. 또한 asv:// 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 메타스토어로 만들어 HDInsight 3.0 또는 3.1 클러스터에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 wasb://를 사용하여 이러한 메타스토어를 다시 만들어야 합니다.

**포트**: HDInsight 서비스에 사용되는 포트가 변경되었습니다. 기존에 사용하는 포트 번호는 Windows OS 사용 후 삭제되는 포트 범위 내에 있었습니다. 포트는 수명이 짧은 인터넷 프로토콜 기반 통신용으로 미리 정의된 사용 후 삭제되는 범위에서 자동으로 할당됩니다. 허용되는 새로운 HDP(Hortonworks Data Platform) 서비스 포트 집합은 이제 이 범위를 벗어나므로 헤드 노드에서 실행 중인 서비스에 사용되는 포트로 인해 발생할 수 있는 충돌을 방지합니다. 새로운 포트 번호로 인한 특별한 변경은 없습니다. 이제 사용되는 번호는 다음과 같습니다.

**HDInsight 1.6(HDP 1.1)**

<table border="1">

<tr>
<th>
이름

</th>
<th>
값

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 and 3.1(HDP 2.0 and 2.1)**

<table border="1">

<tr>
<th>
이름

</th>
<th>
값

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### 종속성

다음 종속성이 HDInsight 3.x (HDP2.x)에 추가되었습니다.

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

다음 종속성은 더 이상 HDInsight 3.x (HDP2.x)에 존재하지 않습니다.

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json :
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   HBase:
-   snappy

### 버전 변경 내용

다음 버전 변경 내용이 HDInsight 2.x(HDP1.x)와 HDInsight 3.x(HDP2.x) 간에 수행되었습니다.

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### 드라이버

SQL Server JDBC 드라이버는 HDInsight에서 내부적으로 사용되며 외부 작업에는 사용되지 않습니다. ODBC를 사용하여 HDInsight에 연결하려는 경우 Microsoft Hive ODBC 드라이버를 사용하세요. Hive ODBC 사용에 대한 자세한 내용은 [Microsoft Hive ODBC 드라이버를 사용하여 HDInsight에 Excel 연결][connect-excel-with-hive-ODBC]를 참조하세요.

### 버그 수정

이 릴리스에서 몇 가지 버그 수정을 통해 다음 HDInsight(Hortonworks Data Platform - HDP) 버전을 새로 고쳤습니다.

-   HDInsight 2.1(HDP 1.3)
-   HDInsight 3.0(HDP 2.0)
-   HDInsight 3.1(HDP 2.1)

## Hortonworks 릴리스 정보

HDInsight 클러스터의 버전에 사용되는 HDP에 대한 릴리스 정보는 다음 위치에서 사용할 수 있습니다.

-   HDInsight 클러스터 버전 3.1에서는 [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1]을 기반으로 하는 Hadoop 배포를 사용합니다. 이는 Azure HDInsight 포털을 사용할 때 만든 기본 Hadoop 클러스터입니다.

-   HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0](영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

-   HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3](영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

-   HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1](영문)을 기반으로 하는 Hadoop 배포를 사용합니다.

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/ko-kr/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/ko-kr/library/azure/dn469975.aspx
  [HDInsight 구성 요소 버전]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-component-versioning/
  [0]: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Breiman 예제]: https://mahout.apache.org/users/classification/breiman-example.html
  [Enterprise Hadoop용 Apache Hive 13 벤치마킹]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [Tez의 Hive 위키 페이지]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

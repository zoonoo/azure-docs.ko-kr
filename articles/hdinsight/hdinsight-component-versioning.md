---
title: "Hadoop 구성 요소 및 버전 - Azure HDInsight | Microsoft Docs"
description: "HDInsight의 Hadoop 구성 요소 및 버전과 HortonWorks Data Platform의 이 클라우드 배포판에서 사용할 수 있는 서비스 수준에 대해 알아봅니다."
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: bprakash
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: c753eac8caed139250a0db85b639fca57c1e9ea6
ms.lasthandoff: 03/28/2017


---
# <a name="what-are-the-different-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight에서 사용할 수 있는 다양한 Hadoop 구성 요소 및 버전은 무엇인가요?

Azure HDInsight에 대해 제공된 서비스 수준과 포함된 Hadoop 에코 시스템 구성 요소 및 버전에 대해 알아봅니다. 각 HDInsight 버전은 클라우드 배포판의 HDP(Hortonworks Data Platform) 버전입니다.

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard 및 HDInsight Premium

Azure HDInsight는 빅 데이터 클라우드 제품을 **Standard** 및 **Premium**의 두 범주로 제공합니다. 아래 섹션의 표에는 **Premium에서만** 사용 가능한 기능이 나와 있습니다. 표에 명시적으로 나와 있지 않은 기능은 Standard에서 제공됩니다.

> [!NOTE]
> HDInsight Premium 제품은 현재 Preview 상태이며 Linux 클러스터에서만 사용할 수 있습니다.
>
>

| HDInsight Premium 기능 | 설명 |
| --- | --- |
| 도메인에 가입된 HDInsight 클러스터 |엔터프라이즈 수준의 보안을 위해 Azure Active Directory(AAD) 도메인에 HDInsight 클러스터를 연결합니다. 이제 HDInsight 클러스터에 로그온하기 위해 Azure Active Directory를 통해 인증할 수 있는 기업의 직원 목록을 구성할 수 있습니다. 기업 관리자는 또한 [Apache Ranger](http://hortonworks.com/apache/ranger/)를 사용하여 Hive 보안용 역할 기반 액세스 제어를 구성할 수 있게 되어 필요한 만큼 데이터에 대한 액세스를 제한할 수 있습니다. 마지막으로 관리자는 직원의 데이터 액세스 및 액세스 제어 정책에 대한 변경 내용을 감사할 수 있게 되어 회사 리소스를 고도로 관리할 수 있습니다. 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요. |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>HDInsight Premium용으로 지원되는 클러스터 유형
다음 표에는 HDInsight 클러스터 유형 및 Premium 지원 행렬이 나와 있습니다.

| 클러스터 유형 | Standard | Premium |
| --- | --- | --- |
| Hadoop은 |예 |예(HDInsight 3.5에만 해당) |
| Spark |예 |아니요 |
| HBase |예 |아니요 |
| Storm |예 |아니요 |
| R 서버  |예 |아니요 |
| 대화형 Hive(미리 보기) |예 |아니요 |
| Kafka(미리 보기)|예|아니요| 

이 표는 HDInsight Premium에 추가 클러스터 유형이 포함되면 업데이트될 예정입니다.

### <a name="features-not-supported-for-hdinsight-premium"></a>HDInsight Premium용으로 지원되지 않는 기능

다음 기능은 현재 HDInsight Premium 클러스터에 대해 지원되지 않습니다.

* **Azure Data Lake Store가 기본 저장소로 지원되지 않습니다**. HDInsight Premium 클러스터와 함께 Azure Data Lake Store를 추가 기능 저장소로 계속 사용할 수 있습니다.

### <a name="pricing-and-sla"></a>가격 및 SLA
HDInsight Premium의 가격 및 SLA에 대한 자세한 내용은 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>각 HDInsight 버전에서 제공되는 Hadoop 구성 요소
Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포에 포함된 구성 요소 집합이 만들어집니다. HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 항목별로 구분되어 있습니다. Azure HDInsight에서 사용하는 기본 클러스터 버전은 현재 3.5이지만 2017/02/17을 기준으로 HDP 2.5에 기반하게 됩니다.

> [!NOTE]
> 서비스의 기본 버전은 예고 없이 변경 될 수 있습니다. 버전 종속성이 있는 경우 .NET SDK/Azure PowerShell 및 Azure CLI를 사용하여 클러스터를 만들 때 버전을 지정하는 것이 좋습니다.
>
>


| 구성 요소 | HDInsight 버전 3.5(기본값) | HDInsight 버전 3.4 | HDInsight 버전 3.3 | HDInsight 버전 3.2 | HDInsight 버전 3.1 | HDInsight 버전 3.0 |
| --- | --- | --- | --- | --- | --- | --- |
| Hortonworks Data Platform |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop & YARN |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive & HCatalog |1.2.1.2.5 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache HBase |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |1.6.2 + 2.0(Linux만 해당) |1.6.0(Linux만 해당) |1.5.2(Linux만 해당/ 실험적 빌드) |1.3.1(Windows만 해당) |-|-|
| Apache Kafka | 0.10.0 | 0.9.0 |-|-|-|-|
| Mono |4.2.1 |3.2.8 |-|-|-|-|

**현재 구성 요소 버전 정보 가져오기**

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 HDInsight에 대한 향후 업데이트에서 변경될 수 있습니다. 사용 가능한 구성 요소를 알아보고 사용 중인 버전을 확인하는 방법은 클러스터에서 Ambari REST API를 사용하는 것입니다. 또한 **GetComponentInformation** 명령을 사용하여 서비스 구성 요소에 대한 정보를 검색할 수도 있습니다. 자세한 내용은 [Ambari 설명서][ambari-docs]를 참조하세요. 이 정보를 가져오는 다른 방법은 원격 데스크톱을 사용하여 클러스터에 로그인하고 "C:\apps\dist\" 디렉터리의 콘텐츠를 직접 검토하는 것입니다.

**릴리스 정보**

최신 HDInsight 버전과 관련된 추가 릴리스 정보에 대해서는 [HDInsight 릴리스 정보](hdinsight-release-notes.md) 를 참조하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전
다음 표는 현재 사용 가능한 HDInsight 버전, 버전에서 사용하는 해당 Hortonworks Data Platform 버전 및 릴리스 날짜를 나열합니다. 알려진 경우 지원 만료 날짜와 사용이 중단된 날짜도 나와 있습니다. 다음 사항에 유의하세요.

* 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 2.1 이상에 대해 배포됩니다. HDInsight 1.6 클러스터에서는 사용할 수 없습니다.
* 특정 버전에 대한 지원이 만료되면 Azure 포털을 통해 사용할 수 없게 됩니다. 다음 표에는 Azure 클래식 포털에서 사용할 수 있는 버전이 나와 있습니다. 클러스터 버전은 사용이 중단될 때까지 Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) 명령의 `Version`매개 변수 및 .NET SDK를 사용하여 계속 사용할 수 있습니다.

| HDInsight 버전 | HDP 버전 | VM OS | 고가용성 | 릴리스 날짜 | Azure 포털에서 사용 가능 여부 | 지원 만료 날짜 | 사용 중단 날짜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 미리 보기 |HDP 2.6 |Ubuntu 16 |예 |02/28/2017 |예 | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |예 |9/30/2016 |예 | | |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |예 |2016/03/29 |예 |12/29/2016 |1/9/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS 또는 Windows Server 2012R2 |예 |12/02/2015 |예 |06/27/2016 |07/31/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 또는 Windows Server 2012R2 |예 |2/18/2015 |예 |3/1/2016 |04/01/2017 |
| HDI 3.1 |HDP 2.1 |Windows Server 2012R2 |예 |6/24/2014 |아니요 |05/18/2015 |06/30/2016 |
| HDI 3.0 |HDP 2.0 |Windows Server 2012R2 |예 |2014/02/11 |아니요 |2014/09/17 |2015/06/30 |
| HDI 2.1 |HDP 1.3 |Windows Server 2012R2 |예 |2013/10/28 |아니요 |2014/05/12 |2015/05/31 |
| HDI 1.6 |HDP 1.1 | |아니요 |2013/10/28 |아니요 |2014/04/26 |2015/05/31 |

##<a name="hdi-36-preview-with-apache-spark-21"></a>Apache Spark 2.1을 사용한 HDI 3.6 미리 보기
미리 보기용 HDI 3.6은 2017년 2월 28일에 릴리스됩니다. Apache Spark 2.1용 미리 보기에서 제공됩니다. 자세한 내용은 [Apache Spark 2.1을 사용한 HDI 3.6 미리 보기](https://azure.microsoft.com/en-us/blog/announcing-preview-of-azure-hdinsight-3-6-with-apache-spark-2-1/)를 참조하세요.

##<a name="hdi-version-32-and-33-nearing-deprecation-date"></a>HDI 버전 3.2 및 3.3(곧 사용 중단됨)
HDI 3.2 클러스터에 대한 지원은 2016년 3월 1일 만료되었으며 2017년 4월 1일부터는 사용되지 않습니다. HDI 3.3 클러스터에 대한 지원은 2016년 6월 27일 만료되었으며 2017년 7월 31일부터는 사용되지 않습니다. HDI 3.2 또는 HDI 3.3 클러스터가 있는 경우에는 최신 버전인 HDI 3.5로 클러스터를 업그레이드하세요.

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 클러스터 버전의 서비스 수준 계약
SLA는 "지원 기간" 면에서 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 클러스터 버전을 지원하는 기간을 나타냅니다. HDInsight 클러스터는 해당 버전의 **지원 만료 날짜** 가 현재 날짜를 지난 경우 지원 기간을 넘긴 것입니다. 지원되는 HDInsight 클러스터 버전 목록은 위의 표에서 찾아볼 수 있습니다. 새로운 X+1 버전이 사용 가능해질 경우 지정된 HDInsight 버전의 지원 만료 날짜는 다음 수식 중 후자로 계산됩니다.  

* 수식 1: HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
* 수식 2: HDInsight 클러스터 버전 X+1(X 이후의 후속 버전)을 포털에서 사용할 수 있는 날짜에 90일을 추가합니다.

**사용 중단 날짜** 는 HDInsight에서 해당 클러스터 버전을 만들 수 없게 되는 날짜입니다. 2017년 7월 31일부터 사용 중단 날짜 이후에는 클러스터 크기를 조정할 수 없습니다.

> [!NOTE]
> Windows 기반 HDInsight 클러스터(버전 2.1, 3.0, 3.1, 3.2 및 3.3 포함)는 Azure Guest OS Family 4에서 실행되고 64 비트 버전의 Windows Server 2012 R2를 사용하고 .NET Framework 4.0, 4.5, 4.5.1 및 4.5.2를 지원합니다.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>HDInsight 버전과 관련된 Hortonworks 릴리스 정보
* HDInsight 클러스터 버전 3.4에서는 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)를 기반으로 하는 Hadoop 배포를 사용합니다. 이는 포털을 사용할 때 만든 **기본** Hadoop 클러스터입니다.
* HDInsight 클러스터 버전 3.3에서는 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)(영문)를 기반으로 하는 Hadoop 배포를 사용합니다.

  * Apache Storm 릴리스 정보는 [여기](https://storm.apache.org/2015/11/05/storm0100-released.html)에 사용할 수 있습니다.
  * Apache Hive 릴리스 정보는 [여기](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)에 사용할 수 있습니다.
* HDInsight 클러스터 버전 3.2에서는 [Hortonworks Data Platform 2.2][hdp-2-2]를 기반으로 하는 Hadoop 배포를 사용합니다.  

  * 특정 Apache 구성 요소에 대한 릴리스 정보 - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight 클러스터 버전 3.1은 [Hortonworks Data Platform 2.1.7][hdp-2-1-7]을 기반으로 하는 Hadoop 배포를 사용합니다. 2014년 11월 7일 이전에 만들어진 HDInsight 3.1 클러스터는 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]에 기반합니다.
* HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8]을 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0]을 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0]을 기반으로 하는 Hadoop 배포를 사용합니다.

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/


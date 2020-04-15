---
title: Hadoop의 고가용성 - Azure HDInsight
description: HDInsight 클러스터에서 추가 헤드 노드를 사용하여 안정성과 가용성을 높이는 방법을 알아봅니다. 이로 인해 Ambari 및 Hive와 같은 Hadoop 서비스에 미치는 영향과 SSH를 사용하여 각 헤드 노드에 개별적으로 연결하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop high availability
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381406"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성

HDInsight 클러스터는 두 개의 헤드 노드를 제공하여 실행 중인 Apache Hadoop 서비스와 작업의 안정성과 가용성을 높입니다.

Hadoop은 클러스터의 여러 노드에서 서비스와 데이터를 복사하여 고가용성과 안정성을 달성합니다. 그러나 Hadoop의 표준 배포에는 일반적으로 단일 헤드 노드만 있습니다. 단일 헤드 노드의 가동 중단으로 인해 클러스터의 작동이 중지될 수 있습니다. HDInsight는 Hadoop의 가용성 및 안정성을 향상시키기 위해 두 헤드 노드를 제공합니다.

## <a name="availability-and-reliability-of-nodes"></a>노드의 가용성 및 안정성

HDInsight 클러스터에 있는 노드는 Azure Virtual Machines를 사용하여 구현됩니다. 다음 섹션에서는 HDInsight와 함께 사용되는 개별 노드 유형을 설명합니다.

> [!NOTE]  
> 일부 노드 유형은 클러스터 유형에 사용되지 않습니다. 예를 들어 Hadoop 클러스터 유형에는 Nimbus 노드가 없습니다. HDInsight 클러스터 유형에서 사용하는 노드에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) 문서의 클러스터 유형 섹션을 참조하세요.

### <a name="head-nodes"></a>헤드 노드

Hadoop 서비스의 고가용성을 보장하기 위해 HDInsight는 두 개의 헤드 노드를 제공합니다. 두 헤드 노드가 모두 활성화되어 HDInsight 클러스터 내에서 동시에 실행됩니다. 일부 서비스(예: Apache HDFS 또는 Apache Hadoop YARN)는 항상 헤드 노드 하나에서만 '활성' 상태입니다. 다른 서비스(예: HiveServer2 또는 Hive 메타스토어)는 두 헤드 노드에서 동시에 활성화됩니다.

클러스터의 다른 노드 유형에 대한 호스트 이름을 얻으려면 [Ambari REST API를](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)사용하십시오.

> [!IMPORTANT]  
> 숫자 값을 노드가 기본 또는 보조 노드와 연결하지 마세요. 숫자 값은 각 노드에 대해 고유한 이름을 제공하기 위해 존재합니다.

### <a name="nimbus-nodes"></a>Nimbus 노드

Nimbus 노드는 Apache Storm 클러스터와 함께 사용할 수 있습니다. Nimbus 노드는 작업자 노드에 대한 프로세싱을 배포하고 모니터링하여 Hadoop JobTracker에 유사한 기능을 제공합니다. HDInsight는 Storm 클러스터에 두 개의 Nimbus 노드를 제공합니다.

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper 노드

[ZooKeeper](https://zookeeper.apache.org/) 노드는 헤드 노드에서 마스터 서비스의 리더 선택에 사용됩니다. 또한 서비스, 데이터(작업자) 노드 및 게이트웨이가 마스터 서비스가 활성 상태인 헤드 노드를 파악하도록 보장하는 데도 사용됩니다. 기본적으로 HDInsight는 3개의 ZooKeeper 노드를 제공합니다.

### <a name="worker-nodes"></a>작업자 노드

작업자 노드는 클러스터에 작업이 제출될 경우 실제 데이터 분석을 수행합니다. 작업자 노드가 실패하면 수행 중인 작업이 다른 작업자 노드에 제출됩니다. 기본적으로 HDInsight는 네 개의 작업자 노드를 만듭니다. 이 숫자는 클러스터를 만드는 중에 그리고 만든 후에 필요에 따라 변경할 수 있습니다.

### <a name="edge-node"></a>모서리 노드

에지 노드는 클러스터 내의 데이터 분석에 적극적으로 참여하지 않습니다. 그것은 Hadoop와 함께 작업 할 때 개발자 또는 데이터 과학자에 의해 사용됩니다. 에지 노드는 클러스터의 다른 노드와 동일한 Azure Virtual Network에 있으며 다른 모든 노드에 직접 액세스할 수 있습니다. 에지 노드는 중요한 Hadoop 서비스 또는 분석 작업에서 리소스를 가져오지 않으면서 사용할 수 있습니다.

현재 HDInsight의 ML 서비스는 기본적으로 에지 노드를 제공하는 유일한 클러스터 형식입니다. HDInsight의 ML 서비스의 경우 에지 노드는 분산된 처리를 위해 클러스터에 제출하기 전에 노드에서 로컬로 R 코드를 테스트하는 데 사용됩니다.

다른 클러스터 형식으로 에지 노드를 사용하는 방법에 대한 내용은 [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md) 문서를 참조하세요.

## <a name="accessing-the-nodes"></a>노드 액세스

인터넷을 통한 클러스터 액세스는 공용 게이트웨이를 통해 제공되며, 액세스는 헤드 노드에 연결하는 것으로 제한되며, 있는 경우 에지 노드가 있습니다. 헤드 노드에서 실행되는 서비스에 대한 액세스는 헤드 노드가 여러 개 있는 데 영향을 받지 않습니다. 공용 게이트웨이는 요청된 서비스를 호스팅하는 헤드 노드로 요청을 라우팅합니다. 예를 들어 현재 Apache Ambari가 보조 헤드 노드에서 호스트되는 경우 게이트웨이는 Ambari에 들어오는 요청을 해당 노드로 라우팅합니다.

공용 게이트웨이를 통한 액세스는 포트 443(HTTPS), 22 및 23으로 제한됩니다.

|포트 |설명 |
|---|---|
|443|헤드 노드에서 호스팅되는 Ambari 및 기타 웹 UI 또는 REST API에 액세스하는 데 사용됩니다.|
|22|SSH를 사용하여 기본 헤드 노드 또는 에지 노드에 액세스하는 데 사용됩니다.|
|23|SSH를 사용하여 보조 헤드 노드에 액세스하는 데 사용됩니다. 예를 들어 `ssh username@mycluster-ssh.azurehdinsight.net`은 **mycluster**라는 클러스터의 기본 헤드 노드에 연결합니다.|

SSH 사용에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>내부 FQDN(정규화된 도메인 이름)

HDInsight 클러스터의 노드에는 클러스터에서만 액세스할 수 있는 내부 IP 주소와 FQDN이 있습니다. 내부 FQDN 또는 IP 주소를 사용하여 클러스터의 서비스에 액세스할 경우, Ambari를 사용하여 서비스 액세스 시 사용할 IP 또는 FQDN을 확인해야 합니다.

예를 들어 Apache Oozie 서비스는 헤드 노드에서만 실행될 수 있으며, SSH 세션에서 `oozie` 명령을 사용하려면 서비스에 대한 URL이 필요합니다. 이 URL은 다음 명령을 사용하여 Ambari에서 검색할 수 있습니다.

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

이 명령은 `oozie` 명령과 함께 사용할 내부 URL을 포함하는 다음과 유사한 값을 반환합니다.

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Ambari REST API 작업에 대한 자세한 내용은 [Apache Ambari REST API를 사용하여 HDInsight 모니터링 및 관리](hdinsight-hadoop-manage-ambari-rest-api.md)를 참조하세요.

### <a name="accessing-other-node-types"></a>다른 노드 유형에 액세스

다음 방법을 사용하여 인터넷을 통해 직접 액세스할 수 없는 노드에 연결할 수 있습니다.

|메서드 |설명 |
|---|---|
|SSH|SSH를 사용하여 헤드 노드에 연결되면 SSH를 사용하여 헤드 노드에서 클러스터의 다른 노드에 연결할 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.|
|SSH 터널|인터넷에 노출되지 않는 노드 중 하나에서 호스팅되는 웹 서비스에 액세스해야 하는 경우 SSH 터널을 사용해야 합니다. 자세한 내용은 [HDInsight와 SSH 터널 사용](hdinsight-linux-ambari-ssh-tunnel.md) 문서를 참조하세요.|
|Azure Virtual Network|HDInsight 클러스터가 Azure Virtual Network의 일부인 경우 동일한 Virtual Network의 리소스는 클러스터의 모든 노드에 직접 액세스할 수 있습니다. 자세한 내용은 [HDInsight](hdinsight-plan-virtual-network-deployment.md) 문서에 대한 가상 네트워크 계획을 참조하십시오.|

## <a name="how-to-check-on-a-service-status"></a>서비스 상태를 확인하는 방법

헤드 노드에서 실행되는 서비스의 상태를 확인하려면 Ambari 웹 UI 또는 Ambari REST API를 사용합니다.

### <a name="ambari-web-ui"></a>Ambari 웹 UI

Ambari 웹 UI는 `https://CLUSTERNAME.azurehdinsight.net`에서 볼 수 있습니다. **CLUSTERNAME을 클러스터** 이름으로 바꿉니다. 메시지가 표시되면 클러스터의 HTTP 사용자 자격 증명을 입력합니다. 기본 HTTP 사용자 이름은 **admin** 이고 암호는 클러스터를 만들 때 입력한 암호입니다.

Ambari 페이지로 이동하면 설치된 서비스가 페이지 왼쪽에 나열됩니다.

![아파치 암바리 설치 서비스](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

서비스 옆에 상태를 나타내는 여러 아이콘이 표시될 수 있습니다. 서비스와 관련된 모든 경고는 페이지 위쪽의 **Alert(경고)** 링크를 사용하여 볼 수 있습니다.  Ambari는 미리 정의된 몇 가지 경고를 제공합니다.

다음 경고는 클러스터의 가용성을 모니터링하는 데 도움이 됩니다.

| 경고 이름                               | 설명                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 메트릭 모니터 상태                    | 이 경고는 모니터 상태 스크립트에 의해 결정된 메트릭 모니터 프로세스의 상태를 나타냅니다.                                                                                   |
| 암바리 에이전트 하트비트                   | 서버가 에이전트와의 접촉을 잃어버린 경우 이 경고가 트리거됩니다.                                                                                                                        |
| 사육사 서버 프로세스                 | ZooKeeper 서버 프로세스가 네트워크에서 대기 중이라고 판단할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                               |
| IOCache 메타데이터 서버 상태           | IOCache 메타 데이터 서버가 클라이언트 요청에 응답하지 않는 것으로 판단할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                            |
| 저널노드 웹 UI                       | 이 호스트 수준 경고는 JournalNode 웹 UI에 연결할 수 없는 경우 트리거됩니다.                                                                                                                 |
| 스파크2 중고품 서버                     | Spark2 중고품 서버가 켜진 것으로 판단할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                                                |
| 기록 서버 프로세스                   | 이 호스트 수준 경고는 기록 서버 프로세스를 네트워크에서 위로 및 수신 대기하도록 설정하지 않는 경우 트리거됩니다.                                                                |
| 히스토리 서버 웹 UI                    | 이 호스트 수준 경고는 기록 서버 웹 UI에 연결할 수 없는 경우 트리거됩니다.                                                                                                              |
| `ResourceManager`웹 UI                   | 이 호스트 수준 경고는 웹 `ResourceManager` UI에 연결할 수 없는 경우 트리거됩니다.                                                                                                             |
| 노드관리자 상태 요약               | 이 서비스 수준 경고는 비정상 NodeManagers가 있는 경우 트리거됩니다.                                                                                                                    |
| 앱 타임라인 웹 UI                      | 이 호스트 수준 경고는 앱 타임라인 서버 웹 UI에 연결할 수 없는 경우 트리거됩니다.                                                                                                         |
| 데이터노드 상태 요약                  | 이 서비스 수준 경고는 비정상 데이터노드가 있는 경우 트리거됩니다.                                                                                                                       |
| 네임노드 웹 UI                          | NameNode 웹 UI에 연결할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                                                                    |
| 사육사 장애 조치 컨트롤러 프로세스    | ZooKeeper 장애 조치 컨트롤러 프로세스가 네트워크에서 위로 및 수신 대기중임을 확인할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                   |
| 오지 서버 웹 UI                      | Oozie 서버 웹 UI에 연결할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                                                                |
| Oozie 서버 상태                      | Oozie 서버가 클라이언트 요청에 응답하지 않는 것으로 판단할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                      |
| 하이브 메타스토어 프로세스                   | 이 호스트 수준 경고는 Hive Metastore 프로세스가 네트워크에서 위로 및 수신 대기로 결정되지 않는 경우 트리거됩니다.                                                                 |
| 하이브서버2 프로세스                      | 이 호스트 수준 경고는 HiveServer가 클라이언트 요청에 응답하지 않는 것으로 판단할 수 없는 경우 트리거됩니다.                                                                        |
| WebHCat 서버 상태                    | 서버 상태가 정상이 아닌 경우 `templeton` 이 호스트 수준 경고가 트리거됩니다.                                                                                                            |
| 사용 가능한 퍼센트 사육사 서버      | 이 경고는 클러스터의 ZooKeeper 서버 수가 구성된 임계 임계값보다 큰 경우 트리거됩니다. 그것은 ZooKeeper 프로세스 검사의 결과를 집계합니다.     |
| 스파크2 리비 서버                       | Livy2 서버가 위로 결정되지 않으면 이 호스트 수준 경고가 트리거됩니다.                                                                                                        |
| 스파크2 히스토리 서버                    | Spark2 기록 서버가 위로 결정되지 않으면 이 호스트 수준 경고가 트리거됩니다.                                                                                               |
| 메트릭 수집기 프로세스                | 메트릭 수집기임계값과 동일한 초 단위로 구성된 포트에서 대기중임을 확인할 수 없는 경우 이 경고가 트리거됩니다.                                 |
| 메트릭 수집기 - HBase 마스터 프로세스 | 메트릭 수집기의 HBase 마스터 프로세스가 몇 초 만에 구성된 임계 임계값에 대해 네트워크에서 위로 및 수신 대기중임을 확인할 수 없는 경우 이 경고가 트리거됩니다. |
| 사용 가능한 백분율 메트릭 모니터       | 이 경고는 메트릭 모니터 프로세스의 백분율이 네트워크에서 구성된 경고 및 중요 임계값에 대해 대기되지 않는 경우 트리거됩니다.                             |
| 사용 가능한 백분율 노드 관리자           | 클러스터의 다운 NodeManagers 수가 구성된 임계 임계값보다 큰 경우 이 경고가 트리거됩니다. NodeManager 프로세스 검사 결과를 집계합니다.        |
| 노드 관리자 상태                       | 이 호스트 수준 경고는 NodeManager 구성 요소에서 사용할 수 있는 노드 상태 속성을 확인합니다.                                                                                              |
| 노드 관리자 웹 UI                       | NodeManager 웹 UI에 연결할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                                                                 |
| 네임노드 고가용성 상태        | 활성 NameNode 또는 대기 이름 노드가 실행되지 않는 경우 이 서비스 수준 경고가 트리거됩니다.                                                                                     |
| 데이터 노드 프로세스                         | 이 호스트 수준 경고는 개별 DataNode 프로세스를 네트워크에서 위로 및 수신 대기하도록 설정하지 않는 경우 트리거됩니다.                                                         |
| 데이터 노드 웹 UI                          | 이 호스트 수준 경고는 DataNode 웹 UI에 연결할 수 없는 경우 트리거됩니다.                                                                                                                    |
| 백분율 저널노드 사용 가능           | 클러스터의 Down JournalNodes 수가 구성된 임계 임계값보다 큰 경우 이 경고가 트리거됩니다. 그것은 저널 노드 프로세스 검사의 결과를 집계합니다.        |
| 백분율 데이터 노드 사용 가능              | 이 경고는 클러스터의 down DataNodes 수가 구성된 임계 임계값보다 큰 경우 트리거됩니다. DataNode 프로세스 검사 결과를 집계합니다.              |
| 제플린 서버 상태                   | Zeppelin 서버가 클라이언트 요청에 응답하지 않는 것으로 판단할 수 없는 경우 이 호스트 수준 경고가 트리거됩니다.                                                                   |
| 하이브서버2 인터랙티브 프로세스          | 이 호스트 수준 경고는 HiveServerInteractive가 클라이언트 요청에 응답하지 않는 것으로 판단할 수 없는 경우 트리거됩니다.                                                             |
| LLAP 응용 프로그램                         | LLAP 응용 프로그램이 요청에 응답하고 있는 것으로 판단할 수 없는 경우 이 경고가 트리거됩니다.                                                                                    |

각 서비스를 선택하여 해당 서비스에 대한 자세한 내용을 볼 수 있습니다.

서비스 페이지는 각 서비스의 상태 및 구성에 대한 정보를 제공하지만 서비스가 실행 중인 헤드 노드에 대한 정보는 제공하지 않습니다. 이 정보를 보려면 페이지 위쪽의 **Hosts(호스트)** 링크를 사용하세요. 이 페이지에는 헤드 노드를 포함하여 클러스터 내의 호스트가 표시됩니다.

![아파치 암바리 헤드 노드 호스트 목록](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

헤드 노드 중 하나에 대한 링크를 선택하면 해당 노드에서 실행 중인 서비스와 구성 요소가 표시됩니다.

![아파치 암바리 구성 요소 상태](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Ambari 사용에 대한 자세한 내용은 [Apache Ambari Web UI를 사용하여 HDInsight 모니터링 및 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API는 인터넷을 통해 사용할 수 있습니다. HDInsight 공용 게이트웨이는 REST API를 현재 호스팅하는 헤드 노드에 라우팅 요청을 처리합니다.

다음 명령을 사용하여 Ambari REST API를 통해 서비스의 상태를 확인할 수 있습니다.

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* **암호를** HTTP 사용자(관리자) 계정 암호로 바꿉니다.
* **CLUSTERNAME을** 클러스터 이름으로 바꿉니다.
* **SERVICENAME**을 상태를 확인할 서비스의 이름으로 바꿉니다.

예를 들어 **password** 암호를 사용하여 **mycluster**라는 클러스터의 **HDFS** 서비스 상태를 확인하려면 다음 명령을 사용합니다.

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

응답은 다음 JSON과 비슷합니다.

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

URL은 서비스가 현재 **mycluster.wutj3h4ic1zejluqxxqq0g라는**헤드 노드에서 실행되고 있음을 알려줍니다.

상태는 현재 서비스가 실행되고 있음( **STARTED**)을 알려줍니다.

클러스터에 설치된 서비스를 모르는 경우 다음 명령을 사용하여 목록을 검색할 수 있습니다.

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Ambari REST API 작업에 대한 자세한 내용은 [Apache Ambari REST API를 사용하여 HDInsight 모니터링 및 관리](hdinsight-hadoop-manage-ambari-rest-api.md)를 참조하세요.

#### <a name="service-components"></a>서비스 구성 요소

서비스는 개별적으로 상태를 확인하려는 구성 요소를 포함할 수 있습니다. 예를 들어 HDFS는 NameNode 구성 요소를 포함합니다. 구성 요소에 대한 정보를 보려면 명령은 다음과 같습니다.

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

서비스에서 제공하는 구성 요소를 모르는 경우 다음 명령을 사용하여 목록을 검색할 수 있습니다.

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>헤드 노드의 로그 파일에 액세스하는 방법

### <a name="ssh"></a>SSH

SSH를 통해 헤드 노드에 연결된 동안에는 **/var/log**에서 로그 파일을 찾을 수 있습니다. 예를 들어 **/var/log/hadoop-yarn/yarn**은 YARN에 대한 로그를 포함합니다.

각 헤드 노드는 고유한 로그 항목을 포함할 수 있으므로 두 가지 다에서 로그를 확인해야 합니다.

### <a name="sftp"></a>SFTP

또한 SSH 파일 전송 프로토콜 또는 SFTP(보안 파일 전송 프로토콜)을 사용하여 헤드 노드에 연결하고 로그 파일을 직접 다운로드할 수 있습니다.

클러스터의 SSH 사용자 계정 이름 및 SSH 주소를 제공해야 하는 클러스터에 연결하는 경우 SSH 클라이언트를 사용하는 것과 비슷합니다. `sftp username@mycluster-ssh.azurehdinsight.net`)을 입력합니다. 메시지가 표시되면 계정에 대한 암호를 제공하거나 `-i` 매개 변수를 사용하여 공개 키를 제공해야 합니다.

연결되면 프롬프트가 `sftp>` 표시됩니다. 이 프롬프트에서 디렉터리를 변경하고 파일을 업로드 및 다운로드할 수 있습니다. 예를 들어 다음 명령은 디렉터리를 **/var/log/hadoop/hdfs** 디렉터리고 변경하고 디렉터리에 있는 모든 파일을 다운로드합니다.

    cd /var/log/hadoop/hdfs
    get *

사용 가능한 명령 목록의 경우 `sftp>` 프롬프트에 `help`을 입력합니다.

> [!NOTE]  
> 또한 SFTP를 사용하여 연결하는 경우 파일 시스템을 시각화할 수 있는 그래픽 인터페이스가 있습니다. 예를 들어 [MobaXTerm](https://mobaxterm.mobatek.net/)을 사용하면 Windows 탐색기와 비슷한 인터페이스를 사용하는 파일 시스템을 찾아볼 수 있습니다.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Ambari를 사용하여 로그 파일에 액세스하려면 SSH 터널을 사용해야 합니다. 개별 서비스의 웹 인터페이스는 인터넷에 공개적으로 노출되지 않습니다. SSH 터널 사용에 대한 내용은 [SSH 터널 사용](hdinsight-linux-ambari-ssh-tunnel.md) 문서를 참조하세요.

Ambari 웹 UI에서 로그를 보려는 서비스(예: YARN)를 선택합니다. 그런 다음, **빠른 링크**를 사용하여 로그를 볼 헤드 노드를 선택합니다.

![빠른 연결을 사용하여 로그 보기](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>노드 크기를 구성하는 방법

노드의 크기는 클러스터를 만드는 동안에만 선택할 수 있습니다. [HDInsight 가격 책정 페이지](https://azure.microsoft.com/pricing/details/hdinsight/)에서 HDInsight에 사용할 수 있는 다양한 VM 크기의 목록을 찾을 수 있습니다.

클러스터를 만들 때 노드 크기를 지정할 수 있습니다. 다음 정보는 [Azure 포털,](https://portal.azure.com/) [Azure PowerShell 모듈 Az](/powershell/azureps-cmdlets-docs)및 Azure [CLI를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)사용하여 크기를 지정하는 방법에 대한 지침을 제공합니다.

* **Azure Portal**: 클러스터를 만들 때 클러스터에서 사용하는 노드의 크기를 설정할 수 있습니다.

    ![노드 크기 선택이 포함된 클러스터 만들기 마법사의 이미지](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 명령을 사용하는 경우 `--headnode-size`" `--workernode-size`및 `--zookeepernode-size` 매개 변수를 사용하여 머리, 작업자 및 ZooKeeper 노드의 크기를 설정할 수 있습니다.

* **Azure PowerShell**: [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet을 사용하는 경우 `-HeadNodeSize`" `-WorkerNodeSize`및 `-ZookeeperNodeSize` 매개 변수를 사용하여 머리, 작업자 및 ZooKeeper 노드의 크기를 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 설명하는 항목에 대한 자세한 내용은 다음을 참조하십시오.

* [Apache Ambari REST 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Azure CLI 설치 및 구성](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell 모듈 Az 설치 및 구성](/powershell/azure/overview)
* [Apache Ambari를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)
* [Linux 기반 HDInsight 클러스터 프로비전을](hdinsight-hadoop-provision-linux-clusters.md)

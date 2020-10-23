---
title: Azure HDInsight의 고가용성 구성 요소
description: HDInsight 클러스터에서 사용 하는 다양 한 고가용성 구성 요소에 대 한 개요입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4d0405df1863ee47374242ba4fba5b845711d3a1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424514"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight에서 지원 되는 고가용성 서비스

분석 구성 요소에 대 한 최적의 가용성 수준을 제공 하기 위해 HDInsight는 중요 한 서비스의 HA (고가용성)를 보장 하기 위한 고유한 아키텍처를 사용 하 여 개발 되었습니다. 이 아키텍처의 일부 구성 요소는 자동 장애 조치 (failover)를 제공 하기 위해 Microsoft에서 개발 되었습니다. 다른 구성 요소는 특정 서비스를 지원 하기 위해 배포 되는 표준 Apache 구성 요소입니다. 이 문서에서는 hdinsight의 HA 서비스 모델 아키텍처, HDInsight에서 HA 서비스에 대 한 장애 조치 (failover)를 지 원하는 방법 및 다른 서비스 중단 으로부터 복구 하는 모범 사례에 대해 설명 합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용 하지 않는 용어 *종속*용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어를 제거 하는 경우이 문서에서 제거 합니다.

## <a name="high-availability-infrastructure"></a>고가용성 인프라

HDInsight는 자동 장애 조치 (failover) 기능을 사용 하 여 네 가지 기본 서비스의 고가용성을 보장 하기 위해 사용자 지정 인프라를 제공

- Apache Ambari 서버
- Apache YARN에 대 한 애플리케이션 타임라인 서버
- Hadoop MapReduce 용 작업 기록 서버
- Apache Livy

이 인프라는 다양 한 서비스 및 소프트웨어 구성 요소로 구성 되며, 이러한 구성 요소 중 일부는 Microsoft에서 설계 되었습니다. HDInsight 플랫폼에 고유한 구성 요소는 다음과 같습니다.

- 슬레이브 장애 조치 컨트롤러
- 마스터 장애 조치 컨트롤러
- 슬레이브 고가용성 서비스
- Master 고가용성 서비스

![고가용성 인프라](./media/hdinsight-high-availability-components/high-availability-architecture.png)

또한 오픈 소스 Apache 안정성 구성 요소에서 지원 되는 기타 고가용성 서비스가 있습니다. 이러한 구성 요소는 HDInsight 클러스터에도 있습니다.

- HDFS (Hadoop 파일 시스템) NameNode
- YARN ResourceManager
- HBase Master

다음 섹션에서는 이러한 서비스가 함께 작동 하는 방법에 대 한 자세한 정보를 제공 합니다.

## <a name="hdinsight-high-availability-services"></a>HDInsight 고가용성 서비스

Microsoft는 HDInsight 클러스터의 다음 표에서 4 개의 Apache 서비스에 대 한 지원을 제공 합니다. Apache의 구성 요소에서 지원 되는 고가용성 서비스와 구별 하기 위해 *HDINSIGHT HA 서비스*라고 합니다.

| 서비스 | 클러스터 노드 | 클러스터 유형 | 용도 |
|---|---|---|---|
| Apache Ambari 서버| 활성 헤드 노드 | 모두 | 클러스터를 모니터링 하 고 관리 합니다.|
| Apache YARN에 대 한 애플리케이션 타임라인 서버 | 활성 헤드 노드 | Kafka를 제외한 모든 | 클러스터에서 실행 되는 YARN 작업에 대 한 디버깅 정보를 유지 관리 합니다.|
| Hadoop MapReduce 용 작업 기록 서버 | 활성 헤드 노드 | Kafka를 제외한 모든 | MapReduce 작업에 대 한 디버깅 데이터를 유지 관리 합니다.|
| Apache Livy | 활성 헤드 노드 | Spark | REST 인터페이스를 통해 Spark 클러스터와 쉽게 상호 작용할 수 있습니다. |

>[!Note]
> 현재 ESP (HDInsight Enterprise Security Package) 클러스터는 Ambari 서버 고가용성만 제공 합니다. 애플리케이션 타임라인 서버, 작업 기록 서버 및 Livy 모두 headnode0 에서만 실행 되며 Ambari failsover 인 경우 headnode1로 장애 조치 (failover) 되지 않습니다. 응용 프로그램 타임 라인 데이터베이스는 Ambari SQL server가 아닌 headnode0에도 있습니다.

### <a name="architecture"></a>Architecture

각 HDInsight 클러스터에는 각각 활성 및 대기 모드의 두 헤드 노드가 있습니다. HDInsight HA 서비스는 헤드 노드에서만 실행 됩니다. 이러한 서비스는 항상 활성 헤드 노드에서 실행 되 고 대기 하 고 대기 헤드 노드에서 유지 관리 모드로 전환 되어야 합니다.

HA는 HA 서비스의 올바른 상태를 유지 하 고 빠른 장애 조치 (failover)를 제공 하기 위해 분산 응용 프로그램에 대 한 조정 서비스인 Apache ZooKeeper 활용 하 여 활성 헤드 노드 선택을 수행 합니다. 또한 hdinsight는 HDInsight HA 서비스의 장애 조치 (failover) 절차를 조정 하는 몇 가지 백그라운드 Java 프로세스를 제공 합니다. 이러한 서비스는 마스터 장애 조치 (failover) 컨트롤러, 슬레이브 장애 조치 (failover) 컨트롤러, *마스터-ha 서비스*및 *종속 ha 서비스*입니다.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper는 분산 응용 프로그램에 대 한 고성능 조정 서비스입니다. 프로덕션에서 사육 사가 일반적으로 복제 된 모드에서 실행 됩니다. 복제 된 그룹은 복제 된 모드에서 실행 됩니다. 각 HDInsight 클러스터에는 3 개의 사육 아웃 서버가 쿼럼을 구성할 수 있는 3 개의 사육 사 노드가 있습니다. HDInsight에는 서로 동시에 실행 되는 2 개의 사육 사 쿼럼 있습니다. 하나의 쿼럼은 HDInsight HA 서비스를 실행 해야 하는 클러스터의 활성 헤드 노드를 결정 합니다. 다른 쿼럼은 이후 섹션에서 설명 하는 대로 Apache에서 제공 하는 HA 서비스를 조정 하는 데 사용 됩니다.

### <a name="slave-failover-controller"></a>슬레이브 장애 조치 컨트롤러

슬레이브 장애 조치 컨트롤러는 HDInsight 클러스터의 모든 노드에서 실행 됩니다. 이 컨트롤러는 각 노드에서 Ambari 에이전트 및 *슬레이브-서비스* 를 시작 하는 일을 담당 합니다. 활성 헤드 노드에 대 한 첫 번째 사육 사 쿼럼을 정기적으로 쿼리 합니다. 활성 및 대기 헤드 노드가 변경 되 면 슬레이브 장애 조치 (failover) 컨트롤러에서 다음을 수행 합니다.

1. 호스트 구성 파일을 업데이트 합니다.
1. Ambari 에이전트를 다시 시작 합니다.

Ambari 서버를 제외 하 고 대기 헤드 노드에서 HDInsight HA 서비스를 중지 하는 것은 *종속 ha 서비스* 입니다.

### <a name="master-failover-controller"></a>마스터 장애 조치 컨트롤러

마스터 장애 조치 (failover) 컨트롤러는 두 헤드 노드에서 실행 됩니다. 두 마스터 장애 조치 (failover) 컨트롤러는 첫 번째 사육 사 쿼럼과 통신 하 여 실행 중인 헤드 노드를 활성 헤드 노드로 추천 합니다.

예를 들어 헤드 노드 0의 마스터 장애 조치 컨트롤러가 선거를 적용 하는 경우 다음 변경 내용이 적용 됩니다.

1. 헤드 노드 0이 활성화 됩니다.
1. 마스터 장애 조치 (failover) 컨트롤러는 헤드 노드 0에서 Ambari 서버 및 *마스터-ha 서비스* 를 시작 합니다.
1. 다른 마스터 장애 조치 (failover) 컨트롤러는 헤드 노드 1에서 Ambari 서버 및 *마스터-ha 서비스* 를 중지 합니다.

마스터-ha 서비스는 활성 헤드 노드에서 실행 되 고, 대기 헤드 노드에서 HDInsight HA 서비스 (Ambari 서버 제외)를 중지 하 고 활성 헤드 노드에서 시작 합니다.

### <a name="the-failover-process"></a>장애 조치 (failover) 프로세스

![장애 조치(Failover) 프로세스](./media/hdinsight-high-availability-components/failover-steps.png)

상태 모니터는 마스터 장애 조치 (failover) 컨트롤러와 함께 각 헤드 노드에서 실행 되어 사육 사 쿼럼에 하트 비트 알림을 보냅니다. 헤드 노드는이 시나리오에서 HA 서비스로 간주 됩니다. 상태 모니터는 각 고가용성 서비스가 정상 상태이 고 리더십 선거에 가입할 준비가 되었는지 확인 합니다. 그렇다면이 헤드 노드는 선거에서 경쟁 합니다. 그렇지 않으면 다시 준비 될 때까지 선거를 종료 합니다.

대기 헤드 노드가 리더십을 달성 하 고 활성화 된 경우 (예: 이전 액티브 노드에 오류가 발생 한 경우) 해당 마스터 장애 조치 (failover) 컨트롤러에서 모든 HDInsight HA 서비스를 시작 합니다. 또한 마스터 장애 조치 (failover) 컨트롤러는 다른 헤드 노드에서 이러한 서비스를 중지 합니다.

서비스가 다운 되거나 비정상 인 경우와 같은 HDInsight HA 서비스 오류의 경우 마스터 장애 조치 (failover) 컨트롤러는 헤드 노드 상태에 따라 서비스를 자동으로 다시 시작 하거나 중지 해야 합니다. 사용자는 두 헤드 노드에서 HDInsight HA 서비스를 수동으로 시작 하면 안 됩니다. 대신 자동 또는 수동 장애 조치 (failover)를 사용 하 여 서비스 복구를 지원 합니다.

### <a name="inadvertent-manual-intervention"></a>실수로 수동 작업

HDInsight HA 서비스는 활성 헤드 노드에서만 실행 해야 하며 필요한 경우 자동으로 다시 시작 됩니다. 개별 HA 서비스는 자체 상태 모니터를 갖지 않으므로 개별 서비스 수준에서 장애 조치 (failover)를 트리거할 수 없습니다. 장애 조치 (Failover)는 서비스 수준이 아니라 노드 수준에서 보장 됩니다.

### <a name="some-known-issues"></a>몇 가지 알려진 문제

- 대기 헤드 노드에서 HA 서비스를 수동으로 시작 하는 경우 다음 장애 조치 (failover)가 발생할 때까지 중지 되지 않습니다. HA 서비스를 두 헤드 노드에서 실행 하는 경우 다음과 같은 몇 가지 잠재적인 문제가 있습니다. Ambari UI에 액세스할 수 없습니다. Ambari throw 오류, YARN, Spark 및 Oozie 작업이 중단 될 수 있습니다.

- 활성 헤드 노드의 HA 서비스가 중지 되 면 다음 장애 조치 (failover)가 발생 하거나 마스터 장애 조치 (failover) 컨트롤러/마스터-HA 서비스가 다시 시작 될 때까지 다시 시작 되지 않습니다. 활성 헤드 노드에서 하나 이상의 HA 서비스가 중지 되는 경우, 특히 Ambari 서버가 중지 되 면 Ambari UI에 액세스할 수 없으며, 다른 잠재적인 문제에 YARN, Spark 및 Oozie 작업 실패가 포함 됩니다.

## <a name="apache-high-availability-services"></a>Apache 고가용성 서비스

Apache는 HDFS NameNode, YARN ResourceManager 및 HBase Master에 대 한 고가용성을 제공 하며,이는 HDInsight 클러스터 에서도 사용할 수 있습니다. HDInsight HA 서비스와 달리 ESP 클러스터에서 지원 됩니다. Apache HA 서비스는 두 번째 사육 사 쿼럼 (위 섹션에 설명)과 통신 하 여 활성/대기 상태를 선택 하 고 자동 장애 조치 (failover)를 수행 합니다. 다음 섹션에서는 이러한 서비스가 작동 하는 방식에 대해 자세히 설명 합니다.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>HDFS (Hadoop 분산 파일 시스템) NameNode

Apache Hadoop 2.0 이상을 기반으로 하는 HDInsight 클러스터는 NameNode 고가용성을 제공 합니다. 자동 장애 조치 (failover)를 위해 구성 된 헤드 노드에서 실행 되는 두 개의 Namenode 있습니다. Namenode는 *Zkfailovercontroller* 를 사용 하 여 활성/대기 상태를 선택 하기 위해 사육 사와 통신 합니다. *Zkfailovercontroller* 는 두 헤드 노드에서 실행 되며 위의 마스터 장애 조치 (failover) 컨트롤러와 동일한 방식으로 작동 합니다.

두 번째 사육 사 쿼럼은 첫 번째 쿼럼을 독립적 이므로 활성 헤드 노드에서 활성 NameNode 실행 되지 않을 수 있습니다. 활성 NameNode 비정상 상태 이거나 비정상 상태 이면 대기 NameNode는 선거를 적용 하 고 활성화 됩니다.

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

Apache Hadoop 2.4 이상을 기반으로 하는 HDInsight 클러스터는 YARN ResourceManager 고가용성을 지원 합니다. 헤드 노드 0 및 헤드 노드 1에서 각각 실행 되는 두 개의 ResourceManagers, rm1 및 rm2가 있습니다. NameNode와 마찬가지로 YARN ResourceManager도 자동 장애 조치 (failover)를 위해 구성 됩니다. 현재 활성 ResourceManager가 다운 되거나 응답 하지 않는 경우 다른 ResourceManager가 자동으로 활성화 되도록 선택 됩니다.

YARN ResourceManager는 포함 된 *ActiveStandbyElector* 를 오류 감지기 및 리더 elector 사용 합니다. HDFS NameNode와 달리 YARN ResourceManager에는 별도의 ZKFC 데몬이 필요 하지 않습니다. 활성 ResourceManager는 Apache 사육 사에 상태를 기록 합니다.

YARN ResourceManager의 고가용성은 NameNode 및 기타 HDInsight HA 서비스와는 독립적입니다. 활성 NameNode가 실행 중인 헤드 노드나 활성 헤드 노드에서 활성 ResourceManager가 실행 되지 않을 수 있습니다. YARN ResourceManager high availability에 대 한 자세한 내용은 [Resourcemanager 고가용성](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)을 참조 하세요.

### <a name="hbase-master"></a>HBase Master

HDInsight HBase 클러스터는 HBase Master 고가용성을 지원 합니다. 헤드 노드에서 실행 되는 다른 HA 서비스와는 달리, HBase 마스터는 세 개의 사육 아웃 노드에서 실행 되며,이 중 하나는 활성 마스터나 다른 두 개는 대기 상태입니다. NameNode와 마찬가지로, 리더 선거에 대해 Apache 사육 사를 사용 하는 좌표를 HBase Master 현재 활성 마스터에 문제가 있는 경우 자동 장애 조치를 수행 합니다. 언제 든 지 활성 HBase Master 하나만 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
- [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)

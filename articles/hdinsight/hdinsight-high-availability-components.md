---
title: Azure HDInsight의 고가용성 구성 요소
description: HDInsight 클러스터에서 사용하는 다양한 고가용성 구성 요소개요
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069622"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight에서 지원하는 고가용성 서비스

 분석 구성 요소에 대한 최적의 가용성 수준을 제공하기 위해 HDInsight는 중요한 서비스의 고가용성(HA)을 보장하기 위한 고유한 아키텍처로 개발되었습니다. 이 아키텍처의 일부 구성 요소는 자동 장애 조치(failover)를 제공하기 위해 Microsoft에서 개발했습니다. 다른 구성 요소는 특정 서비스를 지원하기 위해 배포되는 표준 아파치 구성 요소입니다. 이 문서에서는 HDInsight의 HA 서비스 모델의 아키텍처, HDInsight가 HA 서비스에 대한 장애 조치(failover)를 지원하는 방법 및 다른 서비스 중단으로부터 복구하는 모범 사례에 대해 설명합니다.

## <a name="high-availability-infrastructure"></a>고가용성 인프라

HDInsight는 자동 장애 조치 기능을 갖춘 4가지 기본 서비스가 고가용성인지 확인하기 위한 맞춤형 인프라를 제공합니다.

- 아파치 암바리 서버
- 아파치 YARN용 애플리케이션 타임라인 서버
- 하두프 맵을 위한 작업 기록 서버감소
- Apache Livy

이 인프라는 여러 서비스 및 소프트웨어 구성 요소로 구성되며, 그 중 일부는 Microsoft에서 설계했습니다. 다음 구성 요소는 HDInsight 플랫폼에 고유합니다.

- 슬레이브 장애 조치 컨트롤러
- 마스터 장애 조치 컨트롤러
- 슬레이브 고가용성 서비스
- 고가용성 서비스 마스터

![고가용성 인프라](./media/hdinsight-high-availability-components/high-availability-architecture.png)

오픈 소스 아파치 신뢰성 구성 요소에 의해 지원 되는 다른 고가용성 서비스도 있다. 이러한 구성 요소는 HDInsight 클러스터에도 있습니다.

- 하두프 파일 시스템 (HDFS) 네임 노드
- 원사 리소스 관리자
- H베이스 마스터

다음 섹션에서는 이러한 서비스가 함께 작동하는 방식에 대해 자세히 설명합니다.

## <a name="hdinsight-high-availability-services"></a>HDInsight 고가용성 서비스

Microsoft는 HDInsight 클러스터의 다음 표에 있는 4개의 아파치 서비스에 대한 지원을 제공합니다. 아파치의 구성 요소에서 지원하는 고가용성 서비스와 구별하기 위해 *HDInsight HA*서비스라고합니다.

| 서비스 | 클러스터 노드 | 클러스터 유형 | 목적 |
|---|---|---|---|
| 아파치 암바리 서버| 액티브 헤드노드 | 모두 | 클러스터를 모니터링하고 관리합니다.|
| 아파치 YARN용 애플리케이션 타임라인 서버 | 액티브 헤드노드 | 카프카를 제외한 모든 | 클러스터에서 실행 중인 YARN 작업에 대한 디버깅 정보를 유지 관리합니다.|
| 하두프 맵을 위한 작업 기록 서버감소 | 액티브 헤드노드 | 카프카를 제외한 모든 | MapReduce 작업에 대한 디버깅 데이터를 유지 관리합니다.|
| Apache Livy | 액티브 헤드노드 | Spark | REST 인터페이스를 통해 스파크 클러스터와 손쉽게 상호 작용 |

>[!Note]
> HDInsight 엔터프라이즈 보안 패키지(ESP) 클러스터는 현재 Ambari 서버의 고가용성만 제공합니다.

### <a name="architecture"></a>Architecture

각 HDInsight 클러스터에는 각각 활성 모드와 대기 모드에서 두 개의 헤드노드가 있습니다. HDInsight HA 서비스는 헤드노드에서만 실행됩니다. 이러한 서비스는 항상 활성 헤드노드에서 실행되어야 하며 중지하고 대기 헤드노드의 유지 관리 모드에 넣어야 합니다.

HA 서비스의 올바른 상태를 유지하고 빠른 장애 조치(failover)를 제공하기 위해 HDInsight는 분산 응용 프로그램을 위한 조정 서비스인 아파치 사육사를 활용하여 적극적인 헤드노드 선거를 실시합니다. HDInsight는 또한 HDInsight HA 서비스에 대한 장애 조치 절차를 조정하는 몇 가지 배경 Java 프로세스를 제공합니다. 이러한 서비스는 다음과 같습니다: 마스터 장애 조치 컨트롤러, 슬레이브 장애 조치 컨트롤러, *마스터 하-서비스*및 *슬레이브 하 서비스.*

### <a name="apache-zookeeper"></a>아파치 사육사

아파치 동물원은 분산 응용 프로그램을위한 고성능 조정 서비스입니다. 프로덕션 환경에서 ZooKeeper는 일반적으로 복제된 모드로 실행되며, 여기서 복제된 ZooKeeper 서버 그룹이 쿼럼을 형성합니다. 각 HDInsight 클러스터에는 3개의 ZooKeeper 서버가 쿼럼을 형성할 수 있는 3개의 ZooKeeper 노드가 있습니다. HDInsight에는 두 개의 ZooKeeper 쿼럼이 서로 병렬로 실행됩니다. 한 쿼럼은 HDInsight HA 서비스가 실행되어야 하는 클러스터의 활성 헤드노드를 결정합니다. 또 다른 쿼럼은 아파치에서 제공하는 HA 서비스를 조정하는 데 사용됩니다.

### <a name="slave-failover-controller"></a>슬레이브 장애 조치 컨트롤러

슬레이브 장애 조치 컨트롤러는 HDInsight 클러스터의 모든 노드에서 실행됩니다. 이 컨트롤러는 각 노드에서 Ambari 에이전트 및 *슬레이브 하 서비스를* 시작하는 작업을 담당합니다. 활성 헤드노드에 대한 첫 번째 ZooKeeper 쿼럼을 주기적으로 쿼리합니다. 활성 및 대기 헤드노드가 변경되면 슬레이브 장애 조치 컨트롤러는 다음을 수행합니다.

1. 호스트 구성 파일을 업데이트합니다.
1. 암바리 에이전트를 다시 시작합니다.

*슬레이브 하 서비스는* 대기 헤드노드에서 HDInsight HA 서비스(암바리 서버 제외)를 중지할 책임이 있습니다.

### <a name="master-failover-controller"></a>마스터 장애 조치 컨트롤러

마스터 장애 조치 컨트롤러는 두 헤드노드에서 실행됩니다. 두 마스터 장애 조치 컨트롤러는 첫 번째 ZooKeeper 쿼럼과 통신하여 활성 헤드노드로 실행 중인 헤드노드를 지명합니다.

예를 들어 headnode 0의 마스터 장애 조치 컨트롤러가 선거에서 이기면 다음과 같은 변경 사항이 수행됩니다.

1. 헤드노드 0이 활성화됩니다.
1. 마스터 장애 조치 컨트롤러는 Ambari 서버와 헤드노드 0에서 *마스터 하 서비스를* 시작합니다.
1. 다른 마스터 장애 조치 컨트롤러는 Ambari 서버와 헤드노드 1의 *마스터 하 서비스를* 중지합니다.

마스터 하 서비스는 활성 헤드 노드에서만 실행되며 대기 헤드 노드에서 HDInsight HA 서비스(Ambari 서버 제외)를 중지하고 활성 헤드노드에서 시작합니다.

### <a name="the-failover-process"></a>장애 조치 프로세스

![장애 조치(Failover) 프로세스](./media/hdinsight-high-availability-components/failover-steps.png)

상태 모니터는 마스터 장애 조치 컨트롤러와 함께 각 헤드노드에서 실행되어 사육사 쿼럼에 hearbeat 알림을 보냅니다. 이 시나리오에서는 헤드노드가 HA 서비스로 간주됩니다. 상태 모니터는 각 고가용성 서비스가 정상인지, 리더십 선거에 참여할 준비가 되었는지 확인합니다. 그렇다면 이 헤드노드는 선거에서 경쟁하게 됩니다. 그렇지 않으면 다시 준비가 될 때까지 선거를 그만둘 것입니다.

대기 헤드노드가 리더십을 달성하고 활성화되면(예: 이전 활성 노드에 장애가 발생한 경우) 마스터 장애 조치 컨트롤러가 모든 HDInsight HA 서비스를 시작합니다. 마스터 장애 조치 컨트롤러는 다른 헤드노드에서도 이러한 서비스를 중지합니다.

서비스가 다운되거나 비정상인 경우 HDInsight HA 서비스 오류의 경우 마스터 장애 조치 컨트롤러는 헤드노드 상태에 따라 서비스를 자동으로 다시 시작하거나 중지해야 합니다. 사용자는 두 헤드 노드에서 HDInsight HA 서비스를 수동으로 시작해서는 안 됩니다. 대신 자동 또는 수동 장애 조치(failover)를 허용하여 서비스가 복구되도록 합니다.

### <a name="inadvertent-manual-intervention"></a>의도하지 않은 수동 개입

HDInsight HA 서비스는 활성 헤드노드에서만 실행되어야 하며 필요한 경우 자동으로 다시 시작됩니다. 개별 HA 서비스에는 자체 상태 모니터가 없으므로 개별 서비스 수준에서 장애 조치(failover)를 트리거할 수 없습니다. 장애 조치(Failover)는 서비스 수준이 아닌 노드 수준에서 보장됩니다.

### <a name="some-known-issues"></a>일부 알려진 문제

- 대기 헤드노드에서 HA 서비스를 수동으로 시작하면 다음 장애 조치(failover)가 발생할 때까지 중지되지 않습니다. HA 서비스가 두 헤드노드에서 실행되는 경우 몇 가지 잠재적인 문제가 있습니다: Ambari UI에 액세스할 수 없습니다.

- 활성 헤드노드의 HA 서비스가 중지되면 다음 장애 조치(failover)가 발생하거나 마스터 장애 조치 컨트롤러/마스터 하 서비스가 다시 시작될 때까지 다시 시작되지 않습니다. 하나 이상의 HA 서비스가 활성 헤드노드에서 중지되는 경우, 특히 Ambari 서버가 중지될 때 Ambari UI에 액세스할 수 없으며 YARN, 스파크 및 Oozie 작업 실패가 발생할 수 있습니다.

## <a name="apache-high-availability-services"></a>아파치 고가용성 서비스

아파치는 HDFS 네임노드, YARN 리소스매니저 및 HdInsight 클러스터에서도 사용할 수 있는 HBase 마스터를 위한 고가용성을 제공합니다. HDInsight HA 서비스와 달리 ESP 클러스터에서 지원됩니다. 아파치 HA 서비스는 두 번째 ZooKeeper 쿼럼(위의 섹션에 설명)과 통신하여 활성/대기 상태를 선택시키고 자동 장애 조치(failover)를 수행합니다. 다음 섹션에서는 이러한 서비스의 작동 방식을 자세히 설명합니다.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>하두롭 분산 파일 시스템 (HDFS) 네임 노드

아파치 하두프 2.0 이상의 HDInsight 클러스터는 네임노드 고가용성을 제공한다. 자동 장애 조치(failover)를 위해 구성된 두 개의 NameNodes가 헤드노드에서 실행되고 있습니다. NameNodes는 *ZKFailoverController를* 사용하여 사육사와 통신하여 활성/대기 상태를 선택합니다. *ZKFailoverController는* 두 헤드노드에서 실행되며 위의 마스터 장애 조치 컨트롤러와 동일한 방식으로 작동합니다.

두 번째 사육사 쿼럼은 첫 번째 쿼럼과 독립적이므로 활성 NameNode가 활성 헤드노드에서 실행되지 않을 수 있습니다. 활성 NameNode가 죽었거나 비정상이면 대기 NameNode가 선거에서 승리하고 활성화됩니다.

### <a name="yarn-resourcemanager"></a>원사 리소스 관리자

아파치 하두프 2.4 이상에 기반한 HDInsight 클러스터는 YARN 리소스관리자고가용성을 지원합니다. 각각 헤드 노드 0과 헤드 노드 1에서 실행되는 두 개의 ResourceManagers, rm1 및 rm2가 있습니다. 네임노드와 마찬가지로 YARN 리소스관리자도 자동 장애 조치(failover)를 위해 구성됩니다. 현재 활성 ResourceManager가 다운되거나 응답하지 않을 때 다른 ResourceManager가 자동으로 활성화하도록 선출됩니다.

YARN 리소스 관리자는 내장 된 *ActiveStandbyElector를* 실패 감지기 및 리더 선거인으로 사용합니다. HDFS 네임노드와 달리 YARN 리소스 관리자는 별도의 ZKFC 데몬이 필요하지 않습니다. 활성 ResourceManager는 아파치 사육사에 상태를 기록합니다.

YARN 리소스 관리자의 고가용성은 네임노드 및 기타 HDInsight HA 서비스와 독립적입니다. 활성 ResourceManager활성 NameNode가 실행 중인 활성 헤드노드 또는 헤드노드에서 실행되지 않을 수 있습니다. YARN 리소스 관리자 고가용성에 대한 자세한 내용은 [ResourceManager 고가용성](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)을 참조하십시오.

### <a name="hbase-master"></a>H베이스 마스터

HDInsight HBase 클러스터는 HBase 마스터 고가용성을 지원합니다. 헤드 노드에서 실행되는 다른 HA 서비스와 달리 HBase 마스터는 3개의 사육사 노드에서 실행되며, 그 중 하나는 활성 마스터이고 다른 두 개는 대기 상태입니다. 네임 노드와 마찬가지로 HBase 마스터는 리더 선거를 위해 아파치 사육사와 조정하고 현재 활성 마스터에 문제가있을 때 자동 장애 조치(failover)를 수행합니다. 언제든지 활성 HBase 마스터가 하나만 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
- [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)

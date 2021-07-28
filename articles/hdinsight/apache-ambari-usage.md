---
title: Azure HDInsight에서 Apache Ambari 사용
description: Azure HDInsight에서 Apache Ambari를 사용하는 방법에 대한 설명입니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 35338cad4364abbf233e3da81b05ba912959ed65
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064493"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Ambari 사용

HDInsight는 클러스터 배포 및 관리에 Apache Ambari를 사용합니다. Ambari 에이전트는 모든 노드(헤드 노드, 작업자 노드, zookeeper 및 에지 노드, 있는 경우)에서 실행됩니다. Ambari 서버는 헤드 노드에서만 실행됩니다. Ambari 서버의 인스턴스는 한 번에 하나씩만 실행해야 합니다. 이는 HDInsight 장애 조치(failover) 컨트롤러에 의해 제어됩니다. 다시 부팅 또는 유지 관리를 위해 헤드 노드 중 하나가 다운되면 다른 헤드 노드가 활성화되고 두 번째 헤드 노드의 Ambari 서버가 시작됩니다.

모든 클러스터 구성은 [Ambari UI](./hdinsight-hadoop-manage-ambari.md)를 통해 수행해야 하며 노드가 다시 시작되면 로컬 변경 사항을 덮어씁니다.

## <a name="failover-controller-services"></a>장애 조치(failover) 컨트롤러 서비스

HDInsight 장애 조치(failover) 컨트롤러는 현재 활성 헤드 노드를 가리키는 헤드 노드 호스트의 IP 주소를 업데이트하는 것도 담당합니다. 모든 Ambari 에이전트는 헤드 노드 호스트에 해당 상태 및 하트비트를 보고하도록 구성됩니다. 장애 조치(failover) 컨트롤러는 클러스터의 모든 노드에서 실행되는 서비스 집합입니다. 실행되고 있지 않으면 헤드 노드 장애 조치(failover)가 제대로 작동하지 않을 수 있으며 Ambari 서버에 액세스하려고 할 때 HTTP 502가 표시됩니다.

어떤 헤드 노드가 활성 상태인지 확인하는 한 가지 방법은 클러스터의 노드 중 하나에 SSH를 수행한 다음 `ping headnodehost`를 실행하고 두 헤드 노드의 IP와 비교하는 것입니다.

장애 조치(failover) 컨트롤러 서비스가 실행되고 있지 않으면 헤드 노드 장애 조치(failover)가 제대로 수행되지 않아 Ambari 서버가 실행되지 않을 수 있습니다. 장애 조치(failover) 컨트롤러 서비스가 실행 중인지 확인하려면 다음을 실행합니다.

```bash
ps -ef | grep failover
```

## <a name="logs"></a>로그

활성 헤드 노드에서 다음과 같이 Ambari 서버 로그를 확인할 수 있습니다.

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

클러스터의 모든 노드에서 다음과 같이 Ambari 에이전트 로그를 확인할 수 있습니다.

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>서비스 시작 시퀀스

부팅 시 서비스 시작 시퀀스는 다음과 같습니다.

1. Hdinsight 에이전트가 장애 조치(failover) 컨트롤러 서비스를 시작합니다.
1. 장애 조치(failover) 컨트롤러 서비스가 모든 노드에서 Ambari 에이전트를 시작하고 활성 헤드 노드에서 Ambari 서버를 시작합니다.

## <a name="ambari-database"></a>Ambari 데이터베이스

HDInsight는 Ambari 서버의 데이터베이스 역할을 하는 SQL Database에서 데이터베이스를 만듭니다. 기본 [서비스 계층은 S0](../azure-sql/database/elastic-pool-scale.md)입니다.

클러스터를 만들 때 작업자 노드 수가 16개를 초과하는 클러스터의 경우 S2가 데이터베이스 서비스 계층입니다.

## <a name="takeaway-points"></a>핵심 포인트

문제를 해결하기 위해 서비스를 다시 시작하려고 하지 않는 한 Ambari 서버 또는 Ambari 에이전트 서비스를 수동으로 시작/중지하지 마세요. 강제로 장애 조치(failover)를 수행하려면 활성 헤드 노드를 다시 부팅하면 됩니다.

클러스터 노드에서 구성 파일을 수동으로 수정하지 말고 Ambari UI에서 작업을 수행하도록 합니다.

## <a name="property-values-in-esp-clusters"></a>ESP 클러스터의 속성 값

HDInsight 4.0 Enterprise Security Package 클러스터에서 변수 구분 기호로 쉼표 대신 파이프(`|`)를 사용합니다. 아래에 예가 나와 있습니다.

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

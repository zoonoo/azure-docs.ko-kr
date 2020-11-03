---
title: Azure HDInsight의 Apache Ambari 사용
description: Azure HDInsight에서 Apache Ambari를 사용 하는 방법에 대 한 설명입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cfb83c4ec9972fda3813d414583bc73edeef3229
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285971"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari 사용

HDInsight는 클러스터 배포 및 관리에 Apache Ambari를 사용 합니다. Ambari 에이전트는 모든 노드 (헤드 노드, 작업자 노드, 사육 사 및 지 노드에 airpal가 있는 경우)에서 실행 됩니다. Ambari 서버는 헤드 노드 (hn0 또는 h n 1) 에서만 실행 됩니다. 한 번에 하나의 Ambari 서버 인스턴스만 실행 됩니다. HDInsight 장애 조치 (failover) 컨트롤러에 의해 제어 됩니다. 헤드 노드 중 하나를 다시 부팅 하거나 유지 관리 하는 경우 다른 헤드 노드가 활성화 되 고 두 번째 헤드 노드의 Ambari 서버가 시작 됩니다.

모든 클러스터 구성은 [AMBARI UI](./hdinsight-hadoop-manage-ambari.md)를 통해 수행 해야 합니다. 노드를 다시 시작 하면 로컬 변경 내용이 덮어쓰여집니다.

## <a name="failover-controller-services"></a>장애 조치 컨트롤러 서비스

또한 HDInsight 장애 조치 (failover) 컨트롤러는 현재 활성 헤드 노드를 가리키는 헤드 노드 호스트의 IP 주소를 업데이트 해야 합니다. 모든 Ambari 에이전트는 해당 상태 및 하트 비트를 헤드 노드 호스트에 보고 하도록 구성 됩니다. 장애 조치 컨트롤러는 클러스터의 모든 노드에서 실행 되는 서비스 집합으로, 실행 되지 않는 경우 헤드 노드 장애 조치 (failover)가 제대로 작동 하지 않을 수 있으며 Ambari 서버에 액세스 하려고 할 때 HTTP 502이 발생 합니다.

활성 상태인 헤드 노드를 확인 하려면 한 가지 방법으로 클러스터의 노드 중 하나에 ssh를 실행 하 `ping headnodehost` 고 두 헤드 노드의 IP를 실행 하 고 비교 합니다.

장애 조치 (failover) 컨트롤러 서비스가 실행 되지 않는 경우 헤드 노드 장애 조치 (failover)가 제대로 수행 되지 않아 Ambari 서버를 실행 하지 못할 수 있습니다. 장애 조치 (failover) 컨트롤러 서비스가 실행 중인지 확인 하려면 다음을 실행 합니다.

```bash
ps -ef | grep failover
```

## <a name="logs"></a>로그

활성 헤드 노드에서 Ambari 서버 로그를 확인할 수 있습니다.

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

클러스터의 노드에서 Ambari agent 로그를 확인할 수 있습니다.

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>서비스 시작 시퀀스

부팅 시에 시작 되는 서비스의 순서는 다음과 같습니다.

1. Hdinsight-에이전트가 장애 조치 (failover) 컨트롤러 서비스를 시작 합니다.
1. 장애 조치 (Failover) 컨트롤러 서비스는 모든 노드 및 활성 헤드 노드의 Ambari 서버에서 Ambari agent를 시작 합니다.

## <a name="ambari-database"></a>Ambari 데이터베이스

HDInsight는 Ambari 서버의 데이터베이스 역할을 하는 SQL Database에서 데이터베이스를 만듭니다. 기본 [서비스 계층은 S0](../azure-sql/database/elastic-pool-scale.md)입니다.

클러스터를 만들 때 작업자 노드 수가 16 개를 초과 하는 클러스터의 경우 S2는 데이터베이스 서비스 계층입니다.

## <a name="takeaway-points"></a>요점은 points

문제를 해결 하기 위해 서비스를 다시 시작 하려고 하지 않는 한 ambari 또는 ambari 서비스를 수동으로 시작/중지 하지 마십시오. 강제로 장애 조치 (failover)를 수행 하려면 활성 헤드 노드를 다시 부팅 하면 됩니다.

모든 클러스터 노드에서 구성 파일을 수동으로 수정 하지 마십시오. Ambari UI에서 작업을 수행 하도록 합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
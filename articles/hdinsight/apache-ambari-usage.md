---
title: Azure HDInsight에서 아파치 암바리 사용
description: Azure HDInsight에서 아파치 암바리가 어떻게 사용되는지에 대해 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067397"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight에서 아파치 암바리 사용

HDInsight는 클러스터 배포 및 관리를 위해 아파치 암바리를 사용합니다. Ambari 에이전트는 모든 노드(헤드노드, 작업자 노드, 사육사 및 에지노드가 있는 경우)에서 실행됩니다. Ambari 서버는 헤드노드(hn0 또는 hn1)에서만 실행됩니다. Ambari 서버의 인스턴스는 한 번에 한 번만 실행됩니다. 이것은 HDInsight 장애 조치 컨트롤러에 의해 제어됩니다. 헤드 노드 중 하나가 재부팅 또는 유지 관리를 위해 다운되면 다른 헤드 노드가 활성화되고 두 번째 헤드 노드의 Ambari 서버가 시작됩니다.

모든 클러스터 구성은 [Ambari UI를](./hdinsight-hadoop-manage-ambari.md)통해 수행되어야 하며 노드가 다시 시작될 때 로컬 변경이 덮어씁니다.

## <a name="failover-controller-services"></a>장애 조치 컨트롤러 서비스

HDInsight 장애 조치 컨트롤러는 현재 활성 헤드 노드를 가리키는 헤드노드 호스트의 IP 주소를 업데이트하는 것도 담당합니다. 모든 Ambari 에이전트는 헤드노드 호스트에 상태 및 하트비트를 보고하도록 구성됩니다. 장애 조치 컨트롤러는 클러스터의 모든 노드에서 실행되는 서비스 집합으로, 실행되지 않으면 헤드노드 장애 조치(failover)가 제대로 작동하지 않을 수 있으며 Ambari 서버에 액세스하려고 할 때 HTTP 502로 끝납니다.

어떤 헤드노드가 활성 상태인지 확인하려면 클러스터의 노드 중 하나에 ssh를 실행한 다음 IP를 실행하고 `ping headnodehost` 두 헤드노드의 노드노드와 비교하는 것입니다.

장애 조치 컨트롤러 서비스가 실행되지 않으면 헤드노드 장애 조치(failover)가 제대로 발생하지 않아 Ambari 서버가 실행되지 않을 수 있습니다. 장애 조치 컨트롤러 서비스가 실행 중인지 확인하려면 다음을 실행하십시오.

```bash
ps -ef | grep failover
```

## <a name="logs"></a>로그

활성 헤드노드에서 Ambari 서버 로그를 다음 에서 확인할 수 있습니다.

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

클러스터의 모든 노드에서 Ambari 에이전트 로그를 다음에서 확인할 수 있습니다.

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>서비스 시작 시퀀스

부팅 시 서비스 시작 순서입니다.

1. Hdinsight-에이전트는 장애 조치 컨트롤러 서비스를 시작합니다.
1. 장애 조치 컨트롤러 서비스는 모든 노드에서 Ambari 에이전트를 시작하고 활성 헤드노드의 Ambari 서버를 시작합니다.

## <a name="ambari-database"></a>암바리 데이터베이스

HDInsight는 Ambari 서버의 데이터베이스역할을 하기 위해 후드 아래에 SQL Azure 데이터베이스를 만듭니다. 기본 [서비스 계층은 S0입니다.](../sql-database/sql-database-elastic-pool-scale.md)

클러스터를 만들 때 작업자 노드 수가 16보다 큰 모든 클러스터의 경우 S2는 데이터베이스 서비스 계층입니다.

## <a name="takeaway-points"></a>테이크아웃 포인트

문제를 해결하기 위해 서비스를 다시 시작하려고 하지 않는 한 ambari-server 또는 ambari-agent 서비스를 수동으로 시작/중지하지 마십시오. 장애 조치(failover)를 강제로 적용하려면 활성 헤드노드를 다시 부팅할 수 있습니다.

클러스터 노드의 구성 파일을 수동으로 수정하지 말고 Ambari UI가 작업을 수행하도록 하십시오.

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari-rest-api.md)

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

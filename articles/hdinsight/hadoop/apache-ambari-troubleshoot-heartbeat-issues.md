---
title: Azure HDInsight의 Apache Ambari 하트비트 문제
description: Azure HDInsight에서 아파치 암바리 하트 비트 문제에 대 한 다양 한 이유의 검토
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057076"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari 하트비트 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="scenario-high-cpu-utilization"></a>시나리오: 높은 CPU 사용률

### <a name="issue"></a>문제

Ambari 에이전트는 CPU 사용률이 높으며, 이로 인해 일부 노드의 경우 Ambari 에이전트 하트비트가 손실된다는 Ambari UI의 경고가 발생합니다. 하트비트 손실 경고는 일반적으로 일시적입니다.

### <a name="cause"></a>원인

다양한 암바리 에이전트 버그로 인해 드문 경우, ambari 에이전트는 높은 (100에 가까운) CPU 사용률을 가질 수 있습니다.

### <a name="resolution"></a>해결 방법

1. 암바리 에이전트의 프로세스 ID(pid)를 식별합니다.

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 그 후 다음 명령을 실행하여 CPU 사용률을 표시합니다.

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 문제를 완화하기 위해 ambari 에이전트를 다시 시작하십시오.

    ```bash
    service ambari-agent restart
    ```

1. 다시 시작이 작동하지 않으면 ambari 에이전트 프로세스를 죽인 다음 시작하십시오.

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>시나리오: Ambari 에이전트가 시작되지 않음

### <a name="issue"></a>문제

Ambari 에이전트는 일부 노드의 경우 Ambari 에이전트 하트비트가 손실된다는 경고를 Ambari UI에서 알리는 것을 시작하지 않았습니다.

### <a name="cause"></a>원인

경고는 Ambari 에이전트가 실행되지 않아 발생합니다.

### <a name="resolution"></a>해결 방법

1. 암바리 에이전트의 상태 확인:

    ```bash
    service ambari-agent status
    ```

1. 장애 조치 컨트롤러 서비스가 실행 중인지 확인합니다.

    ```bash
    ps -ef | grep failover
    ```

    장애 조치 컨트롤러 서비스가 실행되지 않는 경우 문제로 인해 hdinsight-agent가 장애 조치 컨트롤러를 시작하지 못하게 될 수 있습니다. 파일에서 `/var/log/hdinsight-agent/hdinsight-agent.out` hdinsight-에이전트 로그를 확인합니다.

## <a name="scenario-heartbeat-lost-for-ambari"></a>시나리오: 암바리에게 잃어버린 하트비트

### <a name="issue"></a>문제

암바리 하트비트 에이전트가 분실되었습니다.

### <a name="cause"></a>원인

OMS 로그는 높은 CPU 사용률을 유발하고 있습니다.

### <a name="resolution"></a>해결 방법

* [비활성화-AzHDInsight모니터링](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) 파워쉘 cmdlet을 사용하여 Azure 모니터 로깅을 사용하지 않도록 설정합니다.
* `mdsd.warn` 로그 파일 삭제

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

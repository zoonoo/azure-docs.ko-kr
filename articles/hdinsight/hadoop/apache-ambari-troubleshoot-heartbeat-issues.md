---
title: Azure HDInsight의 Apache Ambari 하트비트 문제
description: Azure HDInsight의 Apache Ambari 하트비트 문제에 대한 다양한 이유 검토
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 348a4d801590ba8f0e10f3c7f72ce6ead0a16022
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300044"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari 하트비트 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="scenario-high-cpu-utilization"></a>시나리오: 높은 CPU 사용률

### <a name="issue"></a>문제

Ambari 에이전트의 CPU 사용률이 높으며 이로 인해 Ambari UI에서 일부 노드에 대해 Ambari 에이전트 하트비트가 손실되었다는 경고가 발생합니다. 일반적으로 하트비트 손실 경고는 일시적입니다.

### <a name="cause"></a>원인

드문 경우지만 다양한 ambari-agent 버그로 인해 ambari-agent의 CPU 사용률이 높을 수 있습니다(100에 가까움).

### <a name="resolution"></a>해결 방법

1. ambari-agent의 pid(프로세스 ID)를 식별합니다.

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 그 후 다음 명령을 실행하여 CPU 사용률을 표시합니다.

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 문제를 완화하기 위해 ambari-agent를 다시 시작합니다.

    ```bash
    service ambari-agent restart
    ```

1. 다시 시작해도 해결되지 않으면 ambari-agent 프로세스를 종료했다가 시작합니다.

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>시나리오: Ambari 에이전트가 시작되지 않음

### <a name="issue"></a>문제

Ambari 에이전트가 시작되지 않았으므로 Ambari UI에서 일부 노드에 대해 Ambari 에이전트 하트비트가 손실되었다는 경고가 발생합니다.

### <a name="cause"></a>원인

Ambari 에이전트가 실행되고 있지 않기 때문에 경고가 발생합니다.

### <a name="resolution"></a>해결 방법

1. ambari-agent의 상태를 확인합니다.

    ```bash
    service ambari-agent status
    ```

1. 장애 조치(failover) 컨트롤러 서비스가 실행 중인지 확인합니다.

    ```bash
    ps -ef | grep failover
    ```

    장애 조치 컨트롤러 서비스가 실행되고 있지 않으면 문제가 있어서 hdinsight-agent가 장애 조치 컨트롤러를 시작하지 못하기 때문일 수 있습니다. `/var/log/hdinsight-agent/hdinsight-agent.out` 파일에서 hdinsight-agent 로그를 확인합니다.

## <a name="scenario-heartbeat-lost-for-ambari"></a>시나리오: Ambari에 대한 하트비트 손실

### <a name="issue"></a>문제

Ambari 하트비트 에이전트가 손실되었습니다.

### <a name="cause"></a>원인

OMS 로그로 인해 CPU 사용률이 높아집니다.

### <a name="resolution"></a>해결 방법

* [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell cmdlet을 사용하여 Azure Monitor 로깅을 사용하지 않도록 설정합니다.
* `mdsd.warn` 로그 파일을 삭제합니다.

---

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
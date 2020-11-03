---
title: Azure HDInsight의 Apache Ambari 하트비트 문제
description: Azure HDInsight에서 Apache Ambari 하트 비트 문제에 대 한 다양 한 이유 검토
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: adc5dfcef8cce269b6b6d982178433b8ee163f92
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285442"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari 하트비트 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="scenario-high-cpu-utilization"></a>시나리오: 높은 CPU 사용률

### <a name="issue"></a>문제

Ambari 에이전트의 CPU 사용률이 높아,이로 인해 일부 노드에 대해 Ambari agent 하트 비트가 손실 된 Ambari UI의 경고가 발생 합니다. 하트 비트 손실 경고는 일반적으로 일시적입니다.

### <a name="cause"></a>원인

드문 경우 지만 다양 한 ambari-에이전트 버그로 인해 ambari (100)의 CPU 사용률이 높을 수 있습니다.

### <a name="resolution"></a>해결 방법

1. Ambari-에이전트의 pid (프로세스 ID)를 식별 합니다.

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 그 후 다음 명령을 실행하여 CPU 사용률을 표시합니다.

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 문제를 완화 하기 위해 ambari-에이전트를 다시 시작 합니다.

    ```bash
    service ambari-agent restart
    ```

1. 다시 시작이 작동 하지 않으면 ambari 프로세스를 중지 하 고 시작 합니다.

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>시나리오: Ambari 에이전트가 시작 되지 않음

### <a name="issue"></a>문제

Ambari 에이전트가 시작 되지 않았기 때문에 Ambari UI에서 Ambari 에이전트 하트 비트가 손실 된 일부 노드에 대 한 경고가 발생 했습니다.

### <a name="cause"></a>원인

Ambari 에이전트가 실행 되 고 있지 않기 때문에 경고가 발생 합니다.

### <a name="resolution"></a>해결 방법

1. Ambari: 에이전트의 상태를 확인 합니다.

    ```bash
    service ambari-agent status
    ```

1. 장애 조치 (failover) 컨트롤러 서비스가 실행 중인지 확인 합니다.

    ```bash
    ps -ef | grep failover
    ```

    장애 조치 (failover) 컨트롤러 서비스가 실행 되지 않는 경우 hdinsight 에이전트가 장애 조치 (failover) 컨트롤러를 시작 하지 못하도록 하는 문제가 원인일 수 있습니다. 파일에서 hdinsight-에이전트 로그를 확인 `/var/log/hdinsight-agent/hdinsight-agent.out` 합니다.

## <a name="scenario-heartbeat-lost-for-ambari"></a>시나리오: Ambari에 대 한 하트 비트 손실

### <a name="issue"></a>문제

Ambari 하트 비트 에이전트가 손실 되었습니다.

### <a name="cause"></a>원인

OMS 로그의 CPU 사용률이 높습니다.

### <a name="resolution"></a>해결 방법

* [AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell cmdlet을 사용 하 여 Azure Monitor 로깅을 사용 하지 않도록 설정 합니다.
* `mdsd.warn`로그 파일 삭제

---

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
---
title: Azure HDInsight의 Apache Ambari UI 502 오류
description: Azure HDInsight 클러스터에 액세스하려고 하면 Apache Ambari UI 502 오류가 발생합니다
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: da396873fe91777130c407c2b679b0192141cae1
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291424"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari UI 502 오류

이 문서에서는 Azure HDInsight 클러스터와 상호작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터의 Apache Ambari UI에 액세스하려고 하면 "502 - 웹 서버에서 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 받았습니다."와 비슷한 메시지가 표시됩니다.

## <a name="cause"></a>원인

일반적으로 HTTP 502 상태 코드는 활성 헤드 노드에서 Ambari 서버가 제대로 실행되고 있지 않음을 의미합니다. 몇 가지 가능한 원인은 다음과 같습니다.

## <a name="resolution"></a>해결 방법

대부분의 경우 문제를 완화하기 위해 활성 헤드 노드를 다시 시작할 수 있습니다. 또는 헤드 노드에 대해 더 큰 VM 크기를 선택합니다.

### <a name="ambari-server-failed-to-start"></a>Ambari 서버 시작 실패

Ambari-서버 로그를 확인하여 Ambari 서버를 시작하지 못한 이유를 확인할 수 있습니다. 한 가지 일반적인 이유는 데이터베이스 일관성 확인 오류입니다. `/var/log/ambari-server/ambari-server-check-database.log` 로그 파일에서 찾을 수 있습니다.

클러스터 노드를 수정한 경우, 해당 노드를 실행 취소합니다. Hadoop/Spark 관련 구성을 수정할 때 항상 Ambari UI를 사용합니다.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 서버의 CPU 사용률 100% 차지

드문 경우지만 ambari-서버 프로세스에 100%의 CPU 사용률이 지속적으로 발생하는 것을 볼 수 있습니다. 완화를 위해 활성 헤드 노드로 ssh를 실행하고 Ambari 서버 프로세스를 중지하고 다시 시작할 수 있습니다.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>oom-killer으로 종료된 Ambari 서버

일부 시나리오에서는 헤드 노드의 메모리가 부족하여 Linux oom-killer가 프로세스를 중단하기 시작합니다. 찾을 수 없어야 하는 AmbariServer 프로세스 ID를 검색하여 이 상황을 확인할 수 있습니다. 그런 다음 `/var/log/syslog`를 확인하고 다음과 같은 항목을 찾습니다.

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

그런 다음, 메모리가 필요한 프로세스를 식별하고 근본 원인을 찾아보세요.

### <a name="other-issues-with-ambari-server"></a>Ambari 서버의 기타 문제

Ambari 서버가 들어오는 요청을 처리할 수 없는 경우는 거의 없으며, ambari-server 로그에서 오류를 확인하여 자세한 정보를 찾을 수 있습니다. 이러한 경우 중 하나는 다음과 같은 오류입니다.

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
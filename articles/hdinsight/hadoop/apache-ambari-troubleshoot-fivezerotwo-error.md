---
title: 아파치 암바리 UI 502 Azure HDInsight에서 오류
description: Azure HDInsight 클러스터에 액세스하려고 할 때 아파치 암바리 UI 502 오류
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895752"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 아파치 암바리 UI 502 오류

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터의 아파치 암바리 UI에 액세스하려고 하면 "502 - 웹 서버가 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 받았다"와 유사한 메시지가 나타납니다.

## <a name="cause"></a>원인

일반적으로 HTTP 502 상태 코드는 Ambari 서버가 활성 헤드노드에서 올바르게 실행되지 않음을 의미합니다. 몇 가지 가능한 근본 원인이 있습니다.

## <a name="resolution"></a>해결 방법

대부분의 경우 문제를 완화하기 위해 활성 헤드노드를 다시 시작할 수 있습니다. 또는 헤드노드에 더 큰 VM 크기를 선택하십시오.

### <a name="ambari-server-failed-to-start"></a>Ambari 서버를 시작하지 못했습니다.

ambari 서버 로그를 확인하여 Ambari 서버가 시작되지 못한 이유를 확인할 수 있습니다. 한 가지 일반적인 이유는 데이터베이스 일관성 검사 오류입니다. 이 로그 파일에서 이 `/var/log/ambari-server/ambari-server-check-database.log`것을 찾을 수 있습니다.

클러스터 노드를 수정한 경우 클러스터 노드를 취소하십시오. 항상 Ambari UI를 사용하여 Hadoop/Spark 관련 구성을 수정합니다.

### <a name="ambari-server-taking-100-cpu-utilization"></a>100% CPU 사용률을 취하는 암바리 서버

드문 경우지만 ambari 서버 프로세스가 지속적으로 CPU 사용률이 100%에 가깝습니다. 완화를 위해 활성 헤드노드로 이동하여 Ambari 서버 프로세스를 죽이고 다시 시작할 수 있습니다.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>옴 킬러에 의해 살해 암바리 서버

일부 시나리오에서는 헤드 노드메모리가 부족하고 Linux oom-killer가 죽일 프로세스를 선택하기 시작합니다. 찾을 수 없는 AmbariServer 프로세스 ID를 검색하여 이 상황을 확인할 수 있습니다. 그런 다음 `/var/log/syslog`을 보고 다음과 같은 것을 찾습니다.

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

그런 다음 기억을 복용하는 프로세스를 식별하고 추가 근본 원인을 시도합니다.

### <a name="other-issues-with-ambari-server"></a>암바리 서버의 다른 문제

Ambari 서버가 들어오는 요청을 처리할 수 없는 경우는 거의 없으며, 오류에 대한 ambari 서버 로그를 확인하여 더 많은 정보를 찾을 수 있습니다. 이러한 경우 중 하나는 다음과 같은 오류입니다.

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

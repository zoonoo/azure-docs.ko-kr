---
title: Azure HDInsight의 Apache Ambari UI 502 오류
description: Azure HDInsight 클러스터에 액세스 하려고 하면 Apache Ambari UI 502 오류가 발생 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895752"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari UI 502 오류

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

HDInsight 클러스터의 Apache Ambari UI에 액세스 하려고 하면 "502-웹 서버에서 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못 된 응답을 받았습니다."와 비슷한 메시지가 표시 됩니다.

## <a name="cause"></a>원인

일반적으로 HTTP 502 상태 코드는 활성 헤드 노드에서 Ambari 서버가 제대로 실행 되 고 있지 않음을 의미 합니다. 가능한 근본 원인에는 몇 가지가 있습니다.

## <a name="resolution"></a>해결 방법

대부분의 경우 문제를 완화 하기 위해 활성 헤드 노드를 다시 시작할 수 있습니다. 또는 헤드 노드에 대해 더 큰 VM 크기를 선택 합니다.

### <a name="ambari-server-failed-to-start"></a>Ambari 서버를 시작 하지 못함

Ambari-서버 로그를 확인 하 여 Ambari 서버를 시작 하지 못한 이유를 확인할 수 있습니다. 한 가지 일반적인 이유는 데이터베이스 일관성 검사 오류입니다. 이 로그 파일에서 찾을 수 있습니다 `/var/log/ambari-server/ambari-server-check-database.log` .

클러스터 노드를 수정한 경우 해당 노드를 취소 하십시오. 항상 Ambari UI를 사용 하 여 Hadoop/Spark 관련 구성을 수정 합니다.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 서버에서 CPU 사용률이 100%를 차지 합니다.

드문 경우 지만 ambari-서버 프로세스에 100%의 CPU 사용률이 지속적으로 발생 하는 것을 볼 수 있습니다. 완화를 위해 활성 헤드 노드로 ssh를 실행 하 고 Ambari 서버 프로세스를 중지 하 고 다시 시작할 수 있습니다.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Oom으로 종료 된 Ambari 서버-킬러

일부 시나리오에서는 헤드 노드의 메모리가 부족 하 고 Linux oom-킬러은 프로세스를 중단 하도록 선택 하기 시작 합니다. AmbariServer 프로세스 ID (찾을 수 없음)를 검색 하 여이 상황을 확인할 수 있습니다. 그런 다음를 확인 `/var/log/syslog` 하 고 다음과 같은 항목을 찾습니다.

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

그런 다음, 메모리를 많이 차지 하는 프로세스를 식별 하 고 근본 원인을 추가로 시도 합니다.

### <a name="other-issues-with-ambari-server"></a>Ambari server의 기타 문제

Ambari 서버에서 들어오는 요청을 처리할 수 없는 경우는 거의 없습니다. Ambari 로그에서 오류를 확인 하 여 자세한 정보를 찾을 수 있습니다. 이러한 경우에는 다음과 같은 오류가 발생 합니다.

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.

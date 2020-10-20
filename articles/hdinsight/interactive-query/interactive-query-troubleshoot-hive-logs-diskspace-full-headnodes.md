---
title: 디스크 공간을 채우는 Apache Hive 로그-Azure HDInsight
description: Apache Hive 로그는 Azure HDInsight에서 헤드 노드의 디스크 공간을 채우고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: a102c9f375b37579cf6f92b08d67f762d3dfd26a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220893"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>시나리오: Apache Hive 로그가 Azure HDInsight에서 헤드 노드의 디스크 공간을 채우고 있습니다.

이 문서에서는 Azure HDInsight 클러스터의 헤드 노드에 디스크 공간이 부족 하 여 발생 하는 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive/LLAP 클러스터에서 원치 않는 로그는 헤드 노드에서 전체 디스크 공간을 차지 하 고 있습니다. 이로 인해 다음 문제가 나타날 수 있습니다.

1. 헤드 노드에 남아 있는 공간이 없으므로 SSH 액세스가 실패 합니다.
2. Ambari는 *HTTP 오류를 제공 합니다. 503 서비스를 사용할 수 없습니다*.
3. HiveServer2 Interactive를 다시 시작 하지 못했습니다.

`ambari-agent`문제가 발생 하면 로그에 다음이 표시 됩니다.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>원인

고급 hive log4j 구성에서는 마지막으로 수정한 날짜를 기준으로 30 일 이전 파일에 대해 현재 기본 삭제 일정이 설정 됩니다.

## <a name="resolution"></a>해결 방법

1. Ambari 포털에서 Hive 구성 요소 요약으로 이동 하 고 탭을 클릭 `Configs` 합니다.

2. `Advanced hive-log4j`고급 설정 내의 섹션으로 이동 합니다.

3. `appender.RFA.strategy.action.condition.age`매개 변수를 원하는 기간으로 설정 합니다. 14 일에 대 한 예제: `appender.RFA.strategy.action.condition.age = 14D`

4. 관련 설정이 표시 되지 않으면 다음 설정을 추가 하세요.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. `hive.root.logger` `INFO,RFA` 다음과 같이로 설정 합니다. 기본 설정은 디버그로, 로그가 매우 커질 수 있습니다.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Configs를 저장 하 고 필수 구성 요소를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.

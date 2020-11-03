---
title: '문제 해결: 디스크 공간을 채우는 Apache Hive 로그-Azure HDInsight'
description: 이 문서에서는 Apache Hive 로그가 Azure HDInsight에서 헤드 노드의 디스크 공간을 가득 찰 때 따라야 하는 문제 해결 단계를 제공 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 107ec012bf2ff76ee1cbe4c5f8252566a5a16127
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288935"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>시나리오: Apache Hive 로그가 Azure HDInsight에서 헤드 노드의 디스크 공간을 채우고 있습니다.

이 문서에서는 Azure HDInsight 클러스터의 헤드 노드에서 디스크 공간이 부족 하 여 발생 하는 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive/LLAP 클러스터에서 원치 않는 로그는 헤드 노드에서 전체 디스크 공간을 차지 하 고 있습니다. 이로 인해 다음과 같은 문제가 발생할 수 있습니다.

- 헤드 노드에 남아 있는 공간이 없으므로 SSH 액세스가 실패 합니다.
- Ambari는 *HTTP 오류를 throw 합니다. 503 서비스를 사용할 수 없습니다*.
- HiveServer2 Interactive를 다시 시작 하지 못했습니다.

`ambari-agent`문제가 발생 하는 경우 로그에는 다음 항목이 포함 됩니다.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>원인

고급 Hive log4j 구성에서 현재 기본 삭제 일정은 마지막으로 수정한 날짜를 기준으로 30 일 보다 오래 된 파일을 삭제 하는 것입니다.

## <a name="resolution"></a>해결 방법

1. Ambari 포털의 Hive 구성 요소 요약으로 이동 하 여 **Configs** 탭을 선택 합니다.

2. `Advanced hive-log4j` **고급 설정** 의 섹션으로 이동 합니다.

3. `appender.RFA.strategy.action.condition.age`매개 변수를 원하는 기간으로 설정 합니다. 이 예에서는 age를 14 일로 설정 합니다. `appender.RFA.strategy.action.condition.age = 14D`

4. 관련 설정이 표시 되지 않으면 다음 설정을 추가 합니다.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. `hive.root.logger` `INFO,RFA` 다음 예제와 같이를로 설정 합니다. 기본 설정은 `DEBUG` 로, 로그를 크게 만듭니다.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 구성을 저장 하 고 필수 구성 요소를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]

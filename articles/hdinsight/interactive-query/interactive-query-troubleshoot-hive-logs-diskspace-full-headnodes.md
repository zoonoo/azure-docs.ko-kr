---
title: Hive 로그가 Azure HDInsight의 디스크 공간을 가득 채우는 문제 해결
description: 이 문서에서는 Apache Hive 로그가 Azure HDInsight에서 헤드 노드의 디스크 공간을 가득 채울 때 따라야 하는 문제 해결 단계를 제공합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: kcheeeung
ms.author: kecheung
ms.date: 05/21/2021
ms.openlocfilehash: d4fb0c4a197f28321e4328d17f00173f45b28d1b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290740"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>시나리오: Apache Hive 로그가 Azure HDInsight에서 헤드 노드의 디스크 공간을 가득 채우고 있습니다.

이 문서에서는 Azure HDInsight 클러스터의 헤드 노드에서 디스크 공간 부족과 관련하여 발생하는 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDI 4.0 Apache Hive/LLAP 클러스터에서 원치 않는 로그가 헤드 노드의 전체 디스크 공간을 차지하고 있습니다. 이로 인해 다음과 같은 문제가 발생할 수 있습니다.

- 헤드 노드에 남아 있는 공간이 없으므로 SSH 액세스가 실패합니다.
- HiveServer2 Interactive를 다시 시작하지 못합니다.

## <a name="cause"></a>원인

자동 Hive 로그 삭제가 고급 hive-log4j2 구성에서 구성되지 않았습니다. 기본 크기 제한인 60GB는 고객의 사용 패턴에 비해 너무 많은 공간을 사용합니다.

## <a name="resolution"></a>해결 방법

1. Ambari 포털의 Hive 구성 요소 요약으로 이동하여 **Configs**(구성) 탭을 선택합니다.

2. `Advanced hive-log4j2`Advanced settings **(고급 설정)의**  섹션으로 이동합니다.

3. 해당 설정이 있는지 확인합니다. 관련 설정이 보이지 않으면 다음 설정을 추가합니다.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfFileName
    appender.RFA.strategy.action.condition.regex = hive*.*log.*
    appender.RFA.strategy.action.condition.nested_condition.type = IfAny
    # Deletes logs based on total accumulated size, keeping the most recent
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

4. 다음을 기반으로 삭제하는 세 가지 기본 옵션을 살펴보겠습니다.
- **전체 크기**
    - `appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds`를 원하는 크기 제한으로 변경합니다.

- **날짜**
    - 주석 처리를 제거하고 조건을 전환할 수도 있습니다. 그런 다음, `appender.RFA.strategy.action.condition.nested_condition.lastMod.age`를 원하는 기간으로 변경합니다.

    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

- **총 크기와 날짜의 조합**
    - 아래와 같이 주석 처리를 제거하여 두 옵션을 결합할 수 있습니다. 그러면 log4j2는 다음과 같이 동작합니다. 두 조건 중 하나가 충족되면 로그 삭제를 시작합니다.
    
    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```
5. 구성을 저장하고 필수 구성 요소를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]

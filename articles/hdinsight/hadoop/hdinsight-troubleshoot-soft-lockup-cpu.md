---
title: Azure HDInsight 클러스터의 Watchdog 버그 소프트 작동 중지 CPU 오류
description: Watchdog 버그 소프트 작동 중지 CPU가 Azure HDInsight 클러스터의 커널 syslog에 표시됨
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944348"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터의 "watchdog: 버그: 소프트 작동 중지 - CPU" 오류

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

커널 syslog에 `watchdog: BUG: soft lockup - CPU` 오류 메시지가 포함되어 있습니다.

## <a name="cause"></a>원인

Linux 커널의 [버그](https://bugzilla.kernel.org/show_bug.cgi?id=199437)로 인해 CPU 소프트 작동 중지가 발생합니다.

## <a name="resolution"></a>해결 방법

커널 패치를 적용합니다. 아래 스크립트는 Linux 커널을 업그레이드하고 24시간 동안 서로 다른 시간에 컴퓨터를 다시 부팅합니다. 두 개의 일괄 처리로 스크립트 작업을 실행합니다. 첫 번째 일괄 처리는 헤드 노드를 제외한 모든 노드에 있습니다. 두 번째 일괄 처리는 헤드 노드에 있습니다. 헤드 노드 및 다른 노드에서 동시에 실행하지 마세요.

1. Azure Portal에서 HDInsight 클러스터로 이동합니다.

1. 스크립트 작업으로 이동합니다.

1. **새로운 항목 제출** 을 선택한 후 다음과 같이 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | 스크립트 유형 | -사용자 지정 |
    | 이름 |커널 소프트 잠금 문제에 대한 수정 |
    | Bash 스크립트 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 노드 유형 |작업자, Zookeeper |
    | 매개 변수 |해당 없음 |

    새 노드가 추가될 때 스크립트를 실행하려면 **이 스크립트 작업 유지...** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 실행이 성공할 때까지 기다립니다.

1. 3단계와 동일한 단계를 수행하되 이번에는 유형이 헤드인 노드를 사용하여 헤드 노드에서 스크립트 작업을 실행합니다.

1. 실행이 성공할 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
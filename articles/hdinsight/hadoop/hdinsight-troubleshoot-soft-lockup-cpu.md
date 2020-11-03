---
title: Watchdog 버그 소프트 작동 중지 Azure HDInsight 클러스터에서 CPU 오류 발생
description: Watchdog 버그 소프트 작동 중지 CPU가 Azure HDInsight 클러스터에서 커널 syslog 표시 됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 33990e40507f088962fd8d8efd241de5d2fcc63b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289072"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터의 "watchdog: 버그: 소프트 잠금-CPU" 오류

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

커널 syslog에는 오류 메시지가 포함 되어 `watchdog: BUG: soft lockup - CPU` 있습니다.

## <a name="cause"></a>원인

Linux 커널의 [버그로](https://bugzilla.kernel.org/show_bug.cgi?id=199437) 인해 CPU 일시 중지가 발생 합니다.

## <a name="resolution"></a>해결 방법

커널 패치를 적용 합니다. 아래 스크립트는 linux 커널을 업그레이드 하 고 24 시간 동안 서로 다른 시간에 컴퓨터를 다시 부팅 합니다. 두 개의 일괄 처리로 스크립트 작업을 실행 합니다. 첫 번째 일괄 처리는 헤드 노드를 제외한 모든 노드에 있습니다. 두 번째 일괄 처리는 헤드 노드에 있습니다. 헤드 노드 및 다른 노드에서 동시에 실행 하지 마십시오.

1. Azure Portal에서 HDInsight 클러스터로 이동 합니다.

1. 스크립트 동작으로 이동 합니다.

1. **새 제출** 을 선택 하 고 다음과 같이 입력을 입력 합니다.

    | 속성 | 값 |
    | --- | --- |
    | 스크립트 유형 | -Custom |
    | 이름 |커널 소프트 잠금 문제에 대 한 수정 |
    | Bash 스크립트 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 노드 유형 |작업자, 사육 사 |
    | 매개 변수 |해당 없음 |

    새 노드가 추가 될 때 스크립트를 실행 하려면 **이 스크립트 동작 유지 ...** 를 선택 합니다.

1. **만들기** 를 선택합니다.

1. 실행이 성공할 때까지 기다립니다.

1. 3 단계와 동일한 단계를 수행 하 여 헤드 노드에서 스크립트 작업을 실행 하 되 이번에는 노드 유형: Head를 사용 합니다.

1. 실행이 성공할 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
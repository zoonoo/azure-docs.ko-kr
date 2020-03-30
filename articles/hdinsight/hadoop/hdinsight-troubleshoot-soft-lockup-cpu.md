---
title: Azure HDInsight 클러스터의 감시 버그 소프트 잠금 CPU 오류
description: 와치버그 버그 소프트 잠금 CPU는 Azure HDInsight 클러스터의 커널 syslogs에 나타납니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894115"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터의 "감시 타이머: 버그: 소프트 잠금 - CPU" 오류

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

커널 syslogs에는 오류 메시지가 `watchdog: BUG: soft lockup - CPU`포함되어 있습니다.

## <a name="cause"></a>원인

리눅스 커널의 [버그로](https://bugzilla.kernel.org/show_bug.cgi?id=199437) 인해 CPU 소프트 잠금이 발생합니다.

## <a name="resolution"></a>해결 방법

커널 패치를 적용합니다. 아래 스크립트는 Linux 커널을 업그레이드하고 24시간 동안 서로 다른 시간에 컴퓨터를 재부팅합니다. 스크립트 작업을 두 일괄 처리로 실행합니다. 첫 번째 일괄 처리는 헤드 노드를 제외한 모든 노드에 있습니다. 두 번째 일괄 처리는 헤드 노드에 있습니다. 헤드 노드및 다른 노드에서 동시에 실행되지 마십시오.

1. Azure 포털에서 HDInsight 클러스터로 이동합니다.

1. 스크립트 작업으로 이동합니다.

1. 새 제출을 선택하고 다음과 같이 입력을 **입력합니다.**

    | 속성 | 값 |
    | --- | --- |
    | 스크립트 유형 | - 사용자 정의 |
    | 이름 |커널 소프트 잠금 문제 수정 |
    | Bash 스크립트 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 노드 유형 |작업자, 사육사 |
    | 매개 변수 |해당 없음 |

    새 노드가 추가될 때 스크립트를 실행하려면 **이 스크립트 작업 유지를** 선택합니다.

1. **만들기**를 선택합니다.

1. 실행이 성공할 때까지 기다립니다.

1. 3단계와 동일한 단계를 수행하여 Head 노드에서 스크립트 작업을 실행하지만 이번에는 노드 유형인 Head를 실행합니다.

1. 실행이 성공할 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

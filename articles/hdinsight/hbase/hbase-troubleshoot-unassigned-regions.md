---
title: Azure HDInsight에서 지역 서버 문제
description: Azure HDInsight에서 지역 서버 문제
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79272761"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight에서 지역 서버 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="scenario-unassigned-regions"></a>시나리오: 할당 되지 않은 영역

### <a name="issue"></a>문제

명령을 실행 `hbase hbck` 하면 다음과 비슷한 오류 메시지가 표시 됩니다.

```
multiple regions being unassigned or holes in the chain of regions
```

Apache HBase Master UI에서 모든 지역 서버에서 불균형 한 지역 수를 확인할 수 있습니다. 그런 후 `hbase hbck` 명령을 실행하여 영역 체인의 빈 영역을 확인할 수 있습니다.

### <a name="cause"></a>원인

구멍은 오프 라인 영역으로 인해 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

할당을 수정 합니다. 할당되지 않은 지역을 정상 상태로 전환하려면 다음 단계를 수행합니다.

1. SSH를 사용 하 여 HDInsight HBase 클러스터에 로그인 합니다.

1. 명령을 `hbase zkcli` 실행 하 여 사육 사 셸에 연결 합니다.

1. 또는 `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` 명령을 실행 합니다.

1. 명령을 사용 하 `exit` 여 사육 사 셸을 종료 합니다.

1. Apache Ambari UI를 열고 활성 HBase Master 서비스를 다시 시작합니다.

1. 명령을 `hbase hbck` 다시 실행 합니다 (추가 옵션 없음). 출력을 확인 하 고 모든 지역이 할당 되었는지 확인 합니다.

---

## <a name="scenario-dead-region-servers"></a>시나리오: 비활성 지역 서버

### <a name="issue"></a>문제

영역 서버를 시작할 수 없습니다.

### <a name="cause"></a>원인

여러 개의 WAL 디렉터리 분할.

1. 현재 WALs `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`의 목록을 가져옵니다.

1. 파일을 `wals.out` 검사 합니다. 분할 디렉터리가 너무 많은 경우 (* 분할부터) 영역 서버는 이러한 디렉터리로 인해 실패할 수 있습니다.

### <a name="resolution"></a>해결 방법

1. Ambari 포털에서 HBase를 중지 합니다.

1. 새 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` wals 목록을 가져오려면를 실행 합니다.

1. *-디렉터리를 임시 폴더로 `splitWAL`이동 하 고 * 분할 디렉터리를 삭제 합니다.

1. 명령을 `hbase zkcli` 실행 하 여 사육 사 셸에 연결 합니다.

1. `rmr /hbase-unsecure/splitWAL`을 실행합니다.

1. HBase 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

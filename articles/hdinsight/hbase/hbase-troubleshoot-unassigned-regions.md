---
title: Azure HDInsight의 지역 서버 문제
description: Azure HDInsight의 지역 서버 문제
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272761"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight의 지역 서버 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="scenario-unassigned-regions"></a>시나리오: 할당되지 않은 지역

### <a name="issue"></a>문제

명령을 `hbase hbck` 실행할 때 다음과 유사한 오류 메시지가 표시됩니다.

```
multiple regions being unassigned or holes in the chain of regions
```

아파치 HBase 마스터 UI에서 모든 지역 서버에서 불균형한 지역 수를 확인할 수 있습니다. 그런 후 `hbase hbck` 명령을 실행하여 영역 체인의 빈 영역을 확인할 수 있습니다.

### <a name="cause"></a>원인

구멍은 오프라인 영역의 결과일 수 있습니다.

### <a name="resolution"></a>해결 방법

할당을 수정합니다. 할당되지 않은 지역을 정상 상태로 전환하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.

1. 명령을 `hbase zkcli` 실행하여 ZooKeeper 셸과 연결합니다.

1. 실행 `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` 또는 명령.

1. 명령을 사용하여 `exit` 사육사 쉘을 종료합니다.

1. Apache Ambari UI를 열고 활성 HBase Master 서비스를 다시 시작합니다.

1. 추가 `hbase hbck` 옵션 없이 명령을 다시 실행합니다. 출력을 확인하고 모든 영역이 할당되고 있는지 확인합니다.

---

## <a name="scenario-dead-region-servers"></a>시나리오: 데드 리전 서버

### <a name="issue"></a>문제

지역 서버가 시작되지 않습니다.

### <a name="cause"></a>원인

여러 분할 WAL 디렉터리.

1. 현재 월의 목록을 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`가져옵니다: .

1. 파일을 `wals.out` 검사합니다. *-분할부터 시작하는 분할 디렉터리가 너무 많으면 이러한 디렉터리로 인해 지역 서버가 실패할 수 있습니다.

### <a name="resolution"></a>해결 방법

1. 암바리 포털에서 HBase를 중지합니다.

1. 실행하여 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` WA의 최신 목록을 가져옵니다.

1. *분할 디렉터리를 임시 폴더로 `splitWAL`이동하고 *분할 디렉터리를 삭제합니다.

1. 명령을 `hbase zkcli` 실행하여 사육사 셸과 연결합니다.

1. `rmr /hbase-unsecure/splitWAL`을 실행합니다.

1. HBase 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

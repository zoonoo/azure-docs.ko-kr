---
title: Azure HDInsight를 사용한 HBase 문제 해결
description: HBase 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573951"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache HBase 문제 해결

Apache Ambari에서 Apache HBase 페이로드를 사용할 때의 주요 문제 및 해결 방법을 알아봅니다.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>할당되지 않은 여러 영역이 있는 hbck 명령 보고서를 실행하는 방법

`hbase hbck` 명령을 실행할 때 볼 수 있는 일반적인 오류 메시지는 “할당되지 않은 여러 영역이 있거나 영역 체인에 빈 영역이 있습니다.”입니다.

HBase Master UI에서 모든 영역 서버 중 부하가 분산되지 않은 영역 수를 확인할 수 있습니다. 그런 후 `hbase hbck` 명령을 실행하여 영역 체인의 빈 영역을 확인할 수 있습니다.

빈 영역은 오프라인 영역으로 인해 야기된 것일 수 있으므로 먼저 할당을 수정해야 합니다. 

할당되지 않은 영역을 정상 상태로 전환하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.
2. Apache ZooKeeper 셸에 연결하려면 `hbase zkcli` 명령을 실행합니다.
3. `rmr /hbase/regions-in-transition` 명령 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.
4. `hbase zkcli` 셸을 종료하려면 `exit` 명령을 사용합니다.
5. Apache Ambari UI를 열고 활성 HBase Master 서비스를 다시 시작합니다.
6. `hbase hbck` 명령을 다시 실행합니다(옵션 제외). 이 명령의 출력을 확인하고 모든 영역이 할당되었는지 확인합니다.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>영역 할당에 대해 hbck 명령을 사용하여 시간 제한 문제를 해결하는 방법

### <a name="issue"></a>문제점

`hbck` 명령을 사용할 때 시간 초과 문제가 발생하는 잠재적 원인은 오랜 시간 동안 여러 영역이 "전환" 상태에 있다는 것일 수 있습니다. HBase Master UI에서 이러한 영역이 오프라인으로 나타날 수 있습니다. 전환하려는 영역 수가 많기 때문에 HBase Master에서 시간이 초과되어 해당 영역을 온라인 상태로 전환할 수 없습니다.

### <a name="resolution-steps"></a>해결 단계:

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.
2. Apache ZooKeeper 셸에 연결하려면 `hbase zkcli` 명령을 실행합니다.
3. `rmr /hbase/regions-in-transition` 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.
4. `hbase zkcli` 셸을 종료하려면 `exit` 명령을 사용합니다.
5. Ambari UI에서 활성 HBase Master 서비스를 다시 시작합니다.
6. `hbase hbck -fixAssignments` 명령을 다시 실행합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

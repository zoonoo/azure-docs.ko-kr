---
title: Azure HDInsight에서 ' hbase hbck ' 명령을 사용 하 여 시간 제한
description: 지역 할당을 수정할 때 ' hbase hbck ' 명령에 대 한 시간 제한 문제
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737928"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 ' hbase hbck ' 명령을 사용 하 여 시간 제한

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

지역 할당을 `hbase hbck` 수정할 때 명령을 사용 하 여 시간 제한이 발생 합니다.

## <a name="cause"></a>원인

여기서 설명하는 잠재적 원인은 오랜 시간 동안 "전환 중" 상태에 있는 여러 지역 때문일 수 있습니다. 이러한 지역은 Apache HBase Master UI에서 오프 라인으로 볼 수 있습니다. 전환 하려는 영역 수가 많기 때문에 HBase Master 시간이 초과 되어 해당 지역을 온라인 상태로 되돌릴 수 없습니다.

## <a name="resolution"></a>해결 방법

1. SSH를 사용 하 여 HDInsight HBase 클러스터에 로그인 합니다.

1. 명령을 `hbase zkcli` 실행 하 여 사육 사 셸에 연결 합니다.

1. `rmr /hbase/regions-in-transition` 또는`rmr /hbase-unsecure/regions-in-transition` 명령을 실행 합니다.

1. 명령을 사용 하 `exit` 여 셸에서종료합니다.`hbase zkcli`

1. Ambari UI를 열고 Ambari에서 Active HBase Master 서비스를 다시 시작합니다.

1. 해당 섹션의 HBase Master UI "영역 전환"을 모니터링 하 여 아무 지역은 중단 되지 않도록 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

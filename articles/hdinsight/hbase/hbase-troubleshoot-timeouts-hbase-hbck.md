---
title: Azure HDInsight에서 'hbase hbck' 명령을 사용하여 시간 제한
description: 지역 할당을 수정할 때 'hbase hbck' 명령의 시간 시간 시간 지정 문제
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887192"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 'hbase hbck' 명령이 있는 시간 시간

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

지역 할당을 `hbase hbck` 수정할 때 명령으로 시간 시간을 발생합니다.

## <a name="cause"></a>원인

`hbck` 명령을 사용할 때 시간 초과 문제가 발생하는 잠재적 원인은 오랜 시간 동안 여러 영역이 "전환" 상태에 있다는 것일 수 있습니다. HBase Master UI에서 이러한 영역이 오프라인으로 나타날 수 있습니다. 많은 수의 지역이 전환을 시도하기 때문에 HBase Master는 시간 시간이 부족하여 해당 지역을 다시 온라인 상태가 되지 못할 수 있습니다.

## <a name="resolution"></a>해결 방법

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.

1. 아파치 사육사 쉘과 연결하는 명령을 실행합니다. `hbase zkcli`

1. 실행 `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` 또는 명령.

1. 명령을 `hbase zkcli` 사용하여 `exit` 셸에서 종료합니다.

1. 아파치 암바리 UI에서 활성 HBase 마스터 서비스를 다시 시작합니다.

1. `hbase hbck -fixAssignments` 명령을 실행합니다.

1. HBase 마스터 UI "전환 영역"을 모니터링하여 해당 섹션에 대해 어떤 영역이 막히지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

- 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

- 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

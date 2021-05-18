---
title: Azure HDInsight에서 'hbase hbck' 명령을 사용하여 시간 제한
description: 지역 할당을 수정하는 경우 'hbase hbck' 명령을 사용한 시간 초과 문제
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936698"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 'hbase hbck' 명령을 사용한 시간 제한

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

지역 할당을 수정할 때 `hbase hbck` 명령을 포함하는 시간 제한이 발생합니다.

## <a name="cause"></a>원인

`hbck` 명령을 사용할 때 시간 초과 문제가 발생하는 잠재적 원인은 오랜 시간 동안 여러 영역이 "전환" 상태에 있다는 것일 수 있습니다. HBase Master UI에서 이러한 영역이 오프라인으로 나타날 수 있습니다. 전환하려는 지역 수가 많기 때문에 HBase Master에서 시간이 초과되어 해당 지역을 온라인 상태로 전환할 수 없습니다.

## <a name="resolution"></a>해결 방법

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.

1. `hbase zkcli` 명령을 실행하여 Apache ZooKeeper 셸에 연결합니다.

1. `rmr /hbase/regions-in-transition` 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.

1. `exit` 명령을 사용하여 `hbase zkcli` 셸을 끝냅니다.

1. Apache Ambari UI에서 활성 HBase Master 서비스를 다시 시작합니다.

1. `hbase hbck -fixAssignments` 명령을 실행합니다.

1. 해당 섹션의 HBase Master UI "지역 전환"을 모니터링하여 아무 지역도 중단되지 않도록 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

- [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
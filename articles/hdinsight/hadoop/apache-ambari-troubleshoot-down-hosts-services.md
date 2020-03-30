---
title: 아파치 암바리 UI는 Azure HDInsight에서 호스트와 서비스를 보여줍니다
description: Azure HDInsight에서 호스트 및 서비스를 표시 할 때 아파치 암바리 UI 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895646"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>시나리오: 아파치 암바리 UI 는 Azure HDInsight에서 호스트와 서비스를 보여줍니다

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 Ambari UI에 액세스할 수 있지만 UI는 거의 모든 서비스가 다운 표시, 모든 호스트 는 하트 비트를 보여 손실.

## <a name="cause"></a>원인

대부분의 시나리오에서 이는 Ambari 서버가 활성 헤드노드에서 실행되지 않는 문제입니다. 어떤 헤드노드가 활성 헤드노드인지 확인하고 ambari-server가 올바른 헤드노드에서 실행되는지 확인합니다. 수동으로 ambari-서버를 시작하지 마십시오, 장애 조치 컨트롤러 서비스는 오른쪽 헤드 노드에서 ambari-서버를 시작하는 책임을 보자. 활성 헤드노드를 재부팅하여 장애 조치(failover)를 강제로 적용합니다.

네트워킹 문제로 인해 이 문제가 발생할 수도 있습니다. 각 클러스터 노드에서 ping할 `headnodehost`수 있는지 확인합니다. 클러스터 노드가 `headnodehost`다음에 연결할 수 없는 드문 상황이 있습니다.

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>해결 방법

일반적으로 활성 헤드노드를 재부팅하면 이 문제가 완화됩니다. 그렇지 않은 경우 HDInsight 지원 팀에 문의하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

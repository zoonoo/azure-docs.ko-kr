---
title: Apache Ambari UI는 Azure HDInsight에서 호스트 및 서비스를 표시 합니다.
description: Azure HDInsight에서 호스트 및 서비스를 표시 하는 경우 Apache Ambari UI 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 9c9676fa1457be014098b8190986c617262e628d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286497"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>시나리오: Apache Ambari UI는 Azure HDInsight에서 호스트 및 서비스를 표시 합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Ambari UI에 액세스할 수 있지만 UI는 거의 모든 서비스를 중지 하 고 하트 비트 손실을 보여 주는 모든 호스트를 표시 합니다.

## <a name="cause"></a>원인

대부분의 시나리오에서이는 활성 헤드 노드에서 실행 되 고 있지 않은 Ambari 서버에서 발생 하는 문제입니다. 활성 헤드 노드인 헤드 노드를 확인 하 고 ambari 서버가 오른쪽에서 실행 되는지 확인 합니다. Ambari를 수동으로 시작 하지 마세요. 장애 조치 (failover) 컨트롤러 서비스가 올바른 헤드 노드에서 ambari를 시작 하도록 합니다. 활성 헤드 노드를 다시 부팅 하 여 강제로 장애 조치 (failover)를 수행 합니다.

네트워킹 문제로이 문제가 발생할 수도 있습니다. 각 클러스터 노드에서 ping 할 수 있는지 확인 `headnodehost` 합니다. 클러스터 노드를 연결할 수 없는 드문 경우는 `headnodehost` 다음과 같습니다.

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>해결 방법

일반적으로 활성 헤드 노드를 다시 부팅 하면이 문제를 완화할 수 있습니다. 그렇지 않은 경우 HDInsight 지원 팀에 문의 하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
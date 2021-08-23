---
title: Apache Ambari UI가 Azure HDInsight에서 다운된 호스트와 서비스를 표시함
description: Apache Ambari UI가 Azure HDInsight에서 다운된 호스트와 서비스를 표시하는 경우 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: b8cd9c4e00eb3dc454098dbd127622342bc690f7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291460"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>시나리오: Apache Ambari UI가 Azure HDInsight에서 다운된 호스트와 서비스를 표시함

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 발생하는 문제의 문제 해결 단계와 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Ambari UI에 액세스할 수 있지만 UI에는 거의 모든 서비스가 다운된 것으로 표시되므로 모든 호스트는 하트비트가 손실된 것으로 표시합니다.

## <a name="cause"></a>원인

대부분의 시나리오에서 이는 활성 헤드 노드에서 실행되지 않는 Ambari 서버와 관련된 문제입니다. 활성 헤드 노드인 헤드 노드를 확인하고 Ambari 서버가 적합한 헤드 노드에서 실행되는지 확인합니다. Ambari 서버를 수동으로 시작하지 마세요. 장애 조치(failover) 컨트롤러 서비스가 적합한 헤드 노드에서 Ambari 서버를 시작하도록 합니다. 활성 헤드 노드를 다시 부팅하여 강제로 장애 조치합니다.

네트워킹 문제로 인해 이 문제가 발생할 수도 있습니다. 각 클러스터 노드에서 `headnodehost`을 ping할 수 있는지 확인합니다. 드물지만 클러스터 노드가 `headnodehost`에 연결할 수 없는 경우가 있습니다.

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>해결 방법

일반적으로 활성 헤드 노드를 다시 부팅하면 이 문제가 완화됩니다. 문제가 해결되지 않으면 HDInsight 지원 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
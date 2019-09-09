---
title: Azure HDInsight에서 Apache Hive LLAP 쿼리 성능 저하
description: Apache Hive LLAP의 쿼리가 Azure HDInsight에서 예상 보다 느리게 실행 되 고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: d05febbe6ba9e30ef46f5e961af155d37c76c205
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811369"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive LLAP 쿼리 성능 저하

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

기본 클러스터 구성은 워크 로드에 맞게 충분히 조정 되지 않습니다. Hive LLAP의 쿼리가 예상 보다 느리게 실행 되 고 있습니다.

## <a name="cause"></a>원인

이 문제는 다양 한 이유로 인해 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

LLAP은 조인과 집계가 관련 된 쿼리에 최적화 되어 있습니다. 다음과 같은 쿼리는 대화형 Hive 클러스터에서 제대로 작동 하지 않습니다.

```
select * from table where column = "columnvalue"
```

Hive LLAP에서 point 쿼리 성능을 향상 시키려면 다음 구성을 설정 합니다.

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

또한 다음과 같은 구성 변경으로 사용 하 여 성능을 향상 시킬 수 있습니다.

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

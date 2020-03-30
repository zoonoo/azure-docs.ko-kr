---
title: Azure HDInsight의 아파치 하이브 LLAP 쿼리에서 성능 저하
description: 아파치 하이브 LLAP의 쿼리는 Azure HDInsight에서 예상보다 느리게 실행되고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895137"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 아파치 하이브 LLAP 쿼리에서 성능 저하

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

기본 클러스터 구성이 워크로드에 맞게 충분히 조정되지 않았습니다. Hive LLAP의 쿼리가 예상보다 느리게 실행되고 있습니다.

## <a name="cause"></a>원인

이것은 다양한 이유로 인해 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

LLAP는 조인 및 집계를 포함하는 쿼리에 최적화되어 있습니다. 다음과 같은 쿼리는 대화형 하이브 클러스터에서 잘 수행되지 않습니다.

```
select * from table where column = "columnvalue"
```

Hive LLAP에서 포인트 쿼리 성능을 향상하려면 다음 구성을 설정합니다.

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

다음 구성 변경을 통해 성능을 향상시키기 위해 LLAP 캐시 사용량을 늘릴 수도 있습니다.

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

---
title: Azure HDInsight에서 Apache Hive LLAP 쿼리의 성능 저하
description: Apache Hive LLAP의 쿼리가 Azure HDInsight에서 예상보다 느리게 실행되고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 80fe51c2131da5a6fcb69eb5526b5d4f33a45e03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930893"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive LLAP 쿼리의 성능 저하

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

기본 클러스터 구성이 워크로드에 맞게 충분히 조정되지 않습니다. Hive LLAP의 쿼리가 예상보다 느리게 실행되고 있습니다.

## <a name="cause"></a>원인

이 문제는 다양한 이유로 인해 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

LLAP는 조인과 집계가 관련된 쿼리에 최적화되어 있습니다. 다음과 같은 쿼리는 Interactive Hive 클러스터에서 제대로 작동하지 않습니다.

```
select * from table where column = "columnvalue"
```

Hive LLAP에서 지점 쿼리 성능을 개선하려면 다음 구성을 설정합니다.

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

또한 LLAP 캐시 사용량을 늘려서 다음 구성 변경으로 성능을 개선할 수도 있습니다.

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
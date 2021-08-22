---
title: Azure HDInsight에서 Apache Hive LLAP 쿼리의 성능 저하
description: Apache Hive LLAP의 쿼리가 Azure HDInsight에서 예상보다 느리게 실행되고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: e21b49d26e8b7570b7f70e30f04b44fd23341f03
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290614"
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

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
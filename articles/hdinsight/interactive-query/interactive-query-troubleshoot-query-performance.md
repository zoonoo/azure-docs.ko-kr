---
title: Azure HDInsight에서 Apache Hive LLAP 쿼리 성능 저하
description: Apache Hive LLAP의 쿼리가 Azure HDInsight에서 예상 보다 느리게 실행 되 고 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 80fe51c2131da5a6fcb69eb5526b5d4f33a45e03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930893"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive LLAP 쿼리 성능 저하

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
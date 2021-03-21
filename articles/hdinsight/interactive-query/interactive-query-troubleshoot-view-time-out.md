---
title: 쿼리 결과에서 Apache Hive 뷰 시간 초과-Azure HDInsight
description: Azure HDInsight에서 쿼리 결과를 인출 하는 동안 Apache Hive 보기 시간 초과
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930738"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 쿼리 결과를 인출 하는 동안 Apache Hive 뷰 시간 초과

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive 뷰에서 특정 쿼리를 실행 하는 경우 다음과 같은 오류가 발생할 수 있습니다.

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>원인

Hive 뷰 기본 시간 제한 값은 실행 중인 쿼리에 적합 하지 않을 수 있습니다. 지정 된 기간이 너무 짧아서 Hive 뷰에서 쿼리 결과를 가져올 수 없습니다.

## <a name="resolution"></a>해결 방법

에서 다음 속성을 설정 하 여 Apache Ambari Hive 뷰 시간 제한을 늘립니다 `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

값은 `HIVE_VIEW_INSTANCE_NAME` Hive 뷰 URL의 끝에 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
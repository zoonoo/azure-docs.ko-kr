---
title: 쿼리 결과에서 Apache Hive 보기 시간이 초과됨 - Azure HDInsight
description: 쿼리 결과를 가져올 때 Apache Hive 보기 시간이 초과됨 - Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930738"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>시나리오: 쿼리 결과를 가져올 때 Apache Hive 보기 시간이 초과됨 - Azure HDInsight

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Hive 보기에서 특정 쿼리를 실행하는 경우 다음 오류가 발생할 수 있습니다.

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>원인

Hive 보기 기본 시간 초과 값이 실행 중인 쿼리에 적합하지 않을 수 있습니다. 지정된 기간이 너무 짧아서 Hive 보기에서 쿼리 결과를 가져올 수 없습니다.

## <a name="resolution"></a>해결 방법

`/etc/ambari-server/conf/ambari.properties`에서 다음 속성을 설정하여 Apache Ambari Hive 보기 시간 초과를 늘립니다.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

`HIVE_VIEW_INSTANCE_NAME` 값은 Hive 보기 URL의 끝에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
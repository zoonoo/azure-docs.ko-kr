---
title: Apache Hive Zeppelin 인터프리터 오류-Azure HDInsight
description: Apache Zeppelin Hive JDBC 인터프리터는 Azure HDInsight에서 잘못 된 URL을 가리킵니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930747"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>시나리오: Apache Hive Zeppelin 인터프리터는 Azure HDInsight에서 사육 사 오류를 제공 합니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive LLAP 클러스터에서 쿼리 실행을 시도할 때 Zeppelin 인터프리터는 다음과 같은 오류 메시지를 제공 합니다.

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>원인

Zeppelin Hive JDBC 인터프리터가 잘못 된 URL을 가리킵니다.

## <a name="resolution"></a>해결 방법

1. Hive 구성 요소 요약으로 이동 하 여 "Hive JDBC Url"을 클립보드에 복사 합니다.

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`로 이동

1. JDBC 설정 편집: 1 단계에서 복사한 Hive JDBC URL의 hive url 값을 업데이트 합니다.

1. 저장 한 다음 쿼리를 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
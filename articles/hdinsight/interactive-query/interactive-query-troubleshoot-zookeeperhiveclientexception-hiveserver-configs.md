---
title: Apache Hive Zeppelin 인터프리터 오류 - Azure HDInsight
description: Apache Zeppelin Hive JDBC 인터프리터는 Azure HDInsight에서 잘못된 URL을 가리킴
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 04288fdde74da705f6127c2444b0dc7ac7a2a8dd
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290542"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive Zeppelin 인터프리터가 Zookeeper 오류를 표시

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Hive LLAP 클러스터에서 쿼리 실행을 시도할 때 Zeppelin 인터프리터가 다음과 같은 오류 메시지를 표시합니다.

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>원인

Zeppelin Hive JDBC 인터프리터가 잘못된 URL을 가리킵니다.

## <a name="resolution"></a>해결 방법

1. Hive 구성 요소 요약으로 이동하여 “Hive JDBC Url”을 클립보드에 복사합니다.

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`로 이동

1. JDBC 설정 편집: 1단계에서 복사한 Hive JDBC URL의 hive.url 값을 업데이트

1. 저장한 다음 쿼리를 다시 시도

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
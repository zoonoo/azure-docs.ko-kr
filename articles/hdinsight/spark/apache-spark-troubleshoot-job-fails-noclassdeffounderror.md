---
title: NoClassDefFoundError-Azure HDInsight에서 Apache Kafka 데이터를 사용 하 여 Apache Spark
description: Azure HDInsight에서 NoClassDefFoundError을 사용 하 여 Apache Kafka 클러스터에서 데이터를 읽는 Apache Spark 스트리밍 작업이 실패 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c523dbc4612ebfcebf3923900b31f3a2a7b0c5c6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287770"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>HDInsight에서 NoClassDefFoundError을 사용 하 여 Apache Kafka 데이터를 읽는 Apache Spark 스트리밍 작업이 실패 함

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Spark 클러스터는 Apache Kafka 클러스터에서 데이터를 읽는 Spark 스트리밍 작업을 실행 합니다. Kafka stream 압축이 설정 되어 있으면 Spark 스트리밍 작업이 실패 합니다. 이 경우 오류로 인해 Spark streaming Yarn 앱 application_1525986016285_0193 실패 했습니다.

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>원인

이 오류는 `spark-streaming-kafka` 실행 중인 Kafka 클러스터의 버전과 다른 jar 파일의 버전을 지정 하 여 발생할 수 있습니다.

예를 들어 Kafka 클러스터 버전 0.10.1을 실행 하는 경우 다음 명령을 실행 하면 오류가 발생 합니다.

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>해결 방법

Spark-submit 명령을 옵션과 함께 사용 하 `–packages` 고, spark-streaming-kafka jar 파일의 버전이 실행 중인 kafka 클러스터의 버전과 동일한 지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
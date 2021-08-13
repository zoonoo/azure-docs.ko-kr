---
title: NoClassDefFoundError - Azure HDInsight에서 Apache Kafka 데이터를 사용하는 Apache Spark
description: Apache Kafka 클러스터에서 데이터를 읽는 Apache Spark 스트리밍 작업이 Azure HDInsight에서 NoClassDefFoundError를 나타내며 실패함
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946375"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Kafka 데이터를 읽는 Apache Spark 스트리밍 작업이 HDInsight에서 NoClassDefFoundError를 나타내며 실패함

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Spark 클러스터는 Apache Kafka 클러스터에서 데이터를 읽는 Spark 스트리밍 작업을 실행합니다. Kafka 스트림 압축이 설정되어 있으면 Spark 스트리밍 작업이 실패합니다. 이 경우 오류로 인해 Spark 스트리밍 Yarn 앱 애플리케이션 1525986016285_0193이 실패합니다.

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>원인

이 오류는 실행 중인 Kafka 클러스터의 버전과는 다른 `spark-streaming-kafka` jar 파일의 버전을 지정하여 발생할 수 있습니다.

예를 들어 Kafka 클러스터 버전 0.10.1을 실행하는 경우 다음 명령을 실행하면 오류가 발생합니다.

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>해결 방법

Spark-submit 명령을 `–packages` 옵션과 함께 사용하고 spark-streaming-kafka jar 파일의 버전이 실행 중인 kafka 클러스터의 버전과 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
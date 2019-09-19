---
title: NoClassDefFoundError-Azure HDInsight에서 Apache Kafka 데이터를 사용 하 여 Apache Spark
description: Azure HDInsight에서 NoClassDefFoundError을 사용 하 여 Apache Kafka 클러스터에서 데이터를 읽는 Apache Spark 스트리밍 작업이 실패 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 7ccd908c96e68190f09da37a83e0a34a09f5e697
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087143"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>HDInsight에서 NoClassDefFoundError을 사용 하 여 Apache Kafka 데이터를 읽는 Apache Spark 스트리밍 작업이 실패 함

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Apache Spark 클러스터는 Apache Kafka 클러스터에서 데이터를 읽는 Spark 스트리밍 작업을 실행 합니다. Kafka stream 압축이 설정 되어 있으면 Spark 스트리밍 작업이 실패 합니다. 이 경우 오류로 인해 Spark streaming Yarn app application_1525986016285_0193가 실패 했습니다.

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>원인

이 오류는 실행 중인 kafka 클러스터의 버전과 `spark-streaming-kafka` 다른 jar 파일의 버전을 지정 하 여 발생할 수 있습니다.

예를 들어 Kafka 클러스터 버전 0.10.1을 실행 하는 경우 다음 명령을 실행 하면 오류가 발생 합니다.

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>해결 방법

Spark-submit 명령을 `–packages` 옵션과 함께 사용 하 고, spark-streaming-kafka jar 파일의 버전이 실행 중인 kafka 클러스터의 버전과 동일한 지 확인 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

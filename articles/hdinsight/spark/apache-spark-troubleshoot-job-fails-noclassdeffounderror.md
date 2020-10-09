---
title: NoClassDefFoundError-Azure HDInsight에서 Apache Kafka 데이터를 사용 하 여 Apache Spark
description: Azure HDInsight에서 NoClassDefFoundError을 사용 하 여 Apache Kafka 클러스터에서 데이터를 읽는 Apache Spark 스트리밍 작업이 실패 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 4659274110add96613ca88560edfb459b20a99cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894340"
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

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.

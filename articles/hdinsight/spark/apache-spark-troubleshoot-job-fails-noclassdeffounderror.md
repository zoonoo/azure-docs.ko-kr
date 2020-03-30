---
title: NoClassDefFoundError - 아파치 스파크 와 아파치 카프카 데이터 에서 Azure HDInsight
description: 아파치 카프카 클러스터에서 데이터를 읽는 아파치 스파크 스트리밍 작업은 Azure HDInsight에서 NoClassDefFoundError로 실패
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 4659274110add96613ca88560edfb459b20a99cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894340"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>아파치 카프카 데이터를 읽는 아파치 스파크 스트리밍 작업은 HDInsight에서 NoClassDefFoundError로 실패

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 스파크 클러스터는 아파치 카프카 클러스터에서 데이터를 읽는 스파크 스트리밍 작업을 실행합니다. Kafka 스트림 압축이 켜져 있으면 스파크 스트리밍 작업이 실패합니다. 이 경우 Spark 스트리밍 원사 앱이 오류로 인해 application_1525986016285_0193 실패했습니다.

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>원인

이 오류는 실행 중인 Kafka `spark-streaming-kafka` 클러스터 버전과 다른 jar 파일 버전을 지정하여 발생할 수 있습니다.

예를 들어 Kafka 클러스터 버전 0.10.1을 실행 하는 경우 다음 명령에 오류가 발생 합니다.

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>해결 방법

`–packages` 옵션과 함께 Spark 제출 명령을 사용하고 스파크 스트리밍-kafka jar 파일의 버전이 실행 중인 Kafka 클러스터 버전과 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.

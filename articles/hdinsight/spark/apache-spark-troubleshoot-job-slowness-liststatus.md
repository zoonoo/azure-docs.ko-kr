---
title: Apache Spark 스트리밍 작업은 Azure HDInsight에서 처리 하는 데 평소 보다 오래 걸립니다.
description: Apache Spark 스트리밍 작업은 Azure HDInsight에서 처리 하는 데 평소 보다 오래 걸립니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679433"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>시나리오: Apache Spark 스트리밍 작업은 Azure HDInsight에서 처리 하는 데 평소 보다 오래 걸립니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

일부 Apache Spark 스트리밍 작업이 느리거나 처리 하는 데 평소 보다 오래 걸리는 것을 확인할 수 있습니다. Spark 스트리밍 응용 프로그램의 경우 각 메시지 일괄 처리는 Spark에 제출 된 한 작업에 해당 합니다. 일반적으로 작업에서 처리 하는 데 X 초가 소요 되는 경우 일반적으로 2-3 분 정도 걸릴 수 있습니다.

## <a name="cause"></a>원인

한 가지 가능한 원인은 Apache Kafka 생산자가 Kafka 클러스터에 쓰기를 완료 하는 데 2 분 이상 소요 되는 것입니다. Kafka 문제를 추가로 디버깅 하기 위해 Kafka 생산자를 사용 하는 코드에 일부 로깅을 추가 하 여 메시지를 보내고이를 Kafka 클러스터의 로그와 상호 연결할 수 있습니다.

WASB에 대 한 읽기 및 쓰기를 빈번 하 게 수행 하면 후속 마이크로 일괄 처리가 지연 될 수도 있습니다. 중복 항목을 제거 `Filesystem.listStatus` 하는 `O(n!)` 알고리즘 때문에의 WASB 구현은 매우 느립니다. 에서로의 `BlobListItem` `FileMetadata`추가변환 으로 인해 너무 많은 메모리를 사용 합니다.`FileStatus` 예를 들어 알고리즘은 70만 파일을 나열 하는 데 30 분 이상이 걸립니다. 따라서 모든 `ListBlobs` 마이크로 일괄 처리를 SparkSQL 하 여가 적극적으로 호출 되는 경우에는 후속 마이크로 일괄 처리가 지연 되어 높은 일정 지연 시간이 발생 하 게 됩니다. [이 패치](https://issues.apache.org/jira/browse/HADOOP-15547) 는 문제를 해결 하지만 사용자 환경에 누락 된 경우에 `ListBlobs` 는 대기 시간이 길어집니다. 또한 한 시간 마다 파일을 삭제 하는 경우에도 가비지 수집 프로세스가 아직 완료 되지 않았기 때문에 백 엔드의 목록에서 모든 행 (삭제 포함)을 반복 해야 합니다. 패치는 일부 문제를 해결할 수 있지만 가비지 수집 문제로 인해 일괄 처리의 스트림 처리에 지연이 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

[HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) 픽스를 적용 합니다. 가능 하지 않은 경우 check point 위치로 HDFS를 사용할 수 있습니다. `checkpointDirectory` 다음과`hdfs://mycluster/checkpoint`같은 항목으로 설정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.

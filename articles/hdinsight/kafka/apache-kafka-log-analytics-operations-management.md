---
title: Apache Kafka에 대한 Log Analytics - Azure HDInsight
description: Log Analytics를 사용하여 Azure HDInsight의 Apache Kafka 클러스터에서 로그를 분석하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: bd8bfb8775bc9c988bb7484ac25f189c3ff46991
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317282"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Log Analytics를 사용하여 HDInsight의 Apache Kafka에서 생성된 로그를 분석하는 방법을 알아봅니다.

## <a name="enable-log-analytics-for-apache-kafka"></a>Apache Kafka용 Log Analytics 사용

Log Analytics for HDInsight를 사용하도록 설정하는 단계는 모든 HDInsight 클러스터에서 동일합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 [Log Analytics 작업 영역 시작](https://docs.microsoft.com/azure/log-analytics) 문서를 참조하세요.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Log Analytics를 사용하도록 Kafka 클러스터를 구성합니다. 자세한 내용은 [Log Analytics를 사용하여 HDInsight 모니터링](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 문서를 참조하세요.

    > [!NOTE]
    > `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet을 통해 Log Analytics를 사용하도록 클러스터를 구성할 수도 있습니다. 이 cmdlet을 사용하려면 다음과 같은 정보가 필요합니다.
    >
    > * HDInsight 클러스터 이름.
    > * Log Analytics에 대한 작업 영역 ID. Log Analytics 작업 영역에서 작업 영역 ID를 찾을 수 있습니다.
    > * Log Analytics 연결의 기본 키. 기본 키를 찾으려면 Azure Portal에서 작업 영역을 열고, 왼쪽 메뉴에서 __고급 설정__을 선택합니다. 고급 설정에서 __연결된 원본__>__Linux 서버__를 선택합니다.


> [!IMPORTANT]
> Log Analytics에 데이터를 사용할 수 있을 때까지 약 20분 정도 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 선택합니다.

2. __로그 검색__을 선택합니다. 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 다음은 검색 예제입니다.

    * 디스크 사용량:`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * CPU 사용량: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * 초당 들어오는 메시지: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * 초당 들어오는 바이트: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * 초당 나가는 바이트: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > 쿼리 값을 클러스터 관련 정보로 바꿉니다. 예를 들어 `ClusterName_s`를 클러스터의 이름으로 설정해야 합니다. `HostName_s`는 클러스터의 작업자 노드 도메인 이름으로 설정해야 합니다.

    `*`를 입력하여 기록된 모든 종류를 검색할 수도 있습니다. 현재 다음 로그를 쿼리에 사용할 수 있습니다.

    | 로그 형식 | 설명 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX 메트릭 |

    ![CPU 사용량 검색의 이미지](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>다음 단계

Log Analytics에 대한 자세한 내용은 [Log Analytics 작업 영역 시작](../../log-analytics/log-analytics-get-started.md) 문서를 참조하세요.

Apache Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

 * [HDInsight 클러스터 간 Apache Kafka 미러링](apache-kafka-mirroring.md)
 * [HDInsight의 Apache Kafka 확장성 개선](apache-kafka-scalability.md)
 * [Apache Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
 * [Apache Kafka에서 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)

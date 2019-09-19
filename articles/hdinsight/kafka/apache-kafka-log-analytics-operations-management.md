---
title: Apache Kafka에 대 한 Azure Monitor 로그-Azure HDInsight
description: Azure Monitor 로그를 사용 하 여 Azure HDInsight의 Apache Kafka 클러스터에서 로그를 분석 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 5739883984d4087d2b2a1bda66c01ff3cfa10eb0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122589"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Azure Monitor 로그를 사용 하 여 HDInsight에서 Apache Kafka 생성 된 로그를 분석 하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka에 대 한 Azure Monitor 로그 사용

HDInsight에 대 한 Azure Monitor 로그를 사용 하도록 설정 하는 단계는 모든 HDInsight 클러스터에 대해 동일 합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 [Azure Monitor의 로그](../../azure-monitor/platform/data-platform-logs.md) 문서를 참조 하세요.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Azure Monitor 로그를 사용 하도록 Kafka 클러스터를 구성 합니다. 자세한 내용은 [Azure Monitor 로그를 사용 하 여 HDInsight 모니터링](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 문서를 참조 하세요.

> [!IMPORTANT]  
> Azure Monitor 로그에 데이터를 사용할 수 있을 때까지 약 20 분 정도 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 선택합니다.

2. 왼쪽 메뉴의 **일반**에서 **로그**를 선택 합니다. 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 쿼리 창에 쿼리를 입력 한 다음 **실행**을 선택 합니다. 다음은 검색 예제입니다.

* 디스크 사용:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU 사용량:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* 초당 들어오는 메시지 수:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 초당 수신 바이트:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 초당 나가는 바이트:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > 쿼리 값을 클러스터 관련 정보로 바꿉니다. 예를 들어 `ClusterName_s`를 클러스터의 이름으로 설정해야 합니다. `HostName_s`는 클러스터의 작업자 노드 도메인 이름으로 설정해야 합니다.

    `*`를 입력하여 기록된 모든 종류를 검색할 수도 있습니다. 현재 다음 로그를 쿼리에 사용할 수 있습니다.

    | 로그 형식 | 설명 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX 메트릭 |

    ![Apache kafka 로그 분석 cpu 사용량](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>다음 단계

Azure Monitor에 대 한 자세한 내용은 [Azure Monitor 개요](../../log-analytics/log-analytics-get-started.md)및 [HDInsight 클러스터 모니터링을 위한 Azure Monitor 로그 쿼리](../hdinsight-hadoop-oms-log-analytics-use-queries.md)를 참조 하세요.

Apache Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 클러스터 간 Apache Kafka 미러링](apache-kafka-mirroring.md)
* [HDInsight의 Apache Kafka 확장성 개선](apache-kafka-scalability.md)
* [Apache Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [Apache Kafka에서 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)

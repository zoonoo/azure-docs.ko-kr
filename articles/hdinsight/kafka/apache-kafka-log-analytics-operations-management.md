---
title: Apache Kafka에 대한 Azure Monitor 로그 - Azure HDInsight
description: Azure Monitor 로그를 사용하여 Azure HDInsight의 Apache Kafka 클러스터에서 로그를 분석하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 74db121c33864ee72ad984b49c8fa43afa0f598c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870520"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Azure Monitor 로그를 사용하여 HDInsight의 Apache Kafka에서 생성된 로그를 분석하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>로그 위치

클러스터의 Apache Kafka 로그는 `/var/log/kafka`에 있습니다. 관리 디스크를 사용하는지 여부에 관계 없이 Kafka 로그는 클러스터 수명 주기 간에 저장되거나 지속되지 않습니다. 사용 가능한 로그는 다음 표에 나와 있습니다.

|로그 |Description |
|---|---|
|kafka.out|Kafka 프로세스의 stdout 및 stderr입니다. 이 파일에서 Kafka 시작 및 종료 로그를 찾을 수 있습니다.|
|server.log|주 Kafka 서버 로그입니다. 모든 Kafka broker 로그는 여기에서 종료됩니다.|
|controller.log|Broker가 컨트롤러 역할을 하는 경우의 컨트롤러 로그입니다.|
|statechange.log|Broker로의 모든 상태 변경 이벤트는 이 파일에 기록됩니다.|
|kafka-gc.log|Kafka 가비지 수집 상태입니다.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka에 대해 Azure Monitor 로그 사용

HDInsight에 대한 Azure Monitor 로그를 사용하도록 설정하는 단계는 모든 HDInsight 클러스터에서 동일합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 [Azure Monitor의 로그](../../azure-monitor/logs/data-platform-logs.md) 문서를 참조하세요.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Azure Monitor 로그를 사용하도록 Kafka 클러스터를 구성합니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 HDInsight 모니터링](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 문서를 참조하세요.

> [!IMPORTANT]  
> Azure Monitor 로그에 데이터를 사용할 수 있을 때까지 약 20분 정도 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 선택합니다.

2. 왼쪽 메뉴의 **일반** 에서 **로그** 를 선택합니다. 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 쿼리 창에 쿼리를 입력한 다음 **실행** 을 선택합니다. 다음은 검색 예제입니다.

* 디스크 사용량:

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

* 초당 들어오는 메시지: (`your_kafka_cluster_name`을 클러스터 이름으로 대체합니다.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 초당 들어오는 바이트: (`wn0-kafka`를 작업자 노드 호스트 이름으로 대체합니다.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 초당 나가는 바이트: (`your_kafka_cluster_name`을 클러스터 이름으로 대체합니다.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    `*`를 입력하여 기록된 모든 종류를 검색할 수도 있습니다. 현재 다음 로그를 쿼리에 사용할 수 있습니다.

    | 로그 형식 | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX 메트릭 |

    :::image type="content" source="./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png" alt-text="Apache Kafka 로그 분석 CPU 사용량" border="true":::

## <a name="next-steps"></a>다음 단계

Azure Monitor에 대한 자세한 내용은 [Azure Monitor 개요](../../azure-monitor/overview.md) 및 [HDInsight 클러스터 모니터링을 위한 Azure Monitor 로그 쿼리](../hdinsight-hadoop-oms-log-analytics-use-queries.md)를 참조하세요.

Apache Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 클러스터 간 Apache Kafka 미러링](apache-kafka-mirroring.md)
* [HDInsight의 Apache Kafka 확장성 개선](apache-kafka-scalability.md)
* [Apache Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [Apache Kafka에서 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
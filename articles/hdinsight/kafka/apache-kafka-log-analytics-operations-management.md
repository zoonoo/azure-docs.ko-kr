---
title: 아파치 카프카에 대한 Azure 모니터 로그 - Azure HDInsight
description: Azure 모니터 로그를 사용하여 Azure HDInsight의 아파치 카프카 클러스터에서 로그를 분석하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471183"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Azure 모니터 로그를 사용하여 HDInsight에서 아파치 카프카가 생성한 로그를 분석하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>로그 위치

클러스터의 아파치 카프카 로그는 에 `/var/log/kafka`있습니다. Kafka 로그는 관리되는 디스크를 사용하는지에 관계없이 클러스터 수명 주기 간에 저장되거나 유지되지 않습니다. 다음 표에는 사용 가능한 로그가 표시됩니다.

|로그 |설명 |
|---|---|
|카프카.아웃|카프카 프로세스의 stdout및 stderr. 이 파일에서 Kafka 시작 및 종료 로그를 찾을 수 있습니다.|
|서버.로그|주요 카프카 서버 로그입니다. 모든 카프카 브로커 로그는 여기에 끝납니다.|
|컨트롤러.로그|브로커가 컨트롤러 역할을 하는 경우 컨트롤러 로그입니다.|
|상태 변경.log|브로커에 대한 모든 상태 변경 이벤트가 이 파일에 기록됩니다.|
|카프카-gc.log|카프카 쓰레기 수거 통계.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>아파치 카프카에 대한 Azure 모니터 로그 사용

HDInsight에 대한 Azure 모니터 로그를 활성화하는 단계는 모든 HDInsight 클러스터에 대해 동일합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 Azure Monitor 문서의 [로그를](../../azure-monitor/platform/data-platform-logs.md) 참조하십시오.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Azure 모니터 로그를 사용하도록 Kafka 클러스터를 구성합니다. 자세한 내용은 AZURE [모니터 사용 로그를 참조하여 HDInsight 문서를 모니터링합니다.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Azure Monitor 로그에 데이터를 사용할 수 있는 데 약 20분이 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. Azure [포털에서](https://portal.azure.com)로그 분석 작업 영역을 선택합니다.

2. 왼쪽 메뉴에서 **일반**에서 **로그를 선택합니다.** 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 쿼리 창에 쿼리를 입력한 다음 **실행을**선택합니다. 다음은 검색 예제입니다.

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

* 초당 수신 메시지: (클러스터 이름으로 바꾸기). `your_kafka_cluster_name`

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 초당 수신 바이트: (작업자 `wn0-kafka` 노드 호스트 이름으로 바꾸기).

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 초당 나가는 바이트: (클러스터 `your_kafka_cluster_name` 이름으로 바꾸기).

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    `*`를 입력하여 기록된 모든 종류를 검색할 수도 있습니다. 현재 다음 로그를 쿼리에 사용할 수 있습니다.

    | 로그 형식 | 설명 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX 메트릭 |

    ![아파치 카프카 로그 분석 CPU 사용량](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>다음 단계

Azure 모니터에 대한 자세한 내용은 [Azure 모니터 개요](../../log-analytics/log-analytics-get-started.md)및 쿼리 Azure 모니터 [로그를 참조하여 HDInsight 클러스터를 모니터링합니다.](../hdinsight-hadoop-oms-log-analytics-use-queries.md)

Apache Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 클러스터 간 Apache Kafka 미러링](apache-kafka-mirroring.md)
* [HDInsight에서 아파치 카프카의 규모를 증가](apache-kafka-scalability.md)
* [Apache Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [Apache Kafka에서 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)

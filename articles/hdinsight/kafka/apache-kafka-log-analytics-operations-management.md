---
title: Apache Kafka에 대한 Log Analytics - Azure HDInsight | Microsoft Docs
description: Log Analytics를 사용하여 Azure HDInsight의 Apache Kafka 클러스터에서 로그를 분석하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: larryfr
ms.openlocfilehash: 15107a0fbcd6242ac13f366b16be10efaeaad6bb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Log Analytics를 사용하여 HDInsight의 Apache Kafka에서 생성된 로그를 분석하는 방법을 알아봅니다.

## <a name="enable-log-analytics-for-kafka"></a>Kafka용 Log Analytics 사용

Log Analytics for HDInsight를 사용하도록 설정하는 단계는 모든 HDInsight 클러스터에서 동일합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 [Log Analytics 작업 영역 시작](https://docs.microsoft.com/azure/log-analytics) 문서를 참조하세요.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Log Analytics를 사용하도록 Kafka 클러스터를 구성합니다. 자세한 내용은 [Log Analytics를 사용하여 HDInsight 모니터링](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 문서를 참조하세요.

    > [!NOTE]
    > `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet을 통해 Log Analytics를 사용하도록 클러스터를 구성할 수도 있습니다. 이 cmdlet을 사용하려면 다음과 같은 정보가 필요합니다.
    >
    > * HDInsight 클러스터 이름.
    > * Log Analytics에 대한 작업 영역 ID. Log Analytics 작업 영역에서 작업 영역 ID를 찾을 수 있습니다.
    > * Log Analytics 연결의 기본 키. 기본 키를 찾으려면 Log Analytics 인스턴스를 선택한 다음, __OMS 포털__을 선택합니다. OMS 포털에서 __설정__, __연결된 원본__, __Linux 서버__를 차례로 선택합니다.


> [!IMPORTANT]
> Log Analytics에 데이터를 사용할 수 있을 때까지 약 20분 정도 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 선택합니다.

2. __로그 검색__을 선택합니다. 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 다음은 검색 예제입니다.

    * 디스크 사용량:`Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * CPU 사용량: `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * 초당 들어오는 메시지: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * 초당 들어오는 바이트: `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * 초당 나가는 바이트: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

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

Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

 * [HDInsight 클러스터 간 Kafka 미러링](apache-kafka-mirroring.md)
 * [HDInsight의 Kafka 확장성 개선](apache-kafka-scalability.md)
 * [Kafka에 Spark 스트림(DStreams) 사용](../hdinsight-apache-spark-with-kafka.md)
 * [Kafka에 Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
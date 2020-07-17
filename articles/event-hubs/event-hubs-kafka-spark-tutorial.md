---
title: Apache Spark 앱과의 연결 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Kafka에 대한 Azure Event Hubs에서 Apache Spark를 사용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 00925242d5685749aba27ad2fc537ffb07f4c68d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320107"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Azure Event Hubs를 사용 하 여 Apache Spark 응용 프로그램 연결
이 자습서에서는 Spark 응용 프로그램을 실시간 스트리밍에 대 한 Event Hubs 연결 하는 과정을 안내 합니다. 이렇게 통합하면 프로토콜 클라이언트를 변경하거나 사용자 고유의 Kafka 또는 Zookeeper 클러스터를 실행할 필요 없이 스트리밍이 가능합니다. 이 자습서에서는 Apache Spark v2.4+ 및 Apache Kafka v2.0+가 필요합니다.

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)에서 사용할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Spark 실행
> * Kafka용 Event Hubs에서 읽기
> * Kafka용 Event Hubs에 쓰기

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 먼저 다음 사항을 확인해야 합니다.
-   동작합니다. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Spark-Kafka 어댑터는 Spark v2.4부터 Kafka v2.0을 지원하도록 업데이트되었습니다. 이전 Spark 릴리스에서는 어댑터가 Kafka v0.10 이상을 지원했지만 Kafka v0.10 API에 의존했습니다. Kafka용 Event Hubs에서 Kafka v0.10을 지원하지 않기 때문에 Spark v2.4 미만 버전의 Spark-Kafka 어댑터는 Kafka 에코시스템용 Event Hubs에서 지원되지 않습니다.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. 네임 스페이스 및 이벤트 허브를 만드는 방법에 대 한 지침은 [이벤트 허브 만들기](event-hubs-create.md) 를 참조 하세요. 나중에 사용할 수 있도록 Event Hubs 연결 문자열 및 FQDN(정규화된 도메인 이름)을 가져옵니다. 자세한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. 

## <a name="clone-the-example-project"></a>프로젝트 예제 복제
Azure Event Hubs 리포지토리를 복제하고 `tutorials/spark` 하위 폴더로 이동합니다.

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Kafka용 Event Hubs에서 읽기
구성을 약간 변경하면 Kafka용 Event Hubs에서 데이터 읽기를 시작할 수 있습니다. **BOOTSTRAP_SERVERS** 및 **EH_SASL**을 네임스페이스의 세부 정보로 업데이트하면 Kafka와 같은 방법으로 Event Hubs를 사용하여 스트리밍을 시작할 수 있습니다. 전체 샘플 코드는 GitHub의 sparkConsumer.scala 파일을 참조하세요. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Kafka용 Event Hubs에 쓰기
또한 Kafka에 쓰는 것과 동일한 방법으로 Event Hubs에 쓸 수도 있습니다. 잊지 말고 **BOOTSTRAP_SERVERS** 및 **EH_SASL**을 Event Hubs 네임스페이스의 정보로 업데이트해야 합니다.  전체 샘플 코드는 GitHub의 sparkProducer.scala 파일을 참조하세요. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md)
- [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect를 이벤트 허브와 통합](event-hubs-kafka-connect-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka streams 스트림을 이벤트 허브에 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)


---
title: Azure Event Hubs와 Apache Spark 통합 | Microsoft Docs
description: Apache Spark와 통합하여 Event Hubs를 통해 구조적 스트리밍을 사용하도록 설정합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Azure Event Hubs와 Apache Spark 통합

Azure Event Hubs는 [Apache Spark](https://spark.apache.org/)와 원활하게 통합되어 _종단 간_ 분산 스트리밍 응용 프로그램을 쉽게 구축할 수 있습니다. 이 통합은 [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark 스트리밍](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [구조적 스트리밍](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)을 지원합니다. Apache Spark용 Event Hubs 커넥터는 [GitHub](https://github.com/Azure/azure-event-hubs-spark)에서 사용할 수 있습니다. 이 라이브러리는 [Maven 중앙 리포지토리](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)의 Maven 프로젝트에서도 사용할 수 있습니다.

이 문서에서는 [Azure Databrick](https://azure.microsoft.com/services/databricks/)에서 지속적인 응용 프로그램을 만드는 방법에 대해 설명합니다. [Azure Databricks](https://azure.microsoft.com/services/databricks/)는 이 문서에서 사용하지만, Spark 클러스터는 [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview)에서도 사용할 수 있습니다.

다음 예제에서는 두 개의 Scala 노트북을 사용합니다. 하나는 Event Hub에서 이벤트를 스트리밍하기 위한 것이고, 다른 하나는 이벤트를 Event Hub에 다시 보내기 위한 것입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Event Hubs 인스턴스. 아직 없는 경우 [하나를 만듭니다](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) 인스턴스. 아직 없는 경우 [하나를 만듭니다](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).
* [Maven 좌표를 사용하여 라이브러리 만들기](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

노트북에서 다음 코드 조각을 사용하여 Event Hub에서 이벤트를 스트리밍합니다.

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
다음 코드 조각은 Spark의 일괄 처리 API를 사용하여 Event Hub에 이벤트를 보내는 데 사용됩니다. 스트리밍 쿼리를 작성하여 Event Hub에 이벤트를 보낼 수도 있습니다.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

이 문서에서는 실시간 내결함성 스트리밍 솔루션을 구축하기 위해 Event Hubs 커넥터가 작동하는 방식을 보여 주었습니다. 다음 단계에 따라 구조적 스트리밍 및 Event Hubs 통합 커넥터에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

* [구조적 스트리밍 + Azure Event Hubs 통합 가이드](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark 스트리밍 + Event Hubs 통합 가이드](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)

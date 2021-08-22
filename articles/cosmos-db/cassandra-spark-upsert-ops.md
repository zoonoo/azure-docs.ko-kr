---
title: Spark에서 Azure Cosmos DB Cassandra API에 데이터 Upsert
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 테이블로 Upsert하는 방법을 자세히 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: ab09908a51004c1f9c2cf202705c47481271427a
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355342"
---
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 데이터 Upsert
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API로 데이터를 Upsert하는 방법을 설명합니다.

## <a name="cassandra-api-configuration"></a>Cassandra API 구성

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Spark 3.0 또는 그 이상 버전을 사용하는 경우 Cosmos DB 도우미와 연결 팩터리를 설치할 필요가 없습니다. Spark 3 커넥터에 `connections_per_executor_max` 대신 `remoteConnectionsPerExecutor`을 사용해야 합니다(위 참조). 상단의 Notebook 내에서 연결 관련 속성이 정의된 것을 볼 수 있습니다. 아래 구문을 사용하여 클러스터 수준(Spark 컨텍스트 초기화)에서 정의하지 않고도 이러한 방식으로 연결 속성을 정의할 수 있습니다. 그러나 Spark 컨텍스트가 필요한 작업(예: 아래에 표시된 `update`의 경우 `CassandraConnector(sc)`)을 사용하는 경우 연결 속성을 클러스터 수준에서 정의해야 합니다.

## <a name="dataframe-api"></a>데이터 프레임 API

### <a name="create-a-dataframe"></a>데이터 프레임 만들기 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>데이터 Upsert

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>데이터 업데이트

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300' and book_pub_year = 1901;"))
```

## <a name="rdd-api"></a>RDD API
> [!NOTE]
> RDD API에서의 Upsert는 만들기 작업과 같습니다. 

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB Cassandra API 테이블에 저장된 데이터에 대해 다른 작업을 수행하려면 다음 문서를 계속 진행합니다.
 
* [삭제 작업](cassandra-spark-delete-ops.md)
* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

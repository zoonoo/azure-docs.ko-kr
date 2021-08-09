---
title: Spark에서 Azure Cosmos DB Cassandra API에 데이터 만들기 또는 삽입
description: 이 문서에서는 Azure Cosmos DB Cassandra API 테이블에 데이터를 삽입하는 방법을 자세히 설명합니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 585c2985850d47b8df80df6d748bff3a8fafcb1b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464939"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에서 데이터 만들기/삽입
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]
 
이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 테이블에 샘플 데이터를 삽입하는 방법을 설명합니다.

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
> Spark 3.0 또는 그 이상 버전을 사용하는 경우 Cosmos DB 도우미와 연결 팩터리를 설치할 필요가 없습니다. Spark 3 커넥터에 `connections_per_executor_max` 대신 `remoteConnectionsPerExecutor`을 사용해야 합니다(위 참조). 상단의 Notebook 내에서 연결 관련 속성이 정의된 것을 볼 수 있습니다. 아래 구문을 사용하여 클러스터 수준(Spark 컨텍스트 초기화)에서 정의하지 않고도 이러한 방식으로 연결 속성을 정의할 수 있습니다.

## <a name="dataframe-api"></a>데이터 프레임 API

### <a name="create-a-dataframe-with-sample-data"></a>샘플 데이터를 사용하여 데이터 프레임 만들기

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> 행 수준에서 “없는 경우 만들기” 기능은 아직 지원되지 않습니다.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API로 유지

또한 데이터를 저장할 때 다음 예제와 같이 Time-to-Live 및 일관성 정책 설정을 설정할 수 있습니다.

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> 열 수준 TTL은 아직 지원되지 않습니다.

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>RDD(복원력 있는 분산된 데이터베이스) API

### <a name="create-a-rdd-with-sample-data"></a>샘플 데이터로 RDD 만들기
```scala
//Drop and re-create table to delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))

cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> 없는 경우 만들기 기능은 아직 지원되지 않습니다.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API로 유지

또한 Cassandra API에 데이터를 저장할 때 다음 예제와 같이 Time-to-Live 및 일관성 정책 설정을 설정할 수 있습니다.

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB Cassandra API 테이블에 데이터를 삽입한 후 Cosmos DB Cassandra API에 저장된 데이터에 대해 다른 작업을 수행하려면 다음 문서를 계속 진행합니다.
 
* [읽기 작업](cassandra-spark-read-ops.md)
* [Upsert 작업](cassandra-spark-upsert-ops.md)
* [삭제 작업](cassandra-spark-delete-ops.md)
* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

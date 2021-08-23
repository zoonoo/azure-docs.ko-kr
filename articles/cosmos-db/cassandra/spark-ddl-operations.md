---
title: Spark에서 Azure Cosmos DB Cassandra API의 DDL 작업
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API에 대한 키스페이스 및 테이블 DDL 작업을 자세히 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 99eee3fd7506f3baf8b119b3fdf4ba881850d61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566321"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API의 DDL 작업
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API에 대한 키스페이스 및 테이블 DDL 작업을 자세히 설명합니다.

## <a name="spark-context"></a>Spark 컨텍스트

 Cassandra API용 커넥터에서는 Spark 컨텍스트의 일부분으로 Cassandra 연결 세부 정보를 초기화해야 합니다. Notebook을 시작하면 Spark 컨텍스트가 이미 초기화되어 있으므로 중지했다가 다시 초기화하는 것은 권하지 않습니다. 이러한 경우에 사용할 수 있는 방법 중 하나는 클러스터 Spark 구성에서 클러스터 수준에 Cassandra API 인스턴스 구성을 추가하는 것입니다. 이 작업은 클러스터당 한 번씩만 수행하면 됩니다. 다음 코드를 공백으로 구분된 키-값 쌍으로 Spark 구성에 추가합니다.
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="cassandra-api-related-configuration"></a>Cassandra API 관련 구성 

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra
//spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

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
> Spark 3.0 또는 그 이상 버전을 사용하는 경우 Cosmos DB 도우미와 연결 팩터리를 설치할 필요가 없습니다. Spark 3 커넥터에 `connections_per_executor_max` 대신 `remoteConnectionsPerExecutor`을 사용해야 합니다(위 참조).

## <a name="keyspace-ddl-operations"></a>키스페이스 DDL 작업

### <a name="create-a-keyspace"></a>키스페이스 만들기

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 앞에서 만든 키스페이스가 표시되어야 합니다.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>키스페이스 삭제

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>테이블 DDL 작업

**고려 사항:**  

- create table 문을 사용하여 테이블 수준에서 처리량을 할당할 수 있습니다.  
- 하나의 파티션 키는 20GB의 데이터를 저장할 수 있습니다.  
- 하나의 레코드는 최대 2MB의 데이터를 저장할 수 있습니다.  
- 하나의 파티션 키 범위는 여러 파티션 키를 저장할 수 있습니다.

### <a name="create-a-table"></a>테이블 만들기

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 "books"라는 테이블이 표시되어야 합니다. 

```bash
USE books_ks;
DESCRIBE books;
```

프로비전된 처리량 및 기본 TTL 값은 이전 명령의 출력에 표시되지 않습니다. 포털에서 이러한 값을 가져올 수 있습니다.

### <a name="alter-table"></a>alter table

alter table 명령을 사용하여 다음 값을 변경할 수 있습니다.

* 프로비전된 처리량 
* TTL(Time to Live) 값
<br>열 변경은 현재 지원되지 않습니다.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>테이블 삭제

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 "books" 테이블이 더 이상 지원되지 않는다고 표시되어야 합니다.

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>다음 단계

키스페이스 및 테이블을 만들면 CRUD 작업 등에 대한 다음 문서를 진행합니다.
 
* [만들기/삽입 작업](spark-create-operations.md)  
* [읽기 작업](spark-read-operation.md)  
* [Upsert 작업](spark-upsert-operations.md)  
* [삭제 작업](spark-delete-operation.md)  
* [집계 작업](spark-aggregation-operations.md)  
* [테이블 복사 작업](spark-table-copy-operations.md)  

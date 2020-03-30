---
title: Azure Cosmos DB에 Apache Spark 연결
description: Azure Cosmos DB에 Apache Spark를 연결할 수 있도록 하는 Azure Cosmos DB Spark 커넥터에 대해 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bbc97489f0c7045040dd0189b97946c2bd8fb3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481639"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화

코스모스 DB 스파크 커넥터를 사용하여 Azure 코스모스 DB에 저장된 데이터로 [스파크](https://spark.apache.org/) 작업을 실행할 수 있습니다. Cosmos는 배치 및 스트림 처리에 사용할 수 있으며 대기 시간 지연이 적은 서브 레이어로 사용할 수 있습니다.

[Azure 데이터 브릭](https://azure.microsoft.com/services/databricks) 또는 Azure에서 관리되는 스파크 클러스터를 제공하는 [Azure HDInsight와](https://azure.microsoft.com/services/hdinsight/)함께 커넥터를 사용할 수 있습니다. 다음 표에는 지원되는 Spark 버전이 표시됩니다.

| 구성 요소 | 버전 |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x 및 2.1.x |
| 스칼라 | 2.11 |
| Azure Databricks 런타임 버전 | > 3.4 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 핵심(SQL) API를 지원합니다.
> 몽고DB API용 코스모스 DB의 경우 [몽고DB 스파크 커넥터를](https://docs.mongodb.com/spark-connector/master/)사용합니다.
> 코스모스 DB 카산드라 API의 경우 [카산드라 스파크 커넥터를](https://github.com/datastax/spark-cassandra-connector)사용합니다.
>

## <a name="quickstart"></a>빠른 시작

* [Java SDK를 시작하기](sql-api-async-java-get-started.md) 의 단계를 수행하여 Cosmos DB 계정을 설정하고 일부 데이터를 채웁니다.
* [Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 작업 영역 및 클러스터 를 설정하기 시작하는 Azure Databricks의 단계를 따릅니다.
* 이제 새 전자 필기장을 만들고 Cosmos DB 커넥터 라이브러리를 가져올 수 있습니다. 작업 영역을 설정하는 방법에 대한 자세한 내용은 [Cosmos DB 커넥터작업으로](#bk_working_with_connector) 이동합니다.
* 다음 섹션에는 커넥터를 사용하여 읽고 쓰는 방법에 대한 스니펫이 있습니다.

### <a name="batch-reads-from-cosmos-db"></a>코스모스 DB에서 일괄 읽기

다음 코드 조각은 Pyspark의 코스모스 DB에서 읽을 스파크 데이터 프레임을 만드는 방법을 보여줍니다.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

스칼라에서 동일한 코드 스니펫:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>코스모스 DB에 일괄 기록

다음 코드 조각은 PySpark의 코스모스 DB에 데이터 프레임을 작성하는 방법을 보여 주며 있습니다.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

스칼라에서 동일한 코드 스니펫:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>코스모스 DB에서 읽는 스트리밍

다음 코드 조각은 Azure Cosmos DB 변경 피드에 연결하고 읽는 방법을 보여 주며 있습니다.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
스칼라에서 동일한 코드 스니펫:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>코스모스 DB에 스트리밍 기록

다음 코드 조각은 PySpark의 코스모스 DB에 데이터 프레임을 작성하는 방법을 보여 주며 있습니다.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

스칼라에서 동일한 코드 스니펫:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
더 많은 조각과 끝에서 끝까지 샘플, [Jupyter를](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)참조하십시오.

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>커넥터 작업

GitHub의 소스에서 커넥터를 빌드하거나 아래 링크에서 Maven의 Uber 항아리를 다운로드할 수 있습니다.

| Spark | 스칼라 | 최신 버전 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-코스모스db-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-코스모스db-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-코스모스db-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-코스모스db-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>데이터브릭스 노트북 사용

Azure 데이터브릭 가이드 > [Azure Cosmos DB 스파크 커넥터 사용의](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html) 지침에 따라 Databricks 작업 영역을 사용하여 라이브러리 만들기

> [!NOTE]
> Azure **Cosmos DB 스파크 커넥터 사용** 페이지는 현재 최신 이 아닙니다. 여섯 개의 개별 항아리를 6개의 다른 라이브러리에 다운로드하는 대신, 메이븐에서 동네 짱 항아리를 https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) 다운로드하고이 하나의 항아리 / 라이브러리를 설치할 수 있습니다.
> 

### <a name="using-spark-cli"></a>스파크 클리 사용

스파크 `spark-shell`클라이(즉, , 또는 `pyspark` `spark-submit`, 및)를 사용하여 커넥터로 `--packages` 작업하려면 커넥터의 [maven 좌표와](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)함께 매개변수를 사용할 수 있습니다.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>주피터 노트북 사용

HDInsight 내에서 Jupyter 노트북을 사용하는 경우 스파크 매직 `%%configure` 셀을 사용하여 커넥터의 메이븐 좌표를 지정할 수 있습니다.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 이 `spark.jars.excludes` 포함은 커넥터, 아파치 스파크 및 리비 간의 잠재적 충돌을 제거하기 위한 것입니다.

### <a name="build-the-connector"></a>커넥터 빌드

현재 이 커넥터 프로젝트는 `maven` 종속성 없이 빌드하는 데 사용하므로 다음을 실행할 수 있습니다.

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>샘플 작업

[코스모스 DB 스파크 GitHub 리포지토리에는](https://github.com/Azure/azure-cosmosdb-spark) 시도할 수 있는 다음 샘플 노트북과 스크립트가 있습니다.

* **스파크와 코스모스 DB(시애틀)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html로](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)정시 비행 성능 : HDInsight Jupyter 노트북 서비스를 사용하여 스파크를 코스모스 DB에 연결하여 스파크 SQL, GraphFrame 및 ML 파이프라인을 사용하여 비행 지연을 예측합니다.
* **아파치 스파크와 Azure 코스모스 DB 변경 피드와 트위터 소스**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **아파치 스파크를 사용하여 코스모스 DB 그래프를 쿼리**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* 을 `azure-cosmosdb-spark` **[사용하여 Azure 데이터 브릭을 Azure 코스모스 DB에 연결합니다.](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  여기에 연결된 Azure Databricks 버전의 [정시 비행 성능 노트북도](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)있습니다.
* **[Azure 코스모스 DB 및 HDInsight(아파치 스파크)가 있는 람다 아키텍처](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: 코스모스 DB와 스파크를 사용하여 빅 데이터 파이프라인을 유지 관리하는 운영 오버헤드를 줄일 수 있습니다.

## <a name="more-information"></a>추가 정보

`azure-cosmosdb-spark` [우리는 위키에](https://github.com/Azure/azure-cosmosdb-spark/wiki) 더 많은 정보를 가지고 있습니다 :

* [Azure 코스모스 DB 스파크 커넥터 사용자 가이드](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [집계 예제](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>구성 및 설정

* [스파크 커넥터 구성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [스파크에서 코스모스 DB 커넥터 설정(진행](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) 중)
* [아파치 스파크 (HDI)를 통해 Azure 코스모스 DB에 전원 BI 직접 쿼리 구성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>문제 해결

* [코스모스 DB 집계 사용](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [알려진 문제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>성능

* [성능 팁](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [쿼리 테스트 실행](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [테스트 실행 기록](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>피드 변경

* [Azure Cosmos DB 변경 피드 및 아파치 스파크를 사용한 스트림 처리 변경 사항](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [피드 데모 변경](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [구조화 스트림 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>모니터링

* [애플리케이션 인사이트로 스파크 작업 모니터링](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Azure Cosmos DB 커넥터로 Spark를 다운로드합니다. 리포지토리에서 다음 추가 리소스를 살펴보세요.

* [집계 예제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

[또한 아파치 스파크 SQL, 데이터 프레임 및 데이터 집합 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html)및 Azure HDInsight 문서의 [아파치 스파크를](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 검토할 수도 있습니다.

---
title: Databricks (Spark)를 사용 하 여 Apache Cassandra에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션
description: Azure Databricks 및 Spark를 사용 하 여 Apache Cassandra 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932915"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Azure Databricks를 사용 하 여 Cassandra에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API는 다음과 같은 다양 한 이유로 Apache Cassandra에서 실행 되는 엔터프라이즈 워크 로드에 적합 한 선택이 되었습니다. 

* **관리 및 모니터링의 오버 헤드 없음:** OS, JVM, yaml 파일 및 상호 작용에서 많은 설정을 관리 하 고 모니터링 하는 오버 헤드를 제거 합니다.

* **상당한 비용 절감:** Azure Cosmos DB를 사용 하 여 비용을 절감할 수 있습니다. 여기에는 VM의 비용, 대역폭 및 모든 해당 라이선스를 포함 합니다. 또한 데이터 센터, 서버, SSD 저장소, 네트워킹 및 전기 비용을 관리할 필요가 없습니다. 

* **기존 코드 및 도구를 사용 하는 기능:** Azure Cosmos DB는 기존 Cassandra Sdk 및 도구와의 유선 프로토콜 수준 호환성을 제공 합니다. 이 호환성을 통해 간단한 변경 사항이 있는 Azure Cosmos DB Cassandra API와 함께 기존 코드베이스를 사용할 수 있습니다.

한 플랫폼에서 다른 플랫폼으로 데이터베이스 워크 로드를 마이그레이션하는 다양 한 방법이 있습니다. [Azure Databricks](https://azure.microsoft.com/services/databricks/) 는 대규모에서 오프 라인 마이그레이션을 수행 하는 방법을 제공 하는 [Apache Spark](https://spark.apache.org/) 에 대 한 서비스 제공 플랫폼입니다. 이 문서에서는 Azure Databricks를 사용 하 여 네이티브 Apache Cassandra keyspaces/tables에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 데 필요한 단계를 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Cosmos DB Cassandra API 계정 프로비전](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API 연결 관련 기본 사항 검토](cassandra-spark-generic.md)

* [Azure Cosmos DB Cassandra API에서 지원 되는 기능](cassandra-support.md) 을 검토 하 여 호환성을 확인 합니다.

* 대상 Azure Cosmos DB Cassandra API 계정에 빈 keyspace 및 테이블이 이미 만들어졌는지 확인 합니다.

* [유효성 검사에 cqlsh 또는 hosted shell 사용](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks 클러스터 프로비전

지침에 따라 [Azure Databricks 클러스터를 프로 비전](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)할 수 있습니다. 그러나 Apache Spark 3. x는 Apache Cassandra 커넥터에 대해 현재 지원 되지 않습니다. Apache Spark의 지원 되는 v2. x 버전을 사용 하 여 Databricks 런타임을 프로 비전 해야 합니다. Databricks 런타임의 버전 6.6을 권장 합니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks 런타임":::


## <a name="add-dependencies"></a>종속성 추가

기본 및 Cosmos DB Cassandra 끝점에 연결 하려면 Apache Spark Cassandra 커넥터 라이브러리를 클러스터에 추가 해야 합니다. 클러스터에서 라이브러리를 선택 하 > > maven-> search 패키지를 설치 합니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 검색 패키지":::

`Cassandra`검색 상자에를 입력 하 고 `spark-cassandra-connector` 사용 가능한 최신 maven 리포지토리를 선택한 다음 설치를 선택 합니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks 패키지 선택":::

> [!NOTE]
> Cassandra 커넥터 라이브러리가 설치 된 후 Databricks 클러스터를 다시 시작 해야 합니다.

## <a name="create-scala-notebook-for-migration"></a>마이그레이션을 위해 Scala 노트북 만들기

다음 코드를 사용 하 여 Databricks에서 Scala 노트북을 만듭니다. 원본 및 대상 cassandra 구성을 해당 자격 증명, 원본/대상 키 공간 및 테이블로 바꾼 후 다음을 실행 합니다.

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes`및 `connections_per_executor_max` 구성은 프로 비전 된 처리량을 초과 하는 Cosmos DB 요청 ([요청 단위](https://docs.microsoft.com/azure/cosmos-db/request-units))을 초과 하는 경우 발생 하는 [속도 제한을](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)방지 하는 데 중요 합니다. Spark 클러스터의 실행자 수에 따라 이러한 설정을 조정 해야 할 수 있으며, 대상 테이블에 기록 되는 각 레코드의 크기 (및 그에 따른 비용)를 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API의 탄력적인 확장](manage-scale-cassandra.md)

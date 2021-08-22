---
title: Databricks(Spark)를 사용하여 Apache Cassandra에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션
description: Azure Databricks 및 Spark를 사용하여 Apache Cassandra 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 36395b8db4070dce3b26e41fcb01293e3074a24e
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393263"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Azure Databricks를 사용하여 Cassandra에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API는 다음과 같은 이유로 Apache Cassandra에서 실행되는 엔터프라이즈 워크로드에 적합한 선택이 되었습니다.

* **관리 및 모니터링의 오버헤드 없음:** OS, JVM 및 YAML 파일과 그 상호 작용에 대한 설정 관리 및 모니터링의 오버헤드를 제거합니다.

* **대폭적인 비용 절감:** Azure Cosmos DB를 사용하여 비용을 절감할 수 있습니다. 여기에는 VM, 대역폭 및 모든 해당 라이선스의 비용이 포함됩니다. 데이터 센터, 서버, SSD 스토리지, 네트워킹 및 전기 비용을 관리할 필요가 없습니다.

* **기존 코드 및 도구 사용 가능**: Azure Cosmos DB는 기존 Cassandra SDK 및 도구와 유선 프로토콜 수준의 호환성을 제공합니다. 이러한 호환성을 통해 기존 코드 베이스를 약간만 변경하여 Azure Cosmos DB Cassandra API와 함께 사용할 수 있습니다.

한 플랫폼에서 다른 플랫폼으로 데이터베이스 워크로드를 마이그레이션하는 방법에는 여러 가지가 있습니다. [Azure Databricks](https://azure.microsoft.com/services/databricks/)는 대규모 오프라인 마이그레이션을 수행하는 방법을 제공하는 [Apache Spark](https://spark.apache.org/)용 PaaS(Platform as a Service) 제품입니다. 이 문서에서는 Azure Databricks를 사용하여 네이티브 Apache Cassandra 키 공간 및 테이블에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Cosmos DB Cassandra API 계정을 프로비저닝합니다](create-cassandra-dotnet.md#create-a-database-account).

* [Azure Cosmos DB Cassandra API에 연결하는 기본 사항을 검토합니다](cassandra-spark-generic.md).

* [Azure Cosmos DB Cassandra API에서 지원되는 기능](cassandra-support.md)을 검토하여 호환성을 확인합니다.

* 대상 Azure Cosmos DB Cassandra API 계정에 이미 빈 키 공간 및 테이블을 만들었는지 확인합니다.

* [유효성 검사를 위해 cqlsh 또는 호스트된 셸을 사용합니다](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks 클러스터 프로비전

지침에 따라 [Azure Databricks 클러스터를 프로비저닝](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)할 수 있습니다. Spark 3.0을 지원하는 Databricks Runtime 버전 7.5를 선택하는 것이 좋습니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks Runtime 버전 찾기를 보여 주는 스크린샷":::

## <a name="add-dependencies"></a>종속성 추가

네이티브 및 Azure Cosmos DB Cassandra 엔드포인트 모두에 연결하려면 Apache Spark Cassandra 커넥터 라이브러리를 클러스터에 추가해야 합니다. 클러스터에서 **라이브러리** > **새로 설치** > **Maven** 을 선택한 다음 Maven 좌표에 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`을 추가합니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks에서 Maven 패키지 검색을 보여 주는 스크린샷":::

**설치** 를 선택한 다음 설치가 완료되면 클러스터를 다시 시작합니다.

> [!NOTE]
> Cassandra Connector 라이브러리가 설치된 후 Databricks 클러스터를 다시 시작해야 합니다.

## <a name="create-scala-notebook-for-migration"></a>마이그레이션을 위한 Scala Notebook 만들기

Databricks에서 Scala Notebook을 만듭니다. 원본 및 대상 Cassandra 구성을 해당 자격 증명, 원본 및 대상 키 공간과 테이블로 바꿉니다. 다음 코드를 실행합니다.

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
    "spark.cassandra.output.concurrent.writes" -> "1000",
    //"spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1", // Spark 3.x
    "spark.cassandra.connection.connections_per_executor_max"-> "1", // Spark 2.x
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
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows` 및 `spark.cassandra.output.concurrent.writes` 값과 Spark 클러스터의 작업자 수는 [트래픽률 제한](/samples/azure-samples/azure-cosmos-cassandra-extensions-java-sample-v4/azure-cosmos-cassandra-extensions-java-sample-v4/)을 피하기 위해 조정해야 할 중요한 구성입니다. 트래픽률 제한은 Azure Cosmos DB에 대한 요청이 프로비저닝된 처리량 또는 RU([요청 단위](./request-units.md))를 초과할 때 발생합니다. Spark 클러스터의 실행기 수와 대상 테이블에 기록되는 각 레코드의 크기(따라서 RU 비용)에 따라 이러한 설정을 조정해야 할 수도 있습니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="rate-limiting-429-error"></a>트래픽률 제한(429 오류)

설정을 최소값으로 줄인 경우에도 429 오류 코드 또는 "요청 빈도가 높습니다" 오류 텍스트가 표시될 수 있습니다. 다음 시나리오는 트래픽률 제한을 유발할 수 있습니다.

* **테이블에 할당된 처리량이 6,000개 [요청 단위](./request-units.md)보다 낮습니다**. 최소 설정에서도 Spark는 약 6,000개 요청 단위 이상의 속도로 쓸 수 있습니다. 처리량을 공유하는 키 공간에 테이블을 프로비저닝한 경우 이 테이블의 런타임에 사용 가능한 RU가 6,000개 미만일 수 있습니다.

    마이그레이션을 실행할 때 마이그레이션할 테이블에 최소 6,000개 RU를 사용할 수 있는지 확인합니다. 필요한 경우 해당 테이블에 전용 요청 단위를 할당합니다.

* **큰 데이터 볼륨으로 인한 과도한 데이터 기울이기**. 지정된 테이블로 마이그레이션할 많은 양의 데이터가 있지만 데이터에 상당한 기울이기가 있는 경우(즉, 동일한 파티션 키 값에 대해 많은 레코드가 기록됨) 테이블에 여러 [요청 단위](./request-units.md)가 프로비저닝된 경우에도 트래픽률 제한이 발생할 수 있습니다. 요청 단위는 실제 파티션 간에 균등하게 분할되며 과도한 데이터 기울이기로 인해 단일 파티션에 대한 요청 병목 현상이 발생할 수 있습니다.

    이 시나리오에서는 Spark에서 최소 처리량 설정으로 줄이고 마이그레이션을 느리게 실행합니다. 이 시나리오는 액세스 빈도가 낮고 기울이기가 높을 수 있는 참조 또는 제어 테이블을 마이그레이션할 때 더 일반적일 수 있습니다. 그러나 다른 유형의 테이블에 심각한 기울이기가 있는 경우 데이터 모델을 검토하여 정상 상태 작업 중에 워크로드에 대한 핫 파티션 문제를 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB Capacity Planner를 사용하여 RU/s 예측](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API의 탄력적 확장](manage-scale-cassandra.md)

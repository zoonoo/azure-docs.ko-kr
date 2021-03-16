---
title: Databricks (Spark)를 사용 하 여 Apache Cassandra에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션
description: Azure Databricks 및 Spark를 사용 하 여 Apache Cassandra 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caf9cbb0ca017ee00c5061d94e0d37703194943d
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573377"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Azure Databricks를 사용 하 여 Cassandra에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API는 다음과 같은 다양 한 이유로 Apache Cassandra에서 실행 되는 엔터프라이즈 워크 로드에 적합 한 선택이 되었습니다. 

* **관리 및 모니터링의 오버 헤드 없음:** OS, JVM, yaml 파일 및 상호 작용에서 많은 설정을 관리 하 고 모니터링 하는 오버 헤드를 제거 합니다.

* **상당한 비용 절감:** Azure Cosmos DB를 사용 하 여 비용을 절감할 수 있습니다. 여기에는 VM의 비용, 대역폭 및 모든 해당 라이선스를 포함 합니다. 또한 데이터 센터, 서버, SSD 저장소, 네트워킹 및 전기 비용을 관리할 필요가 없습니다. 

* **기존 코드 및 도구를 사용 하는 기능:** Azure Cosmos DB는 기존 Cassandra Sdk 및 도구와의 유선 프로토콜 수준 호환성을 제공 합니다. 이 호환성을 통해 간단한 변경 사항이 있는 Azure Cosmos DB Cassandra API와 함께 기존 코드베이스를 사용할 수 있습니다.

한 플랫폼에서 다른 플랫폼으로 데이터베이스 워크 로드를 마이그레이션하는 다양 한 방법이 있습니다. [Azure Databricks](https://azure.microsoft.com/services/databricks/) 는 대규모에서 오프 라인 마이그레이션을 수행 하는 방법을 제공 하는 [Apache Spark](https://spark.apache.org/) 에 대 한 서비스 제공 플랫폼입니다. 이 문서에서는 Azure Databricks를 사용 하 여 네이티브 Apache Cassandra keyspaces/tables에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 데 필요한 단계를 설명 합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Cosmos DB Cassandra API 계정 프로비전](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API 연결 관련 기본 사항 검토](cassandra-spark-generic.md)

* [Azure Cosmos DB Cassandra API에서 지원 되는 기능](cassandra-support.md) 을 검토 하 여 호환성을 확인 합니다.

* 대상 Azure Cosmos DB Cassandra API 계정에 빈 keyspace 및 테이블이 이미 만들어졌는지 확인 합니다.

* [유효성 검사에 cqlsh 또는 hosted shell 사용](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks 클러스터 프로비전

지침에 따라 [Azure Databricks 클러스터를 프로 비전](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)할 수 있습니다. Spark 3.0을 지 원하는 Databricks runtime 버전 7.5을 선택 하는 것이 좋습니다.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks 런타임":::


## <a name="add-dependencies"></a>종속성 추가

기본 및 Cosmos DB Cassandra 끝점에 연결 하려면 Apache Spark Cassandra 커넥터 라이브러리를 클러스터에 추가 해야 합니다. 클러스터에서 라이브러리를 선택 하 > > maven를 설치 합니다. `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`maven 좌표에 추가:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 검색 패키지":::

설치를 선택 하 고 설치가 완료 되 면 클러스터를 다시 시작 하는지 확인 합니다. 

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
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> 및에 대 한 값과 `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` Spark 클러스터의 작업자 수는 Azure Cosmos DB 요청이 프로 비전 된 처리량을 초과 하는 경우, 즉[요청 단위](./request-units.md)를 초과 하는 경우 발생 하는 [속도 제한을](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)방지 하기 위해 조정 해야 하는 중요 한 구성입니다. Spark 클러스터의 실행자 수에 따라 이러한 설정을 조정 해야 할 수 있으며, 대상 테이블에 기록 되는 각 레코드의 크기 (및 그에 따른 비용)를 줄일 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="rate-limiting-429-error"></a>전송률 제한 (429 오류)
`request rate is large`위의 설정이 최소값으로 감소 하더라도 오류 코드 429 또는 오류 텍스트가 표시 될 수 있습니다. 이러한 시나리오는 다음과 같습니다.

- **테이블에 할당 된 처리량이 6000 [요청 단위](./request-units.md)보다 낮습니다**. 최소 설정 에서도 Spark는 6000 요청 단위 이상으로 쓰기를 실행할 수 있습니다. 공유 처리량이 프로 비전 된 keyspace에 테이블을 프로 비전 한 경우 런타임에이 테이블에 6000의 RUs를 사용할 수 있습니다. 마이그레이션을 실행할 때 마이그레이션하는 테이블에 6000 RUs 이상이 제공 되는지 확인 하 고 필요한 경우 해당 테이블에 전용 요청 단위를 할당 합니다. 
- **대용량 데이터 볼륨이 포함 된 과도 한 데이터 왜곡**. 지정 된 테이블로 마이그레이션하는 데 많은 양의 데이터가 있는 경우 (즉, 동일한 파티션 키 값에 대해 기록 되는 레코드 수가 많은 경우) 테이블에 너무 많은 양의 [요청 단위가](./request-units.md) 포함 된 경우에도 계속 해 서 요율 제한을 경험할 수 있는 것입니다. 이는 요청 단위가 실제 파티션 간에 균등 하 게 분할 되 고, 많은 데이터 오차는 단일 파티션에 대 한 요청의 병목 현상이 발생 하 여 요율 제한을 발생 시킬 수 있기 때문입니다. 이 시나리오에서는 속도 제한을 피하고 마이그레이션을 느리게 실행 하도록 Spark에서 최소 처리량 설정을 줄이는 것이 좋습니다. 이 시나리오는 참조 또는 컨트롤 테이블을 마이그레이션하는 경우 보다 일반적 일 수 있습니다 .이 경우 액세스 빈도가 낮지만 기울이기가 높을 수 있습니다. 그러나 다른 유형의 테이블에 상당한 기울이기를 있으면 데이터 모델을 검토 하 여 안정적인 상태 작업 중에 작업에 대 한 핫 파티션 문제를 방지 하는 것이 좋습니다. 



## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API의 탄력적인 확장](manage-scale-cassandra.md)

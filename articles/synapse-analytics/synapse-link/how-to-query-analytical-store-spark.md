---
title: Apache Spark를 사용하여 Azure Cosmos DB Analytical Store(미리 보기) 쿼리
description: Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Cosmos DB 분석을 쿼리하는 방법
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee04c922e4373414dc27ed2b7c98be605280e5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089110"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Cosmos DB Analytical Store(미리 보기) 쿼리

이 문서에서는 Synapse 제스처에서 분석 저장소와 상호 작용하는 방법에 대한 예를 제공합니다. 컨테이너를 마우스 오른쪽 단추로 클릭하면 제스처가 표시됩니다. 제스처를 사용하면 코드를 빠르게 생성하고 필요에 맞게 조정할 수 있습니다. 제스처는 한 번의 클릭으로 데이터를 검색하는 데에도 적합합니다.

## <a name="load-to-dataframe"></a>DataFrame에 로드

이 단계에서는 Spark DataFrame의 Azure Cosmos DB 분석 저장소에서 데이터를 읽습니다. ***df***라는 DataFrame에서 10개의 행을 표시합니다. 데이터가 데이터 프레임에 포함되면 추가 분석을 수행할 수 있습니다.

이 작업은 트랜잭션 저장소에 영향을 주지 않습니다.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

**Scala**의 해당 코드 제스처는 다음 코드입니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Spark 테이블 만들기

이 제스처에서는 선택한 컨테이너를 가리키는 Spark 테이블을 만듭니다. 해당 작업에는 데이터 이동이 발생하지 않습니다. 해당 테이블을 삭제하기로 결정한 경우 기본 컨테이너(및 해당 분석 저장소)는 영향을 받지 않습니다. 

이 시나리오는 타사 도구를 통해 테이블을 다시 사용하고 런타임 데이터에 대한 액세스 가능성을 제공하는 데 편리합니다.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>컨테이너에 DataFrame 쓰기

이 제스처에서는 컨테이너에 데이터 프레임을 작성합니다. 이 작업은 트랜잭션 성능에 영향을 주므로 요청 단위를 사용합니다. Azure Cosmos DB 트랜잭션 성능을 사용하는 것은 쓰기 트랜잭션에 적합합니다. **YOURDATAFRAME**을 다시 쓰려는 데이터 프레임으로 바꾸어야 합니다.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala**의 해당 코드 제스처는 다음 코드입니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>컨테이너에서 스트리밍 DataFrame 로드
이 제스처에서는 Spark Streaming 기능을 사용하여 컨테이너에서 데이터 프레임으로 데이터를 로드합니다. 데이터는 작업 영역에 연결된 기본 데이터 레이크 계정(및 파일 시스템)에 저장됩니다. 

*/localReadCheckpointFolder* 폴더가 생성되지 않으면 자동으로 생성됩니다. 이 작업은 Azure Cosmos DB의 트랜잭션 성능에 영향을 줍니다.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala**의 해당 코드 제스처는 다음 코드입니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>컨테이너에 스트리밍 DataFrame 쓰기
이 제스처에서는 선택한 Azure Cosmos DB 컨테이너에 스트리밍 데이터 프레임을 작성합니다. */localReadCheckpointFolder* 폴더가 생성되지 않으면 자동으로 생성됩니다. 이 작업은 Azure Cosmos DB의 트랜잭션 성능에 영향을 줍니다.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala**의 해당 코드 제스처는 다음 코드입니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>다음 단계

* [Synapse와 Azure Cosmos DB 간에 지원되는 항목 알아보기](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB용 Synapse Link에 연결](../quickstart-connect-synapse-link-cosmos-db.md)

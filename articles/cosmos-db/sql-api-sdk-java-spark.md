---
title: SQL API 릴리스 정보 및 리소스에 대 한 Cosmos DB Apache Spark 커넥터
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 Azure Cosmos DB Apache Spark 커넥터에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: afee95f6a8776c3506e10c29cfd8e776734a915a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326680"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API에 대 한 Azure Cosmos DB Apache Spark 커넥터: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [스프링 데이터 v2](sql-api-sdk-java-spring-v2.md)
> * [스프링 데이터 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](/rest/api/cosmos-db/)
> * [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Core (SQL)에 대 한 Azure Cosmos DB Apache Spark Connector를 사용 하 여 빅 데이터 분석을 가속화할 수 있습니다. Spark 커넥터를 사용 하면 Azure Cosmos DB에 저장 된 데이터에서 [spark](https://spark.apache.org/) 작업을 실행할 수 있습니다. 일괄 처리 및 스트림 처리가 지원 됩니다.

Azure에서 관리 되는 Spark 클러스터를 제공 하는 [Azure Databricks](https://azure.microsoft.com/services/databricks) 또는 [azure HDInsight](https://azure.microsoft.com/services/hdinsight/)에서 커넥터를 사용할 수 있습니다. 다음 표에서는 지원 되는 버전을 보여 줍니다.

| 구성 요소 | 버전 |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x*및 2.1. *x* |
| Scala | 2.11 |
| Azure Databricks (런타임 버전) | 3.4 이후 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 코어 (SQL) API를 지원 합니다.
> MongoDB에 대 한 Cosmos DB API의 경우 [Spark에 MongoDB 커넥터](https://docs.mongodb.com/spark-connector/master/)를 사용 합니다.
> Cosmos DB Cassandra API의 경우 [Cassandra Spark 커넥터](https://github.com/datastax/spark-cassandra-connector)를 사용 합니다.
>

## <a name="resources"></a>리소스

| 리소스 | 링크 |
|---|---|
| **SDK 다운로드** | [최신 jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) 다운로드 |
|**API 설명서** | [Spark 커넥터 참조]() |
|**SDK에 기여** | [GitHub의 Apache Spark에 대 한 Azure Cosmos DB 커넥터](https://github.com/Azure/azure-cosmosdb-spark) | 
|**시작** | [Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Apache Kafka 및 Azure Cosmos DB에서 Apache Spark 정형 스트림 사용](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>릴리스 기록

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>새로운 기능
- 새 구성 옵션인를 추가 합니다 .이 옵션을 `changefeedstartfromdatetime` 사용 하 여 플랫폼인 처리 되어야 하는 시작 시간을 지정할 수 있습니다. 자세한 내용은 [Config options](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)를 참조 하세요.

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>핵심 버그 수정
- 큰 결과 집합 (예: 수백만 개의 행)에 대해 실행자에서 과도 한 메모리 사용을 야기 하는 회귀를 수정 하 여 결국 오류가 발생 `java.lang.OutOfMemoryError: GC overhead limit exceeded` 합니다.

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 에 `ID` 구성이 적용 된 파이프 문자 (|)가 포함 된 스트리밍 검사점에 지 사례를 수정 `ChangeFeedMaxPagesPerBatch` 합니다.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>새로운 기능
* 중첩 된 파티션 키를 사용 하는 경우 대량 업데이트에 대 한 지원을 추가 합니다.
* Azure Cosmos DB에 쓰는 동안 Decimal 및 Float 데이터 형식에 대 한 지원을 추가 합니다.
* Long (Unix epoch)를 값으로 사용 하는 경우 타임 스탬프 형식에 대 한 지원을 추가 합니다.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 구성을 사용할 때 발생 하는 형식 캐스팅 예외를 수정 `WriteThroughputBudget` 합니다.

### <a name="307"></a>3.0.7 이상을
#### <a name="new-features"></a>새로운 기능
* 대량 실패에 대 한 오류 정보를 예외 및 로그에 추가 합니다.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 스트리밍 검사점 문제를 수정 합니다.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 노이즈를 줄이기 위해는 실수로 수준 오류가 발생 한 메시지의 로그 수준을 수정 합니다.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 파티션을 분할 하는 동안 구조적 스트리밍에서 버그를 수정 합니다. 버그는 일부 누락 된 변경 피드 레코드 또는 검사점 쓰기에 대 한 Null 예외를 발생 시킬 수 있습니다.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* ReadStream에 대해 제공 된 사용자 지정 스키마를 무시 하는 버그를 수정 합니다.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 빌드 시간을 50% 늘리는 재발 (unshaded JAR에 모든 음영 처리 된 종속성 포함)을 수정 합니다.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* TCP를 통한 직접 전송이 RequestTimeoutException을 사용 하 여 실패 하는 종속성 문제를 해결 합니다.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>새로운 기능
* 연결 관리 및 연결 풀링을 개선 하 여 메타 데이터 호출의 수를 줄입니다.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[Apache Spark](https://spark.apache.org/)에 대해 자세히 알아봅니다.

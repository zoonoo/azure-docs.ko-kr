---
title: SQL API 용 스프링 데이터 Azure Cosmos DB v3 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 스프링 데이터 Azure Cosmos DB에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817873"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Core 용 스프링 데이터 Azure Cosmos DB v3 (SQL) API: 릴리스 정보 및 리소스
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

Core (SQL)에 대 한 스프링 데이터 Azure Cosmos DB 버전 3을 통해 개발자는 스프링 응용 프로그램에서 Azure Cosmos DB를 사용할 수 있습니다. 스프링 데이터 Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작 하 고, 문서 작업을 수행 하 고, 쿼리를 실행 하기 위한 스프링 데이터 인터페이스를 제공 합니다. 동기화 및 비동기 (대응식) Api는 모두 동일한 Maven 아티팩트에서 지원 됩니다. 

스프링 데이터 Azure Cosmos DB는 스프링 데이터 프레임 워크에 대 한 종속성이 있습니다. Azure Cosmos DB SDK 팀은 스프링 데이터 버전 2.2 및 2.3에 대 한 Maven 아티팩트를 릴리스 합니다.

[스프링 프레임 워크](https://spring.io/projects/spring-framework) 는 Java 응용 프로그램 개발을 간소화 하는 프로그래밍 및 구성 모델입니다. 스프링은 종속성 주입을 사용 하 여 응용 프로그램의 "배관"을 간소화 합니다. 응용 프로그램을 보다 간단 하 게 빌드하고 테스트할 수 있기 때문에 많은 개발자 들이 스프링을 좋아합니다. [스프링 부팅](https://spring.io/projects/spring-boot) 은 웹 응용 프로그램 및 마이크로 서비스 개발에 대 한 눈에 맞추어 이러한 통로의 처리를 확장 합니다. [스프링 데이터](https://spring.io/projects/spring-data) 는 스프링 또는 스프링 부팅 응용 프로그램의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스 하기 위한 프로그래밍 모델 및 프레임 워크입니다. 

[Azure 스프링 클라우드](https://azure.microsoft.com/services/spring-cloud/) 응용 프로그램에서 스프링 데이터 Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 스프링 데이터 Azure Cosmos DB 버전 3에 대 한 것입니다. [버전 2에 대 한 릴리스 정보는 여기](sql-api-sdk-java-spring-v2.md)에서 확인할 수 있습니다. 
>
> 스프링 데이터 Azure Cosmos DB는 SQL API만 지원 합니다.
>
> 다른 Azure Cosmos DB Api의 스프링 데이터에 대 한 자세한 내용은 다음 문서를 참조 하세요.
> * [Azure Cosmos DB에서 Apache Cassandra에 대 한 스프링 데이터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>여기에서 시작

# <a name="explore"></a>[탐색](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>이러한 탭에는 기본 스프링 데이터 Azure Cosmos DB 샘플이 포함 되어 있습니다.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>종속성 구성

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[연결](#tab/connect)

### <a name="connect"></a>연결

Azure Cosmos DB 계정 및 컨테이너 세부 정보를 지정 합니다. 스프링 데이터 Azure Cosmos DB 자동으로 클라이언트를 만들고 컨테이너에 연결 합니다.

[응용 프로그램. 속성](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc ops](#tab/docs)

### <a name="document-operations"></a>문서 작업

만들기:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

삭제:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[쿼리](#tab/queries)

쿼리:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>리소스

* **SDK에 기여**: [GitHub의 스프링 데이터 Azure Cosmos DB 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **자습서**: [GitHub의 스프링 데이터 Azure Cosmos DB 자습서](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>릴리스 기록

### <a name="300-beta2-september-17-2020"></a>3.0.0-beta. 2 (2020 년 9 월 17 일)

#### <a name="new-features"></a>새 기능

* 아티팩트 id를로 업데이트 했습니다 `azure-spring-data-cosmos` .
* Azure cosmos 종속성을로 업데이트 했습니다 `4.5.0` .
* `Query Annotation` 네이티브 쿼리를 지원 합니다.
* Java 11 지원.
* 주석에서 필드를 노출 하 여 중첩 된 파티션 키에 대 한 지원이 추가 되었습니다 `partitionKeyPath` `@Container` .
* `limit` `top` `first` 리포지토리 api를 정의할 때 및를 사용할 수 있도록 하는 쿼리 형식에 대 한 지원이 추가 되었습니다.

#### <a name="key-bug-fixes"></a>핵심 버그 수정

* 주석과 함께 사용 될 경우 중첩 된 파티션 키 버그를 수정 `@GeneratedValue` 했습니다.

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta. 1 (2020 년 8 월 17 일)

#### <a name="new-features"></a>새 기능

* 그룹 ID를로 업데이트 `com.azure` 합니다.
* 아티팩트 ID를로 업데이트 `azure-spring-data-2-3-cosmos` 합니다.
* Azure cosmos SDK 종속성을로 업데이트 `4.3.2-beta.2` 합니다.
* 에는 `createdBy` ,, `createdDate` `lastModifiedBy` 및 주석이 추가 된 `lastModifiedDate` 필드의 자동 관리를 위한 감사 엔터티에 대 한 지원이 추가 되었습니다.
* `@GeneratedValue`유형의 id 필드에 대 한 자동 id 생성에 대 한 주석 지원을 추가 `String` 합니다.
* 여러 데이터베이스가 포함 된 단일 Azure Cosmos DB 계정에 대 한 다중 데이터베이스 구성 지원 및 여러 데이터베이스를 사용 하는 여러 개의 Azure Cosmos DB 계정 추가
* 문자열 필드에 대 한 주석에 대 한 지원을 추가 `@Version` 합니다.
* 동기화 API 반환 형식을 `Iterable` 대신 형식으로 업데이트 `List` 합니다.
* `CosmosClientBuilder`AZURE COSMOS DB SDK에서 스프링 bean로 클래스에 노출 `@Configuration` 합니다.
* `CosmosConfig`쿼리 메트릭 및 응답 진단 프로세서 구현을 포함 하도록 업데이트 되었습니다.
* `Optional`단일 결과 쿼리에 대 한 데이터 형식을 반환 하기 위한 지원을 추가 합니다.

#### <a name="renames"></a>바꾸기

* `CosmosDbFactory` to `CosmosFactory` .
* `CosmosDBConfig` to `CosmosConfig` .
* `CosmosDBAccessException` to `CosmosAccessException` .
* `Document` 주석에 대 한 주석 `Container` 입니다.
* `DocumentIndexingPolicy` 주석에 대 한 주석 `CosmosIndexingPolicy` 입니다.
* `DocumentQuery` to `CosmosQuery` .
* application. 속성은 `populateQueryMetrics` 에 플래그를 지정 `queryMetricsEnabled` 합니다.

#### <a name="key-bug-fixes"></a>핵심 버그 수정

* `Parallel`Netty i/o 스레드를 차단 하지 않도록 하기 위해 스레드에 진단 로깅 작업을 예약 하는 중입니다.
* 삭제 작업에 대 한 낙관적 잠금을 수정 합니다.
* 절의 이스케이프 쿼리와 관련 된 문제를 해결 `IN` 합니다.
* 의 데이터 형식을 허용 하 여 문제 `long` 를 해결 `@Id` 합니다.
* ,, 및를 `boolean` `long` `int` `double` 주석의 데이터 형식으로 허용 하 여 문제를 해결 `@PartitionKey` 합니다.
* `IgnoreCase` `AllIgnoreCase` 대/소문자 쿼리를 무시 하는에 대 한 수정 및 키워드입니다.
* 컨테이너를 자동으로 만들 때 4000의 기본 요청 단위 값을 제거 합니다.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[스프링 프레임 워크](https://spring.io/projects/spring-framework)에 대해 자세히 알아보세요.

[스프링 부팅](https://spring.io/projects/spring-boot)에 대해 자세히 알아보세요.

[스프링 데이터](https://spring.io/projects/spring-data)에 대해 자세히 알아보세요.
---
title: SQL API 릴리스 정보 및 리소스에 대 한 스프링 데이터 Azure Cosmos DB v2
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 스프링 데이터 Azure Cosmos DB에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d5a906e12df14c6c3ffec704310bbede742ad841
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760828"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API에 대 한 스프링 데이터 Azure Cosmos DB v2: 릴리스 정보 및 리소스
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

 Core (SQL) 용 스프링 데이터 Azure Cosmos DB 버전 2를 통해 개발자는 스프링 응용 프로그램에서 Azure Cosmos DB를 사용할 수 있습니다. 스프링 데이터 Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작 하 고, 문서 작업을 수행 하 고, 쿼리를 실행 하기 위한 스프링 데이터 인터페이스를 제공 합니다. 동기화 및 비동기 (대응식) Api는 모두 동일한 Maven 아티팩트에서 지원 됩니다. 

[스프링 프레임 워크](https://spring.io/projects/spring-framework) 는 Java 응용 프로그램 개발을 간소화 하는 프로그래밍 및 구성 모델입니다. 스프링은 종속성 주입을 사용 하 여 응용 프로그램의 "배관"을 간소화 합니다. 응용 프로그램을 보다 간단 하 게 빌드하고 테스트할 수 있기 때문에 많은 개발자 들이 스프링을 좋아합니다. [스프링 부팅](https://spring.io/projects/spring-boot) 은 웹 응용 프로그램 및 마이크로 서비스 개발에 대 한 눈에 맞추어 이러한 통로의 처리를 확장 합니다. [스프링 데이터](https://spring.io/projects/spring-data) 는 스프링 또는 스프링 부팅 응용 프로그램의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스 하기 위한 프로그래밍 모델입니다. 

[Azure 스프링 클라우드](https://azure.microsoft.com/services/spring-cloud/) 응용 프로그램에서 스프링 데이터 Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 스프링 데이터 Azure Cosmos DB 버전 2에 대 한 것입니다. [버전 3에 대 한 릴리스 정보는 여기](sql-api-sdk-java-spring-v3.md)에서 확인할 수 있습니다. 
>
> 스프링 데이터 Azure Cosmos DB는 SQL API만 지원 합니다.
>
> 다른 Azure Cosmos DB Api의 스프링 데이터에 대 한 자세한 내용은 다음 문서를 참조 하세요.
> * [Azure Cosmos DB에서 Apache Cassandra에 대 한 스프링 데이터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 신속 하 게 진행 하 고 싶으세요?
> 1. SDK를 사용할 수 있도록 [지원 되는 최소 Java RUNTIME JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)을 설치 합니다.
> 2. [스타터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)를 사용 하 여 스프링 데이터 Azure Cosmos DB 앱을 만듭니다. 간단합니다.
> 3. 기본 Azure Cosmos DB 요청을 안내 하는 [스프링 데이터 Azure Cosmos DB 개발자 가이드를 참조](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)하세요.
>
> 스프링 [Inr](https://start.spring.io/)을 사용 하 여 스프링 부팅 시작 앱을 빠르게 회전할 수 있습니다.
>

## <a name="resources"></a>리소스

| 리소스 | 링크 |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 설명서** | [스프링 데이터 Azure Cosmos DB 참조 설명서]() |
|**SDK에 기여** | [GitHub의 스프링 데이터 Azure Cosmos DB 리포지토리](https://github.com/microsoft/spring-data-cosmosdb) | 
|**스프링 부팅 스타터**| [Java 용 Azure Cosmos DB 스프링 부팅 스타터 클라이언트 라이브러리](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure Cosmos DB를 사용 하는 스프링 TODO 앱 샘플**| [App Service Linux에서 종단 간 Java 환경 (2 부)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**개발자 가이드** | [Spring Data Azure Cosmos DB 개발자 가이드](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**스타터 사용** | [Azure Cosmos DB SQL API에서 스프링 부팅 스타터를 사용 하는 방법](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure Cosmos DB 스프링 부팅 스타터 용 GitHub 리포지토리](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure App Service 사용 예제** | [Azure App Service on Linux를 통해 Spring 및 Cosmos DB를 사용하는 방법](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 앱 샘플](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>릴리스 기록

### <a name="230-may-21-2020"></a>2.3.0 (2020 년 5 월 21 일)
#### <a name="new-features"></a>새로운 기능
* 스프링 부팅 버전을 2.3.0로 업데이트 합니다.


### <a name="225-may-19-2020"></a>2.2.5 (2020 년 5 월 19 일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB 버전을 3.7.3로 업데이트 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* Azure Cosmos DB SDK 3.7.3의 메모리 누수 수정과 Netty 버전 업그레이드를 포함 합니다.

### <a name="224-april-6-2020"></a>2.2.4 (4 월 6 일, 2020)
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* `allowTelemetry`에서 고려할 플래그를 수정 `CosmosDbConfig` 합니다.
* `TTL`컨테이너의 속성을 수정 합니다.

### <a name="223-february-25-2020"></a>2.2.3 (2 월 25 일, 2020)
#### <a name="new-features"></a>새로운 기능
* `findAll`파티션 키 API를 기준으로 새를 추가 합니다.
* Azure Cosmos DB 버전을 3.7.0로 업데이트 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 수정 `collectionName`  ->  `containerName` .
* `entityClass`및를 수정 `domainClass`  ->  `domainType` 합니다.
* "입력 엔터티 대신 리포지토리에 저장 된 반환 엔터티 컬렉션"을 수정 합니다.

### <a name="2110-february-25-2020"></a>2.1.10 (2 월 25 일, 2020)
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* Backports는 "입력 엔터티 대신 리포지토리에 저장 된 반환 엔터티 컬렉션"을 수정 합니다.

### <a name="222-january-15-2020"></a>2.2.2 (1 월 15 일 2020)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB 버전을 3.6.0로 업데이트 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="221-december-31-2019"></a>2.2.1 (2019 년 12 월 31 일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB SDK 버전을 3.5.0로 업데이트 합니다.
* 자동 컬렉션 만들기를 사용 하거나 사용 하지 않도록 주석 필드를 추가 합니다.
* 예외 처리를 향상 시킵니다. `CosmosClientException`를 통해 노출 `CosmosDBAccessException` 합니다.
* `requestCharge`및를 `activityId` 통해를 노출 `ResponseDiagnostics` 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* SDK 3.5.0 업데이트는 Cosmos DB HTTP 응답 헤더가 8192 바이트 보다 크면 "" ConsistencyPolicy defaultConsistencyLevel ()이 (가) 제한 된 부실 접두사에서 실패 합니다. "라는 오류를 수정 합니다.
* `findById`메서드의 동작을 수정 합니다. 이전에는이 메서드가 예외를 throw 하는 대신 엔터티를 찾지 못한 경우 빈 결과를 반환 했습니다.
* 가 사용 된 경우 다음 페이지에 정렬이 적용 되지 않는 버그를 수정 `CosmosPageRequest` 합니다.

### <a name="219-december-26-2019"></a>2.1.9 (2019 년 12 월 26 일)
#### <a name="new-features"></a>새로운 기능
* 자동 컬렉션 만들기를 사용 하거나 사용 하지 않도록 주석 필드를 추가 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
*  `findById`메서드의 동작을 수정 합니다. 이전에는이 메서드가 예외를 throw 하는 대신 엔터티를 찾지 못한 경우 빈 결과를 반환 했습니다.

### <a name="220-october-21-2019"></a>2.2.0 (2019 년 10 월 21 일)
#### <a name="new-features"></a>새로운 기능
* 사후 Cosmos 리포지토리 지원을 완료 합니다.
* Azure Cosmos DB 요청 진단 문자열 및 쿼리 메트릭을 지원 합니다.
* 3.3.1에 SDK 버전 업데이트를 Azure Cosmos DB 합니다.
* 스프링 프레임 워크 버전을 5.2.0로 업그레이드 합니다.
* 스프링 데이터 Commons 버전을 2.2.0로 업그레이드 합니다.
* `findByIdAndPartitionKey`및 api를 추가 `deleteByIdAndPartitionKey` 합니다.
* Azure documentdb에서 종속성을 제거 합니다.
* Azure Cosmos DB에 대 한 rebrands DocumentDB.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "PageSize가 리포지토리의 총 항목 보다 작은 경우 정렬 시 예외가 throw 됩니다."를 수정 합니다.

### <a name="218-october-18-2019"></a>2.1.8 (2019 년 10 월 18 일)
#### <a name="new-features"></a>새로운 기능
* DocumentDB Api를 지지.
* `findByIdAndPartitionKey`및 api를 추가 `deleteByIdAndPartitionKey` 합니다.
* 에 따라 낙관적 잠금을 추가 `_etag` 합니다.
* 문서 컬렉션 이름에 대해 SpEL expression을 사용 하도록 설정 합니다.
* `ObjectMapper`향상 된 기능을 추가 합니다.

### <a name="217-october-18-2019"></a>2.1.7 (2019 년 10 월 18 일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB SDK 버전 3 종속성을 추가 합니다.
* 사후 Cosmos 리포지토리를 추가 합니다.
* `DocumentDbTemplate`AZURE COSMOS DB SDK 버전 3을 사용 하도록의 구현을 업데이트 합니다.
* 사후 Cosmos 리포지토리 지원에 대 한 다른 구성 변경 사항을 추가 합니다.

### <a name="212-march-19-2019"></a>2.1.2 (3 월 19 일, 2019)
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* `applicationInsights`다음에 대 한 종속성을 제거 합니다.
    * 종속성이 polluting 잠재적 위험이 있습니다.
    * Java 11 비호환.
    * CPU 및/또는 메모리에 대 한 잠재적 성능 저하를 방지 합니다.

### <a name="207-march-20-2019"></a>2.0.7 (3 월 20 일, 2019)
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* Backport `applicationInsights` 는 다음에 대 한 종속성을 제거 합니다.
    * 종속성이 polluting 잠재적 위험이 있습니다.
    * Java 11 비호환.
    * CPU 및/또는 메모리에 대 한 잠재적 성능 저하를 방지 합니다.

### <a name="211-march-7-2019"></a>2.1.1 (3 월 7 일, 2019)
#### <a name="new-features"></a>새로운 기능
* 주 버전을 2.1.1로 업데이트 합니다.

### <a name="206-march-7-2019"></a>2.0.6 (3 월 7 일, 2019)
#### <a name="new-features"></a>새로운 기능
* 원격 분석에서 모든 예외를 무시 합니다.

### <a name="210-december-17-2018"></a>2.1.0 (2018 년 12 월 17 일)
#### <a name="new-features"></a>새로운 기능
* 2.1.0에 버전을 업데이트 하 여 문제를 해결 합니다.

### <a name="205-september-13-2018"></a>2.0.5 (9 월 13 일, 2018)
#### <a name="new-features"></a>새로운 기능
* 및 키워드 `exists` 를 추가 `startsWith` 합니다.
* 추가 정보를 업데이트 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "엔터티에 대해 직접 자체 href를 호출할 수 없습니다."를 수정 합니다.
* "수집이 생성 되지 않으면 findAll이 실패 합니다."를 수정 합니다.

### <a name="204-prerelease-august-23-2018"></a>2.0.4 이상을 (시험판) (2018 년 8 월 23 일)
#### <a name="new-features"></a>새로운 기능
* Documentdb에서 cosmosdb로 패키지의 이름을 바꿉니다.
* 쿼리 메서드 키워드의 새 기능을 추가 합니다. 이제 SQL API에서 16 개의 키워드가 지원 됩니다.
* 페이징 및 정렬을 사용 하 여 쿼리의 새 기능을 추가 합니다.
* 스프링 데이터 cosmosdb의 구성을 간소화 합니다.
* `deleteCollection`및 api를 추가 `deleteAll` 합니다.

#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 버그 수정 및 결함 완화.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[스프링 프레임 워크](https://spring.io/projects/spring-framework)에 대해 자세히 알아보세요.

[스프링 부팅](https://spring.io/projects/spring-boot)에 대해 자세히 알아보세요.

[스프링 데이터](https://spring.io/projects/spring-data)에 대해 자세히 알아보세요.
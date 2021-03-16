---
title: SQL API 용 스프링 데이터 Azure Cosmos DB v3 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 스프링 데이터 Azure Cosmos DB에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 3c740aa00b158c7ddbca3e4f61d79e37978223c2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493614"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Core 용 스프링 데이터 Azure Cosmos DB v3 (SQL) API: 릴리스 정보 및 리소스
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](/rest/api/cosmos-db/)
> * [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [대량 실행기 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Core (SQL)에 대 한 스프링 데이터 Azure Cosmos DB 버전 3을 통해 개발자는 스프링 응용 프로그램에서 Azure Cosmos DB를 사용할 수 있습니다. 스프링 데이터 Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작 하 고, 문서 작업을 수행 하 고, 쿼리를 실행 하기 위한 스프링 데이터 인터페이스를 제공 합니다. 동기화 및 비동기 (대응식) Api는 모두 동일한 Maven 아티팩트에서 지원 됩니다. 

> [!IMPORTANT]
> 스프링 데이터 Azure Cosmos DB는 스프링 데이터 프레임 워크에 대 한 종속성이 있습니다.
> 
> 3.0.0에서 3.4.0 cosmos 버전의 azure-스프링 데이터 버전 2.2 및 2.3을 지원 합니다.
> 
> azure-cosmos 버전 3.5.0 이상에서는 스프링 데이터 버전 2.4.3 이상을 지원 합니다.
>

[스프링 프레임 워크](https://spring.io/projects/spring-framework) 는 Java 응용 프로그램 개발을 간소화 하는 프로그래밍 및 구성 모델입니다. 스프링은 종속성 주입을 사용 하 여 응용 프로그램의 "배관"을 간소화 합니다. 응용 프로그램을 보다 간단 하 게 빌드하고 테스트할 수 있기 때문에 많은 개발자 들이 스프링을 좋아합니다. [스프링 부팅](https://spring.io/projects/spring-boot) 은 웹 응용 프로그램 및 마이크로 서비스 개발에 대 한 눈에 맞추어 이러한 통로의 처리를 확장 합니다. [스프링 데이터](https://spring.io/projects/spring-data) 는 스프링 또는 스프링 부팅 응용 프로그램의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스 하기 위한 프로그래밍 모델 및 프레임 워크입니다. 

[Azure 스프링 클라우드](https://azure.microsoft.com/services/spring-cloud/) 응용 프로그램에서 스프링 데이터 Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Spring Data Azure Cosmos DB 버전 3에 대한 것입니다. [여기에서 버전 2의 릴리스 정보](sql-api-sdk-java-spring-v2.md)를 확인할 수 있습니다. 
>
> Spring Data Azure Cosmos DB는 SQL API만 지원합니다.
>
> 다른 Azure Cosmos DB API의 Spring Data에 대한 정보는 다음 문서를 참조하세요.
> * [Azure Cosmos DB의 Spring Data for Apache Cassandra](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB의 Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB의 Spring Data Gremlin](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>빠른 시작

  [스프링 부팅 시작 가이드](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)에 따라 스프링 데이터 Azure Cosmos DB을 시작 하 고 실행 합니다. 스프링 부팅 스타터 접근 방식은 스프링 데이터 Azure Cosmos DB 커넥터를 사용 하 여 시작 하는 데 권장 되는 방법입니다.

  또는 아래와 같이 스프링 데이터 Azure Cosmos DB 종속성을 파일에 추가할 수 있습니다 `pom.xml` .

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>유용한 내용

| Content | 링크 |
|---|---|
|**SDK 다운로드**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API 설명서** | [Java API 참조 설명서](/java/api/com.azure.spring.data.cosmos) |
|**SDK에 참여** | [GitHub의 Java용 Azure SDK 중앙 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**시작** | [빠른 시작: Azure Cosmos DB SQL API 데이터를 관리 하는 스프링 데이터 Azure Cosmos DB 앱 빌드](./create-sql-api-spring-data.md) <br> [빠른 시작 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**기본 코드 샘플** | [Azure Cosmos DB: SQL API에 대 한 스프링 데이터 Azure Cosmos DB 예제](sql-api-spring-data-sdk-samples.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **성능 팁**| [Java SDK v4에 대 한 성능 팁 (스프링 데이터에 적용 가능)](performance-tips-java-sdk-v4-sql.md)| 
| **문제 해결** | [Java SDK v4 문제 해결 (스프링 데이터에 적용 가능)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB 워크샵 및 랩** |[Cosmos DB 워크샵 홈페이지](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>추가적인 참고 사항

* 스프링 데이터 Azure Cosmos DB는 Java JDK 8 및 Java JDK 11을 지원 합니다.
* 스프링 데이터 2.3은 현재 지원 되지 않습니다. 현재 스프링 데이터 2.4은 지원 되지 않습니다.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[스프링 프레임 워크](https://spring.io/projects/spring-framework)에 대해 자세히 알아보세요.

[스프링 부팅](https://spring.io/projects/spring-boot)에 대해 자세히 알아보세요.

[스프링 데이터](https://spring.io/projects/spring-data)에 대해 자세히 알아보세요.
---
title: Azure Cosmos DB Java SDK v4 for SQL API 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Java SDK v4 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 686c1e37e093a358bb5122fbfeccdfd98eb301ce
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804234"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core(SQL) API: 릴리스 정보 및 리소스
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
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core(SQL)는 비동기 API와 동기 API를 하나의 Maven 아티팩트로 결합합니다. V4 SDK는 Project Reactor 및 [Netty 라이브러리](https://netty.io/)를 기준으로 향상된 성능, 새 API 기능 및 비동기 지원을 제공합니다. Azure Cosmos DB Java SDK v4와 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 및 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)를 사용하여 성능을 향상시킬 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Azure Cosmos DB Java SDK v4에만 해당됩니다. 현재 v4보다 이전 버전을 사용하는 경우 v4로 업그레이드하는 데 도움이 필요하면 [Azure Cosmos DB Java SDK v4로 마이그레이션](migrate-java-v4-sdk.md) 가이드를 참조하세요.
>
> 빠른 시작을 위한 세 가지 단계는 다음과 같습니다.
> 1. SDK를 사용할 수 있도록 [지원되는 최소 Java 런타임 JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)을 설치합니다.
> 2. Maven 아티팩트에 대한 액세스 권한을 가져오고 기본 Azure Cosmos DB 요청을 안내하는 [Azure Cosmos DB Java SDK v4에 대한 빠른 시작 가이드](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)를 사용합니다.
> 3. 애플리케이션에 대한 SDK를 최적화하는 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md) 및 [문제 해결](troubleshoot-java-sdk-v4-sql.md) 가이드를 읽어 보세요.
>
> [Azure Cosmos DB 워크샵 및 랩](https://aka.ms/cosmosworkshop)은 Azure Cosmos DB Java SDK v4 사용 방법을 배우는 데 유용한 또 다른 리소스입니다.
>

## <a name="helpful-content"></a>유용한 내용

| Content | 링크 |
|---|---|
|**SDK 다운로드**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 설명서** | [Java API 참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable&preserve-view=true) |
|**SDK에 참여** | [GitHub의 Java용 Azure SDK 중앙 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**시작** | [빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Java 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [빠른 시작 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**기본 코드 샘플** | [Azure Cosmos DB: SQL API에 대한 Java 예제](sql-api-java-sdk-samples.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**변경 피드를 사용하는 콘솔 앱**| [변경 피드-Java SDK v4 샘플](create-sql-api-java-changefeed.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**웹앱 샘플**| [Java SDK v4를 사용 하 여 웹 앱 빌드](sql-api-java-application.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **성능 팁**| [Java SDK v4용 성능 팁](performance-tips-java-sdk-v4-sql.md)| 
| **문제 해결** | [Java SDK v4 문제 해결](troubleshoot-java-sdk-v4-sql.md) |
| **이전 SDK에서 v4로 마이그레이션** | [Java V4 SDK로 마이그레이션](migrate-java-v4-sdk.md) |
| **지원되는 최소 런타임**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Azure Cosmos DB 워크샵 및 랩** |[Cosmos DB 워크샵 홈페이지](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>다음 단계
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.
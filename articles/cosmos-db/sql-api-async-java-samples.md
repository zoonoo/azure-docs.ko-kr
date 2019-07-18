---
title: Azure Cosmos DB는 SQL API에 대한 비동기 Java 예제
description: CRUD 작업을 포함하여 Azure Cosmos DB SQL API를 사용하는 일반적인 작업에 대한 비동기 Java 예제를 GitHub에서 찾아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 12/03/2018
ms.author: sngun
ms.openlocfilehash: 46858affe44d488a9ade1693713072fded18afed
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475797"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB는 SQL API에 대한 비동기 Java 예제

> [!div class="op_single_selector"]
> * [.NET 예제](sql-api-dotnet-samples.md)
> * [Java 예제](sql-api-java-samples.md)
> * [비동기 Java 예제](sql-api-async-java-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB 리소스에 대한 CRUD 작업 및 다른 일반적인 작업을 수행하는 최신 애플리케이션 예제는 [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 예제 비동기 Java 프로젝트 파일에서 작업에 연결합니다. 
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

이 애플리케이션 예제를 실행하려면 다음이 필요합니다.

* Java Development Kit 8
* Microsoft Azure Cosmos DB Java SDK

필요에 따라 Maven을 사용하여 프로젝트에서 사용할 최신 Microsoft Azure Cosmos DB Java SDK 이진 파일을 가져올 수 있습니다. 최신 버전은 [여기](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)에서 선택할 수 있습니다. Maven은 필요한 종속성을 자동으로 추가합니다. 자동으로 추가하지 않을 경우 pom.xml 파일에 나열된 종속성을 직접 다운로드하여 빌드 경로에 추가할 수 있습니다.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**애플리케이션 예제 실행**

샘플 리포지토리 복제:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

Eclipse를 사용하거나 Maven을 사용하여 명령줄에서 샘플을 실행할 수 있습니다.

Eclipse에서 실행하려면
* Eclipse에서 기본 부모 프로젝트 pom.xml 파일을 로드합니다. azure-cosmosdb-examples가 자동으로 로드됩니다.
* 샘플을 실행하려면 유효한 Azure Cosmos DB 엔드포인트가 필요합니다. 엔드포인트는 `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`에서 읽어옵니다.
* Eclipse JUnit 구성 실행에서 엔드포인트 자격 증명을 VM 인수로 전달하거나, 엔드포인트 자격 증명을 TestConfigurations.java에 배치할 수 있습니다.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* 이제 Eclipse에서 JUnit 테스트로 샘플을 실행할 수 있습니다.

명령줄에서 실행하려면
* 샘플을 실행하는 다른 방법은 Maven을 사용하는 것입니다.
* Maven을 실행하고 Azure Cosmos DB 엔드포인트 자격 증명을 전달합니다.
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > 각 샘플은 자체 포함되므로 자체 설정된 후 자체 정리됩니다. 샘플은 [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컬렉션의 성능 계층에 대해 1시간 사용량이 청구됩니다. 
   > 
   > 

## <a name="database-examples"></a>데이터베이스 예제
[DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 데이터베이스에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [이미 존재하는 데이터베이스를 만드는 데 실패](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [데이터베이스 만들기 및 읽기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [데이터베이스 만들기 및 삭제](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [데이터베이스 만들기 및 쿼리](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>컬렉션 예제
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 컬렉션에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [단일 파티션 컬렉션 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [사용자 지정 다중 파티션 컬렉션 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [이미 존재하는 컬렉션을 만드는 데 실패](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [컬렉션 만들기 및 읽기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [컬렉션 만들기 및 삭제](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [컬렉션 만들기 및 쿼리](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>문서 예제
[DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 문서에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [문서 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [프로그래밍 가능한 문서 정의가 포함된 문서 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [문서](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [문서 만들기 및 총 RU 비용 찾기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [이미 존재하는 문서를 만드는 데 실패](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [문서 만들기 및 바꾸기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [문서 만들기 및 upsert](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [문서 만들기 및 삭제](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [문서 만들기 및 읽기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>청구 예제
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다.  다음 샘플을 실행하기 전에 Azure Cosmos DB에서 인덱싱에 대해 자세히 알아보려면 [인덱싱 정책](index-policy.md), [인덱싱 유형](index-types.md) 및 [인덱싱 경로](index-paths.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [인덱스 만들기 및 인덱싱 정책 설정](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

인덱싱에 대한 자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="query-examples"></a>쿼리 예제
[DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 SQL 쿼리 참조에 대해 자세히 알아보려면 [SQL 쿼리 예제](how-to-sql-query.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [간단한 문서 쿼리 수행](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [간단한 문서 쿼리 수행 및 총 RU 비용 찾기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [간단한 문서 쿼리 실행, 한 페이지 읽기 및 반환된 식별 가능한 구독 취소](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [간단한 문서 쿼리 수행 및 결과 필터링](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [order-by 교차 파티션 문서 쿼리 수행](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

쿼리를 작성하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 내에서 SQL 쿼리](how-to-sql-query.md)를 참조하세요.

## <a name="offer-examples"></a>제안 예제
[OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다.

| Task | API 참조 |
| --- | --- |
| [컬렉션 만들기 및 처리량 설정](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [컬렉션을 읽어 관련된 제안 찾기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [해당 제품을 대체하여 컬렉션의 처리량 업데이트](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>저장 프로시저 예제
[StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 서버 측 프로그래밍에 대해 자세히 알아보려면 [저장 프로시저, 트리거 및 사용자 정의 함수](stored-procedures-triggers-udfs.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [저장 프로시저 만들기 및 실행](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [인수를 사용하여 저장 프로시저 만들기 및 실행](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [개체 인수를 사용하여 저장 프로시저 만들기 및 실행](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>고유 키
[UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) 파일은 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 고유 키에 대해 자세히 알아보려면 [고유 키 제약 조건](unique-keys.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [고유 키를 사용하여 컬렉션 만들기](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |

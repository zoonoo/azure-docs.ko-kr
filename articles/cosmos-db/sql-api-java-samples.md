---
title: Azure Cosmos DB는 SQL API에 대한 Java 예제
description: CRUD 작업을 포함하여 Azure Cosmos DB SQL API를 사용하는 일반적인 작업에 대한 Java 예제를 GitHub에서 찾아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 075ddf2df5c8bd5c6eed04911be1f4a20faf6921
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475768"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB는 SQL API에 대한 Java 예제

> [!div class="op_single_selector"]
> * [.NET 예제](sql-api-dotnet-samples.md)
> * [Java 예제](sql-api-java-samples.md)
> * [비동기 Java 예제](sql-api-async-java-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB 리소스에 대한 CRUD 작업 및 다른 일반적인 작업을 수행하는 최신 애플리케이션 예제는 [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 예제 Java 프로젝트 파일에서 작업에 연결합니다. 
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

이 애플리케이션 예제를 실행하려면 다음이 필요합니다.

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

필요에 따라 Maven을 사용하여 프로젝트에서 사용할 최신 Microsoft Azure DocumentDB Java SDK 이진을 가져올 수 있습니다. Maven은 필요한 종속성을 자동으로 추가합니다. 자동으로 추가하지 않을 경우 pom.xml 파일에 나열된 종속성을 직접 다운로드하여 빌드 경로에 추가할 수 있습니다.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**애플리케이션 예제 실행**

샘플 리포지토리 복제:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Eclipse를 사용하거나 Maven을 사용하여 명령줄에서 샘플을 실행할 수 있습니다.

Eclipse에서 실행하려면
* Eclipse에서 기본 부모 프로젝트 pom.xml 파일을 로드합니다. documentdb-examples가 자동으로 로드됩니다.
* 샘플을 실행하려면 유효한 Azure Cosmos DB 엔드포인트가 필요합니다. 엔드포인트는 `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`에서 읽어옵니다.
* Eclipse JUnit 구성 실행에서 엔드포인트 자격 증명을 VM 인수로 전달하거나, 엔드포인트 자격 증명을 AccountCredentials.java에 넣을 수 있습니다.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* 이제 Eclipse에서 JUnit 테스트로 샘플을 실행할 수 있습니다.

명령줄에서 실행하려면
* 샘플을 실행하는 다른 방법은 Maven을 사용하는 것입니다.
* Maven을 실행하고 Azure Cosmos DB 엔드포인트 자격 증명을 전달합니다.
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > 각 샘플은 자체 포함되므로 자체 설정된 후 자체 정리됩니다. 샘플은 [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컬렉션의 성능 계층에 대해 1시간 사용량이 청구됩니다. 
   > 
   > 

## <a name="database-examples"></a>데이터베이스 예제
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 데이터베이스에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기 및 읽기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [데이터베이스 만들기 및 삭제](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [데이터베이스 만들기 및 쿼리](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>컬렉션 예제
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 컬렉션에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [단일 파티션 컬렉션 만들기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [사용자 지정 다중 파티션 컬렉션 만들기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [컬렉션 삭제](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>문서 예제
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 문서에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [문서 만들기, 읽기 및 삭제](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [프로그래밍 가능한 문서 정의가 포함된 문서 만들기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [문서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>청구 예제
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB에서 인덱싱에 대해 자세히 알아보려면 [인덱싱 정책](index-policy.md), [인덱싱 유형](index-types.md) 및 [인덱싱 경로](index-paths.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [인덱스 만들기 및 인덱싱 정책 설정](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

인덱싱에 대한 자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="query-examples"></a>쿼리 예제
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 SQL 쿼리 참조에 대해 자세히 알아보려면 [SQL 쿼리 예제](how-to-sql-query.md) 개념 문서를 참조하세요. 


| Task | API 참조 |
| --- | --- |
| [단순한 교차 파티션 문서 쿼리 수행](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [쿼리별 정렬](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

쿼리를 작성하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 내에서 SQL 쿼리](how-to-sql-query.md)를 참조하세요.

## <a name="offer-examples"></a>제안 예제
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [컬렉션 만들기 및 처리량 설정](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [컬렉션을 읽어 관련된 제안 찾기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>파티션 키 예제
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 분할 및 파티션 키에 대해 자세히 알아보려면 [분할](partitioning-overview.md) 개념 문서를 참조하세요. 


| Task | API 참조 |
| --- | --- |
| [단일 파티션 컬렉션 만들기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [단일 파티션 컬렉션에 대한 처리량 제안 변경](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>저장 프로시저 예제
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 서버 측 프로그래밍에 대해 자세히 알아보려면 [저장 프로시저, 트리거 및 사용자 정의 함수](stored-procedures-triggers-udfs.md) 개념 문서를 참조하세요. 


| Task | API 참조 |
| --- | --- |
| [저장 프로시저 만들기](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [인수를 사용하여 저장 프로시저 실행](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [개체 인수를 사용하여 저장 프로시저 실행](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |

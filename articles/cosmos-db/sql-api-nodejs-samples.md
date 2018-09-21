---
title: Azure Cosmos DB에 대한 Node.js 예제 | Microsoft Docs
description: CRUD 작업을 비롯한 Azure Cosmos DB의 일반적인 작업에 대한 github의 Node.js 예제를 찾습니다.
keywords: Node.js 예제
services: cosmos-db
author: moderakh
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: sample
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: 0abe743f9afceea936a55a13d7004a3094ddb5b3
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575272"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js 예제
> [!div class="op_single_selector"]
> * [.NET 예제](sql-api-dotnet-samples.md)
> * [Java 예제](sql-api-java-samples.md)
> * [비동기 Java 예제](sql-api-async-java-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Node.js 예제 - v2.0 미리 보기](sql-api-nodejs-samples-preview.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB 리소스에 대해 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 Node.js 예제 프로젝트 파일에서 작업에 연결합니다.
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있음: Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

또한 [Node.js SDK](sql-api-sdk-node.md)가 필요합니다.
   
   > [!NOTE]
   > 각 샘플은 자체 포함되며 자체를 설정하고 자체를 정리합니다. 샘플은 [DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-
)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컬렉션의 성능 계층 당 1시간 사용량이 청구됩니다.
   > 
   > 

## <a name="database-examples"></a>데이터베이스 예제
[DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdatabase-body--options--callback-) |
| [데이터베이스에 대한 계정 쿼리](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydatabases-query--options-) |
| [ID에서 데이터베이스 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabase-databaselink--options--callback-) |
| [계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabases-options-) |
| [데이터베이스 삭제](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedatabase-databaselink--options--callback-) |

## <a name="collection-examples"></a>컬렉션 예제
[CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [컬렉션 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [데이터베이스에서 모든 컬렉션의 목록 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollections-databaselink--options-) |
| [_self에서 컬렉션 가져오기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [ID에서 컬렉션 가져오기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [컬렉션의 성능 계층 가져오기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#queryoffers-query--options-) |
| [컬렉션의 성능 계층 변경](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replaceoffer-offerlink--offer--callback-) |
| [컬렉션 삭제](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletecollection-collectionlink--options--callback-) |

## <a name="document-examples"></a>문서 예제
[DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [문서 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdocument-documentsfeedordatabaselink--body--options--callback-) |
| [컬렉션에 대한 문서 피드 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [ID로 문서 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [문서가 변경된 경우에만 문서 읽기](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [문서에 대한 쿼리](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydocuments-documentsfeedordatabaselink--query--options-) |
| [문서 바꾸기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-) |
| [조건부 ETag 검사로 문서 바꾸기](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [문서 삭제](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedocument-documentlink--options--callback-) |

## <a name="indexing-examples"></a>청구 예제
[IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [기본 인덱싱을 사용하여 컬렉션 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [수동으로 특정 문서 인덱스](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [수동으로 특정 문서를 인덱스에서 제외](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [대량 가져오기에 대한 지연 인덱싱 사용 또는 많은 컬렉션 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.indexingmode?view=azure-dotnet) |
| [인덱싱에 문서의 특정 경로 포함](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_IncludedPaths) |
| [인덱싱에서 특정 경로 제외](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_ExcludedPaths) |
| [범위 작업 중 문자열 경로에서 검색 허용](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_EnableScanInQuery) |
| [문자열 경로에 범위 인덱스 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.indexkind?view=azure-dotnet#Microsoft_Azure_Documents_IndexKind_Range), [IndexingPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.indexingpolicy?view=azure-dotnet), [DocumentClient.queryDocument](https://docs.microsoft.com/javascript/api/documentdb/documentclient?view=azure-node-latest) |
| [기본 indexPolicy로 컬렉션을 만든 다음 이 온라인 업데이트](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-)<br> [DocumentClient.replaceCollection#replaceCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacecollection-collectionlink--collection--options--callback-) |

인덱싱에 대한 자세한 내용은 [Azure Cosmos DB 인덱싱 정책](indexing-policies.md)을 참조하세요.

## <a name="server-side-programming-examples"></a>서버 쪽 프로그래밍 예제
[ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [저장 프로시저 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createstoredprocedure-collectionlink--sproc--options--callback-) |
| [저장 프로시저 실행](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#executestoredprocedure-sproclink--params--options--callback-) |

서버 쪽 프로그래밍에 대한 자세한 내용은 [Azure Cosmos DB 서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF](programming.md)를 참조하세요.

## <a name="partitioning-examples"></a>분할 예제
[Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [HashPartitionResolver 사용](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](https://docs.microsoft.com/javascript/api/documentdb/HashPartitionResolver?view=azure-node-latest) |

Azure Cosmos DB에서 데이터를 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 데이터 분할 및 크기 조정](partition-data.md)을 참조하세요.


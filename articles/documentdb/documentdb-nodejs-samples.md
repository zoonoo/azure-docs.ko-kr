<properties 
	pageTitle="DocumentDB용 NoSQL Node.js 예제 | Microsoft Azure" 
	description="NoSQL 데이터베이스에서 JSON 문서에 대한 CRUD 작업을 비롯한 DocumentDB의 일반적인 작업에 대한 github의 NoSQL Node.js 예제를 찾습니다." 
	keywords="node.js 예제"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="mimig"/>


# DocumentDB Node.js 예제

> [AZURE.SELECTOR]
- [.NET Examples](documentdb-dotnet-samples.md)
- [Node.js Examples](documentdb-nodejs-samples.md)
- [Azure Code Sample Gallery](https://azure.microsoft.com/documentation/samples/?service=documentdb)

DocumentDB 리소스에 대한 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub 리포지토리에 포함됩니다. 이 문서는 다음을 제공합니다.

- 각 Node.js 예제 프로젝트 파일에서 작업에 연결합니다. 
- 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

1. 이러한 Node.js 예제를 사용하려면 Azure 계정이 필요합니다.
    - [Azure 계정을 무료로 개설](https://azure.microsoft.com/pricing/free-trial/)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.
   - [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)할 수 있음: Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
2. 또한 [Node.js SDK](documentdb-sdk-node.md)가 필요합니다. 

    > [AZURE.NOTE] 각 샘플은 자체 포함되며 자체를 설정하고 자체를 정리합니다. 샘플은 [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컬렉션의 성능 계층 당 1시간 사용량이 청구됩니다.

## 데이터베이스 예제

[DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) 프로젝트의 [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) 파일에서는 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[데이터베이스 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[데이터베이스에 대한 계정 쿼리](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[ID에서 데이터베이스 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[데이터베이스 삭제](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## 컬렉션 예제 

[DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) 프로젝트의 [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) 파일에서는 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[컬렉션 만들기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[데이터베이스에서 모든 컬렉션의 목록 읽기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[\_self]에서 컬렉션 가져오기(https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [ID에서 컬렉션 가져오기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [컬렉션의 성능 계층 가져오기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) [컬렉션의 성능 계층 변경](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) [컬렉션 삭제](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## 문서 예제

[DocumentDB.Samples.DocumentManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.DocumentManagement) 프로젝트의 [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.DocumentManagement/app.js) 파일에서는 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[문서 만들기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[컬렉션에 대한 문서 피드 읽기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[ID로 문서 읽기](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[문서에 대한 쿼리](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[문서 업데이트](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[문서 삭제](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## 청구 예제

[DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.IndexManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.IndexManagement/app.js) 파일에서는 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
기본 인덱싱을 사용하여 컬렉션 만들기 | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[수동으로 특정 문서 인덱스](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[수동으로 특정 문서를 인덱스에서 제외](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[대량 가져오기에 대한 지연 인덱싱 사용 또는 많은 컬렉션 읽기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[인덱싱에 문서의 특정 경로 포함](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[인덱싱에서 특정 경로 제외](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[범위 작업 중 문자열 경로에서 검색 허용](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[문자열 경로에 범위 인덱스 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[기본 indexPolicy로 컬렉션을 만든 다음 이 온라인 업데이트](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

인덱싱에 대한 자세한 내용은 [DocumentDB 인덱싱 정책](documentdb-indexing-policies.md)을 참조하세요.

## 서버 쪽 프로그래밍 예제

[DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.ServerSideScripts) 프로젝트의 [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.ServerSideScripts/app.js) 파일에서는 다음 작업을 수행하는 방법을 보여 줍니다.

작업 | API 참조
--- | ---
[저장 프로시저 만들기](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[저장 프로시저 실행](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

서버 쪽 프로그래밍에 대한 자세한 내용은 [DocumentDB 서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF](documentdb-programming.md)를 참조하세요.

<!---HONumber=AcomDC_0128_2016-->
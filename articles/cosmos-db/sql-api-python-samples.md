---
title: Azure Cosmos DB에 대한 SQL API Python 예제
description: CRUD 작업을 비롯한 Azure Cosmos DB의 일반적인 작업에 대한 GitHub의 Python 예제를 찾습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: cf296d8bb494307dbb58b9de522d55a83892c6d5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537323"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 예제

> [!div class="op_single_selector"]
> * [.NET 예제](sql-api-dotnet-samples.md)
> * [Java 예제](sql-api-java-samples.md)
> * [비동기 Java 예제](sql-api-async-java-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB 리소스에 대해 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 Python 예제 프로젝트 파일에서 작업에 연결합니다. 
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

또한 [Python SDK](sql-api-sdk-python.md)가 필요합니다. 
   
   > [!NOTE]
   > 각 샘플은 자체 포함되므로 자체 설정된 후 자체 정리됩니다. 샘플은 [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-)에 대한 여러 호출을 실행합니다. 이 작업이 완료될 때마다 구독에 1시간 사용 요금이 청구됩니다. Azure Cosmos DB 청구에 대한 자세한 내용은 [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)을 참조하세요.
   > 
   > 

## <a name="database-examples"></a>데이터베이스 예제
[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 데이터베이스에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createdatabase-database--options-none-) |
| [ID별 데이터베이스 읽기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabase-database-link--options-none-) |
| [계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabases-options-none-) |
| [데이터베이스 삭제](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>컬렉션 예제
[CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 컬렉션에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [컬렉션 만들기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-) |
| [데이터베이스에서 모든 컬렉션의 목록 읽기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainers-database-link--options-none-) |
| [ID별 컬렉션 가져오기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainer-collection-link--options-none-) |
| [컬렉션의 처리량 변경](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceoffer-offer-link--offer-)|
| [컬렉션 삭제](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>문서 예제
[DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 문서에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [문서 만들기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [문서 컬렉션 만들기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [ID로 문서 읽기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitem-document-link--options-none-) |
| [컬렉션의 모든 문서 읽기](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitems-collection-link--feed-options-none-) |
| [조건부 ETag 검사로 문서 바꾸기](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>청구 예제
[IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) 프로젝트의 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.  다음 샘플을 실행하기 전에 Azure Cosmos DB에서 인덱싱에 대해 자세히 알아보려면 [인덱싱 정책](index-policy.md), [인덱싱 유형](index-types.md) 및 [인덱싱 경로](index-paths.md) 개념 문서를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [(자동 대신) 수동 인덱싱 사용](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | 자동 인덱싱 정책 |
| [인덱스에서 지정된 문서 경로 제외](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | 제외된 경로를 사용하는 인덱싱 정책|
| [인덱스에서 문서 제외](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [인덱싱 모드 설정](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [문자열에서 범위 인덱스 사용](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | 포함된 경로를 사용하는 인덱싱 정책|
| [인덱스 변환 수행](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>쿼리 예제
샘플 프로젝트는 다음 쿼리 작업을 수행하는 방법도 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 SQL 쿼리 참조에 대해 자세히 알아보려면 [SQL 쿼리 예제](how-to-sql-query.md) 개념 문서를 참조하세요. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 SQL 쿼리 참조에 대해 자세히 알아보려면 [SQL 쿼리 예제](how-to-sql-query.md) 개념 문서를 참조하세요. 


| Task | API 참조 |
| --- | --- |
| [데이터베이스에 대한 계정 쿼리](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#querydatabases-query--options-none-) |
| [문서에 대한 쿼리](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [해시 인덱스된 경로에 범위 검색 강제](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |


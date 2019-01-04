---
title: Azure Cosmos DB에 대한 Node.js 예제
description: CRUD 작업을 비롯한 Azure Cosmos DB의 일반적인 작업에 대한 GitHub의 Node.js 예제를 찾습니다.
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.topic: sample
ms.date: 09/24/2018
ms.author: deborahc
ms.openlocfilehash: 491e31fecedf52be0ecf2aee470fab4ce168c866
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538449"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js 예제
> [!div class="op_single_selector"]
> * [.NET 예제](sql-api-dotnet-samples.md)
> * [Java 예제](sql-api-java-samples.md)
> * [비동기 Java 예제](sql-api-async-java-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB 리소스에 대해 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 Node.js 예제 프로젝트 파일에서 작업에 연결합니다.
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

**필수 구성 요소**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[JavaScript SDK](sql-api-sdk-node.md)도 필요합니다.
   
   > [!NOTE]
   > 각 샘플은 자체 포함되며 자체를 설정하고 자체를 정리합니다. 따라서 샘플은 [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create)에 대한 여러 호출을 실행합니다. 구독에 이렇게 영향을 줄 때마다 생성되는 컨테이너의 성능 계층당 1시간 사용에 대한 요금이 청구됩니다.
   > 
   > 

## <a name="database-examples"></a>데이터베이스 예제
[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [데이터베이스가 없는 경우 새로 만들기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#createifnotexists) |
| [계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#readall) |
| [ID에서 데이터베이스 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#read) |
| [데이터베이스 삭제](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#delete) |

## <a name="container-examples"></a>컨테이너 예제
[ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여줍니다.

| Task | API 참조 |
| --- | --- |
| [컨테이너가 없는 경우 새로 만들기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#createifnotexists) |
| [계정의 컨테이너 나열](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#readall) |
| [ID별로 컬렉션 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#read) |
| [컨테이너 삭제](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#delete) |

## <a name="item-examples"></a>항목 예제
[ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [항목 만들기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#create) |
| [컨테이너의 모든 항목 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#readall) |
| [ID별로 항목 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read) |
| [항목이 변경된 경우에만 항목 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [문서에 대한 쿼리](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [항목 바꾸기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace) |
| [항목을 조건부 ETag 검사로 바꾸기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [항목 삭제](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#delete) |

## <a name="indexing-examples"></a>청구 예제
[IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) 프로젝트의 [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [수동으로 특정 문서 인덱스](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [수동으로 특정 문서를 인덱스에서 제외](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [대량 가져오기에 대한 지연 인덱싱 사용 또는 대량 컨테이너 읽기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [인덱스에서 경로 제외](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [범위 작업 중 문자열 경로에서 검색 허용](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [문자열 경로에 범위 인덱스 만들기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [기본 indexPolicy로 컨테이너를 만든 다음, 온라인으로 업데이트](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create)


인덱싱에 대한 자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="server-side-programming-examples"></a>서버 쪽 프로그래밍 예제
[ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) 프로젝트의 [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) 파일은 다음 작업을 수행하는 방법을 보여 줍니다.

| Task | API 참조 |
| --- | --- |
| [저장 프로시저 만들기](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [저장 프로시저 실행](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest#execute) |

서버 쪽 프로그래밍에 대한 자세한 내용은 [Azure Cosmos DB 서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF](stored-procedures-triggers-udfs.md)를 참조하세요.


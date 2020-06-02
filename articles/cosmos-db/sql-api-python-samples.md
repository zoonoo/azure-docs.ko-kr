---
title: Azure Cosmos DB에 대한 SQL API Python 예제
description: CRUD 작업을 비롯한 Azure Cosmos DB의 일반적인 작업에 대한 GitHub의 Python 예제를 찾습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.openlocfilehash: 3b92ae206aa623e948a612f31b48213421bf9da3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798505"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 예제

> [!div class="op_single_selector"]
> * [.NET V2 SDK 예제](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 예제](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 예제](sql-api-java-sdk-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Azure Cosmos DB 리소스에 대해 CRUD 작업 및 다른 일반적인 작업을 수행하는 샘플 솔루션은 [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 리포지토리에 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 Python 예제 프로젝트 파일에서 작업에 연결합니다.
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

또한 [Python SDK](sql-api-sdk-python.md)가 필요합니다.

   > [!NOTE]
   > 각 샘플은 자체 포함되므로 자체 설정된 후 자체 정리됩니다. 샘플은 `CosmosClient.CreateContainer`에 대한 여러 호출을 실행합니다. 이 작업이 완료될 때마다 구독에 1시간 사용 요금이 청구됩니다. Azure Cosmos DB 청구에 대한 자세한 내용은 [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)을 참조하세요.

## <a name="database-examples"></a>데이터베이스 예제

[database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python 샘플에서는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 데이터베이스에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [ID별 데이터베이스 읽기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [데이터베이스 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [계정에 대한 데이터베이스 나열](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [데이터베이스 삭제](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>컨테이너 예제

[container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python 샘플에서는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 컬렉션에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [컨테이너에 대한 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [컨테이너 만들기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [데이터베이스의 모든 컨테이너 나열](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [해당 ID로 컨테이너 가져오기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [컨테이너의 프로비저닝된 처리량 관리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [컨테이너 삭제](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>항목 예제

[item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python 샘플에서는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 문서에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [컨테이너에서 항목 만들기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [해당 ID별로 항목 읽기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [컨테이너의 모든 항목 읽기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [해당 ID별로 항목 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [항목 바꾸기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [항목 Upsert](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [항목 삭제](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [컨테이너에 있는 항목의 변경 피드 가져오기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>청구 예제

[index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python 샘플에서는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB에서 인덱싱에 대해 자세히 알아보려면 [인덱싱 정책](index-policy.md), [인덱싱 유형](index-types.md) 및 [인덱싱 경로](index-paths.md) 개념 문서를 참조하세요.

| Task | API 참조 |
| --- | --- |
| [특정 항목을 인덱싱에서 제외](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [인덱싱된 특정 항목으로 수동 인덱싱 사용](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [인덱싱에서 경로 제외](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |`IndexingPolicy` 속성에서 제외할 경로 정의 |
| [문자열에서 범위 인덱스 사용](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | 문자열 데이터 형식의 범위 인덱스를 사용하여 인덱싱 정책을 정의합니다. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [인덱스 변환 수행](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container(업데이트된 인덱싱 정책 사용)|
| [경로에 해시 인덱스만 있는 경우 검색 사용](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | 항목을 쿼리할 때 `enable_scan_in_query=True` 및 `enable_cross_partition_query=True` 설정 |
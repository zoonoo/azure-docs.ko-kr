---
title: Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB을 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053230"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿에 대 한 링크가 포함 되어 있습니다.

## <a name="sql-core-api"></a>SQL(코어) API

|**템플릿**|**설명**|
|---|---|
|[Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에 SQL (Core) API 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 있습니다. |
|[저장 프로시저, 트리거 및 UDF를 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿에서는 컨테이너에 대 한 저장 프로시저, 트리거 및 UDF를 사용 하 여 두 지역에 SQL (Core) API 계정을 만듭니다. |
|[데이터베이스의 업데이트 처리량 (r u/초)](manage-sql-with-resource-manager.md#database-ru-update) | 이 템플릿은 SQL (Core) API 계정에서 데이터베이스에 대 한 처리량을 업데이트 합니다. |
|[컨테이너의 업데이트 처리량 (r u/초)](manage-sql-with-resource-manager.md#container-ru-update) | 이 템플릿은 SQL (Core) API 계정의 컨테이너에 대 한 처리량을 업데이트 합니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에서 MongoDB 용 Azure Cosmos DB API를 사용 하 여 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 있습니다. |
|[데이터베이스의 업데이트 처리량 (r u/초)](manage-mongodb-with-resource-manager.md#database-ru-update) | 이 템플릿은 MongoDB API 계정에서 데이터베이스에 대 한 처리량을 업데이트 합니다. |
|[컬렉션에 대 한 업데이트 처리량 (r u/초)](manage-mongodb-with-resource-manager.md#collection-ru-update) | 이 템플릿은 MongoDB API 계정의 컨테이너에 대 한 처리량을 업데이트 합니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, keyspace, table 만들기](manage-cassandra-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Cassandra API 계정을 만듭니다. Azure Cosmos 계정에는 keyspace 수준 처리량을 공유 하는 두 개의 테이블이 포함 됩니다. |
|[Keyspace에 대 한 업데이트 처리량 (r u/초)](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 이 템플릿은 Cassandra API 계정에서 keyspace의 처리량을 업데이트 합니다. |
|[테이블에 대 한 업데이트 처리량 (r u/초)](manage-cassandra-with-resource-manager.md#table-ru-update) | 이 템플릿은 Cassandra API 계정에서 테이블에 대 한 처리량을 업데이트 합니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에 Gremlin API 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 그래프가 있습니다. |
|[데이터베이스의 업데이트 처리량 (r u/초)](manage-gremlin-with-resource-manager.md#database-ru-update) | 이 템플릿은 Gremlin API 계정에서 데이터베이스에 대 한 처리량을 업데이트 합니다. |
|[그래프의 업데이트 처리량 (r u/초)](manage-gremlin-with-resource-manager.md#graph-ru-update) | 이 템플릿은 Gremlin API 계정에서 그래프의 처리량을 업데이트 합니다. |

## <a name="table-api"></a>Table API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, 테이블을 만듭니다.](manage-table-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Table API 계정을 만듭니다. Azure Cosmos 계정에는 단일 테이블이 포함 됩니다. |
|[테이블에 대 한 업데이트 처리량 (r u/초)](manage-table-with-resource-manager.md#table-ru-update) | 이 템플릿은 Table API 계정에서 테이블에 대 한 처리량을 업데이트 합니다. |

> [!TIP]
> Table API를 사용할 때 공유 처리량을 사용 하도록 설정 하려면 Azure Portal에서 계정 수준 처리량을 사용 하도록 설정 합니다.

참조 설명서는 [Azure Cosmos DB 페이지에 대 한 ARM 참조](/azure/templates/microsoft.documentdb/allversions) 를 참조 하세요.

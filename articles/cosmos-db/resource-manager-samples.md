---
title: Azure Cosmos DB에 대 한 azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿 만들기 및 Azure Cosmos DB를 구성 하려면 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969170"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 azure Resource Manager 템플릿

Azure Cosmos DB 용 Azure Resource Manager 템플릿 링크를 포함 하는 다음 표에:

## <a name="sql-core-api"></a>SQL(Core) API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-resource) | 이 템플릿은 두 개의 지역에서 사용 하도록 설정 하는 다중 마스터를 사용 하 여 (코어) SQL API 계정을 만듭니다. 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 Azure Cosmos 계정이 됩니다. |
|[데이터베이스에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-sql-with-resource-manager.md#database-ru-update) | 이 서식 파일 (코어) SQL API 계정에 데이터베이스에 대 한 처리량을 업데이트합니다. |
|[컨테이너에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-sql-with-resource-manager.md#container-ru-update) | 이 서식 파일 (코어) SQL API 계정에서 컨테이너에 대 한 처리량을 업데이트합니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-resource) | 이 템플릿은 두 개의 지역에서 MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 사용 하도록 설정 하는 다중 마스터를 사용 하 여 계정을 만듭니다. 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 Azure Cosmos 계정이 됩니다. |
|[데이터베이스에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-mongodb-with-resource-manager.md#database-ru-update) | 이 템플릿은 MongoDB API 계정에 데이터베이스에 대 한 처리량을 업데이트합니다. |
|[컬렉션에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-mongodb-with-resource-manager.md#collection-ru-update) | 이 템플릿은 MongoDB API 계정에서 컨테이너에 대 한 처리량을 업데이트합니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, keyspace, 테이블 만들기](manage-cassandra-with-resource-manager.md#create-resource) | 이 템플릿을 사용 하도록 설정 하는 다중 마스터를 사용 하 여 두 지역에 Cassandra API 계정을 만듭니다. Azure Cosmos 계정 keyspace 수준 처리량을 공유 하는 두 테이블 해야 합니다. |
|[업데이트를 keyspace에 대 한 처리량 (RU/s)](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 이 템플릿은 Cassandra API 계정의 keyspace에 대 한 처리량을 업데이트합니다. |
|[테이블에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-cassandra-with-resource-manager.md#table-ru-update) | 이 템플릿은 Cassandra API 계정에서 테이블에 대 한 처리량을 업데이트합니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-resource) | 이 템플릿은 사용 하도록 설정 하는 다중 마스터를 사용 하 여 두 지역에는 Gremlin API 계정을 만듭니다. 두 그래프 데이터베이스 수준 처리량을 공유 하는 Azure Cosmos 계정이 됩니다. |
|[데이터베이스에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-gremlin-with-resource-manager.md#database-ru-update) | 이 템플릿에 Gremlin API 계정에 데이터베이스에 대 한 처리량을 업데이트합니다. |
|[그래프에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-gremlin-with-resource-manager.md#graph-ru-update) | 이 템플릿에 Gremlin API 계정에서 그래프에 대 한 처리량을 업데이트합니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정 만들기, 테이블](manage-table-with-resource-manager.md#create-resource) | 이 템플릿은 두 개의 지역에서 사용 하도록 설정 하는 다중 마스터를 사용 하 여 Table API 계정을 만듭니다. Azure Cosmos 계정에는 단일 테이블을 해야 합니다. |
|[테이블에 대 한 처리량 (RU/s)를 업데이트 합니다.](manage-table-with-resource-manager.md#table-ru-update) | 이 템플릿은 Table API 계정에서 테이블에 대 한 처리량을 업데이트합니다. |

> [!TIP]
> 공유 처리량을 Table API를 사용 하는 경우 사용 하도록 설정 하려면 Azure Portal에서 계정 수준 처리량을 사용 하도록 설정 합니다.

참조 [Azure Cosmos DB에 대 한 ARM 참조](/azure/templates/microsoft.documentdb/allversions) 참조 설명서 페이지.
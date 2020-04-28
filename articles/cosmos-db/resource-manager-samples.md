---
title: Azure Cosmos DB용 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB을 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193261"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿에 대 한 링크가 포함 되어 있습니다.

## <a name="sql-core-api"></a>SQL(Core) API

|**템플릿**|**설명**|
|---|---|
|[Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-resource) | 이 템플릿은 공유 데이터베이스 처리량이 있는 두 개의 컨테이너와 전용 처리량을 가진 컨테이너를 사용 하 여 두 지역에 SQL (Core) API 계정을 만듭니다. 업데이트 된 처리량 속성 값으로 템플릿을 다시 전송 하 여 처리량을 업데이트할 수 있습니다. |
|[저장 프로시저, 트리거 및 UDF를 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿에서는 컨테이너에 대 한 저장 프로시저, 트리거 및 UDF를 사용 하 여 두 지역에 SQL (Core) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정에 대 한 개인 끝점 만들기](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에 있는 기존 Azure Cosmos SQL API 계정에 대 한 개인 끝점을 만듭니다. |
|[무료 계층 Azure Cosmos 계정 만들기](manage-sql-with-resource-manager.md#free-tier) |  이 템플릿은 무료 계층에 Azure Cosmos DB 계정을 만듭니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에서 MongoDB 용 Azure Cosmos DB API를 사용 하 여 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 있습니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, keyspace, table 만들기](manage-cassandra-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Cassandra API 계정을 만듭니다. Azure Cosmos 계정에는 keyspace 수준 처리량을 공유 하는 두 개의 테이블이 포함 됩니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에 Gremlin API 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 그래프가 있습니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, 테이블을 만듭니다.](manage-table-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Table API 계정을 만듭니다. Azure Cosmos 계정에는 단일 테이블이 포함 됩니다. |

> [!TIP]
> Table API를 사용할 때 공유 처리량을 사용 하도록 설정 하려면 Azure Portal에서 계정 수준 처리량을 사용 하도록 설정 합니다.

참조 설명서는 [Azure Cosmos DB 페이지 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 를 참조 하세요.

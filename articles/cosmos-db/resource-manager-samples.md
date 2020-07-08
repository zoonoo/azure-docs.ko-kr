---
title: Azure Cosmos DB용 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586153"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB용 Azure Resource Manager 템플릿에 대한 링크가 포함되어 있습니다.

## <a name="core-sql-api"></a>Core(SQL) API

|**템플릿**|**설명**|
|---|---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[분석 저장소를 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-analytical-store) | 이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 통해 한 지역에 Core(SQL) API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-manual) | 이 템플릿은 표준 처리량이 있는 데이터베이스와 컨테이너의 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[저장 프로시저, 트리거 및 UDF를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿은 컨테이너에 대한 저장 프로시저, 트리거 및 UDF를 사용하여 두 지역에 Core(SQL) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos Core(SQL) API 계정에 대한 프라이빗 엔드포인트를 만듭니다. |
|[체험 계층 Azure Cosmos 계정 만들기](manage-sql-with-resource-manager.md#free-tier) |  이 템플릿은 체험 계층에서 Azure Cosmos DB Core(SQL) API 계정을 만듭니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-autoscale) | 이 템플릿은 데이터베이스 수준 자동 크기 조정 처리량을 공유하는 두 개의 컨테이너가 있는 두 지역에서 Azure Cosmos DB API for MongoDB를 사용하여 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-manual) | 이 템플릿은 데이터베이스 수준 표준 처리량을 공유하는 두 개의 컨테이너가 있는 두 지역에서 Azure Cosmos DB API for MongoDB를 사용하여 계정을 만듭니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 키스페이스, 테이블 만들기](manage-cassandra-with-resource-manager.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 키스페이스와 컨테이너를 사용하여 두 지역에 Cassandra API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 키스페이스, 테이블 만들기](manage-cassandra-with-resource-manager.md#create-manual) | 이 템플릿은 수동 처리량이 있는 키스페이스와 테이블을 사용하여 두 지역에 Cassandra API 계정을 만듭니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 데이터베이스와 그래프를 사용하여 두 지역에 Gremlin API 계정을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-manual) | 이 템플릿은 표준 처리량이 있는 데이터베이스와 그래프를 사용하여 두 지역에 Gremlin API 계정을 만듭니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용하여 Azure Cosmos 계정, 테이블 만들기](manage-table-with-resource-manager.md#create-autoscale) | 이 템플릿은 두 지역에 Table API 계정을 만들고 자동 크기 조정 처리량이 있는 단일 테이블을 만듭니다. |
|[표준(수동) 처리량을 사용하여 Azure Cosmos 계정, 테이블 만들기](manage-table-with-resource-manager.md#create-manual) | 이 템플릿은 두 지역에 Table API 계정을 만들고 표준 처리량이 있는 단일 테이블을 만듭니다. |

참조 설명서는 [Azure Cosmos DB용 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 페이지를 참조하세요.

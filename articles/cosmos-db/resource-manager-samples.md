---
title: Azure Cosmos DB용 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB을 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791632"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿에 대 한 링크가 포함 되어 있습니다.

## <a name="core-sql-api"></a>Core(SQL) API

|**템플릿**|**설명**|
|---|---|
|[자동 크기 조정 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-autoscale) | 이 템플릿은 데이터베이스 및 자동 크기 조정 처리량을 가진 두 개의 지역에서 코어 (SQL) API 계정을 만듭니다. |
|[표준 (수동) 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-manual) | 이 템플릿은 표준 처리량을 사용 하 여 데이터베이스 및 컨테이너 라는 두 지역에서 코어 (SQL) API 계정을 만듭니다. |
|[저장 프로시저, 트리거 및 UDF를 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿에서는 컨테이너에 대 한 저장 프로시저, 트리거 및 UDF를 사용 하 여 두 지역에 코어 (SQL) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정에 대 한 개인 끝점 만들기](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos Core (SQL) API 계정에 대 한 개인 끝점을 만듭니다. |
|[무료 계층 Azure Cosmos 계정 만들기](manage-sql-with-resource-manager.md#free-tier) |  이 템플릿은 무료 계층에서 Azure Cosmos DB 코어 (SQL) API 계정을 만듭니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-autoscale) | 이 템플릿은 데이터베이스 수준 자동 크기 조정 처리량을 공유 하는 두 개의 컨테이너를 사용 하 여 두 지역에서 MongoDB 용 Azure Cosmos DB API를 사용 하 여 계정을 만듭니다. |
|[표준 (수동) 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-manual) | 이 템플릿은 데이터베이스 수준 표준 처리량을 공유 하는 두 개의 컨테이너를 사용 하 여 두 지역에서 MongoDB 용 Azure Cosmos DB API를 사용 하 여 계정을 만듭니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 포함 하는 Azure Cosmos account, keyspace, 테이블 만들기](manage-cassandra-with-resource-manager.md#create-autoscale) | 이 템플릿은 자동 크기 조정 처리량이 있는 keyspace 및 테이블을 사용 하 여 두 지역에 Cassandra API 계정을 만듭니다. |
|[표준 (수동) 처리량을 포함 하는 Azure Cosmos account, keyspace 테이블 만들기](manage-cassandra-with-resource-manager.md#create-manual) | 이 템플릿은 keyspace 및 수동 처리량이 있는 테이블을 사용 하 여 두 지역에 Cassandra API 계정을 만듭니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-autoscale) | 이 템플릿은 데이터베이스와 자동 크기 조정 처리량이 있는 두 지역에 Gremlin API 계정을 만듭니다. |
|[표준 (수동) 처리량을 사용 하 여 Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-manual) | 이 템플릿은 데이터베이스와 표준 처리량이 있는 그래프를 사용 하 여 두 지역에 Gremlin API 계정을 만듭니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[자동 크기 조정 처리량이 있는 테이블, Azure Cosmos 계정 만들기](manage-table-with-resource-manager.md#create-autoscale) | 이 템플릿은 두 지역에 Table API 계정을 만들며 자동 크기 조정 처리량이 있는 단일 테이블을 만듭니다. |
|[표준 (수동) 처리량을 포함 하는 Azure Cosmos 계정 만들기](manage-table-with-resource-manager.md#create-manual) | 이 템플릿은 두 지역에 Table API 계정을 만들고 표준 처리량이 포함 된 단일 테이블을 만듭니다. |

참조 설명서는 [Azure Cosmos DB 페이지 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 를 참조 하세요.

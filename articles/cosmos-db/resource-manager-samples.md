---
title: Azure Cosmos DB용 Azure Resource Manager 템플릿
description: Azure 리소스 관리자 템플릿을 사용하여 Azure Cosmos DB를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390869"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB에 대한 Azure 리소스 관리자 템플릿에 대한 링크가 포함되어 있습니다.

## <a name="sql-core-api"></a>SQL(Core) API

|**템플릿**|**설명**|
|---|---|
|[Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-resource) | 이 템플릿은 공유 데이터베이스 처리량이 있는 두 개의 컨테이너와 전용 처리량이 있는 컨테이너가 있는 두 지역에서 SQL(Core) API 계정을 만듭니다. 처리량은 업데이트된 처리량 속성 값으로 템플릿을 다시 제출하여 업데이트할 수 있습니다. |
|[저장된 프로시저, 트리거 및 UDF를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿은 컨테이너에 대한 저장 프로시저, 트리거 및 UDF가 있는 두 지역에 SQL(코어) API 계정을 만듭니다. |
|[기존 Azure Cosmos 계정에 대한 개인 끝점 만들기](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  이 템플릿은 기존 가상 네트워크에서 기존 Azure Cosmos SQL API 계정에 대한 개인 끝점을 만듭니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[Azure 코스모스 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-resource) | 이 템플릿은 멀티 마스터가 활성화된 두 지역에서 MongoDB용 Azure Cosmos DB API를 사용하여 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유하는 두 개의 컨테이너가 있습니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[Azure 코스모스 계정 만들기, 키 공간, 테이블](manage-cassandra-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화된 두 지역에서 Cassandra API 계정을 만듭니다. Azure Cosmos 계정에는 키공간 수준 처리량을 공유하는 두 개의 테이블이 있습니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[Azure 코스모스 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-resource) | 이 템플릿은 멀티 마스터가 활성화된 두 지역에서 Gremlin API 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유하는 두 개의 그래프가 있습니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[Azure 코스모스 계정 만들기, 표](manage-table-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화된 두 지역에서 Table API 계정을 만듭니다. Azure 코스모스 계정에는 단일 테이블이 있습니다. |

> [!TIP]
> 테이블 API를 사용할 때 공유 처리량을 활성화하려면 Azure Portal에서 계정 수준 처리량을 사용하도록 설정합니다.

참조 [설명서에 대 한 Azure 코스모스 DB](/azure/templates/microsoft.documentdb/allversions) 페이지에 대 한 Azure 리소스 관리자 참조를 참조 를 참조 하십시오.

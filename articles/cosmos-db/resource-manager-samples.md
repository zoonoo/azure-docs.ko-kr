---
title: Azure Cosmos DB에 대 한 azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿 만들기 및 Azure Cosmos DB를 구성 하려면 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078462"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 azure Resource Manager 템플릿

다음 테이블은 Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿 링크를 포함 합니다.

|**API 형식** | **샘플에 연결**| **설명** |
|---|---| ---|
|코어 (SQL) API| [Azure Cosmos DB 계정 (다중 마스터) 만들기](manage-sql-with-resource-manager.md) | 이 템플릿은 두 개의 지역에서 사용 하도록 설정 하는 다중 마스터를 사용 하 여 SQL API 계정을 만듭니다. 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 Azure Cosmos 계정이 됩니다. |
| MongoDB API | [Azure Cosmos DB 계정 (다중 마스터) 만들기](manage-mongodb-with-resource-manager.md) | 이 템플릿은 두 개의 지역에서 MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 사용 하도록 설정 하는 다중 마스터를 사용 하 여 계정을 만듭니다. 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 Azure Cosmos 계정이 됩니다. |
| Cassandra API | [Azure Cosmos DB 계정 (다중 마스터) 만들기](manage-cassandra-with-resource-manager.md) | 이 템플릿을 사용 하도록 설정 하는 다중 마스터를 사용 하 여 두 지역에 Cassandra API 계정을 만듭니다. Azure Cosmos 계정 keyspace 수준 처리량을 공유 하는 두 테이블 해야 합니다. |
| Gremlin API| [Azure Cosmos DB 계정 (다중 마스터) 만들기](manage-gremlin-with-resource-manager.md) | 이 템플릿은 사용 하도록 설정 하는 다중 마스터를 사용 하 여 두 지역에는 Gremlin API 계정을 만듭니다. 두 그래프 데이터베이스 수준 처리량을 공유 하는 Azure Cosmos 계정이 됩니다. |
| 테이블 API | [Azure Cosmos DB 계정 (다중 마스터) 만들기](manage-table-with-resource-manager.md) | 이 템플릿은 두 개의 지역에서 사용 하도록 설정 하는 다중 마스터를 사용 하 여 Table API 계정을 만듭니다. Azure Cosmos 계정에는 단일 테이블을 해야 합니다. |

> [!TIP]
> 공유 처리량을 Table API를 사용 하는 경우 사용 하도록 설정 하려면 Azure Portal에서 계정 수준 처리량을 사용 하도록 설정 합니다.

참조 [Azure Cosmos DB에 대 한 ARM 참조](/azure/templates/microsoft.documentdb/allversions) 참조 설명서 페이지.
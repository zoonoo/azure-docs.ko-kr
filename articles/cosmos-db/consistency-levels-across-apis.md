---
title: 일관성 수준 및 Azure Cosmos DB API
description: Azure Cosmos DB에서 API에 대한 일관성 수준 이해
keywords: 일관성, azure cosmos db, azure, 모델, mongodb, cassandra, 그래프, 테이블, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832499"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>일관성 수준 및 Azure Cosmos DB API

Azure Cosmos DB에서 제공되는 5가지 일관성 모델은 Azure Cosmos DB SQL API에서 기본적으로 지원됩니다. Azure Cosmos DB를 사용하는 경우 SQL API는 기본값입니다. 

Azure Cosmos DB는 인기 있는 데이터베이스에 대한 와이어 프로토콜 호환 API를 기본적으로 지원합니다. 데이터베이스에는 MongoDB, Apache Cassandra, Gremlin 및 Azure Table Storage가 있습니다. 이러한 데이터베이스는 정확하게 정의된 일관성 모델 또는 일관성 수준에 대한 SLA 지원 보장을 제공하지 않습니다. 이러한 데이터베이스는 일반적으로 Azure Cosmos DB에서 제공된 5가지 일관성 모델의 하위 집합만 제공합니다. SQL API, Gremlin API 및 Table API의 경우 Azure Cosmos DB 계정에서 구성된 기본 일관성 수준을 사용합니다. 

다음 섹션에서는 Apache Cassandra 4.x 및 MongoDB 3.4용 OSS 클라이언트 드라이버에서 요청된 데이터 일관성 간의 매핑을 보여 줍니다. 또한 이 문서에서는 Apache Cassandra 및 MongoDB용 해당 Azure Cosmos DB 일관성 수준을 보여 줍니다.

## <a id="cassandra-mapping"></a>Apache Cassandra 및 Azure Cosmos DB 일관성 수준 간 매핑

이 테이블에서는 Apache Cassandra 4.x 클라이언트와 Azure Cosmos DB의 기본 일관성 수준 간의 “읽기 일관성” 매핑을 보여 줍니다. 또한 다중 지역 및 단일 지역 배포도 보여 줍니다.

| **Apache Cassandra 4.x** | **Azure Cosmos DB(다중 영역)** | **Azure Cosmos DB(단일 영역)** |
| - | - | - |
| ONE, TWO, THREE | 일관적인 접두사 | 일관적인 접두사 |
| LOCAL_ONE | 일관적인 접두사 | 일관적인 접두사 |
| QUORUM, ALL, SERIAL | 제한된 부실은 기본값입니다. 강력은 현재 비공개 미리 보기입니다. | 강력 |
| LOCAL_QUORUM | 제한된 부실 | 강력 |
| LOCAL_SERIAL | 제한된 부실 | 강력 |

## <a id="mongo-mapping"></a>MongoDB 3.4 및 Azure Cosmos DB 일관성 수준 간의 매핑

다음 테이블에서는 MongoDB 3.4와 Azure Cosmos DB의 기본 일관성 수준 간의 “읽기 문제” 매핑을 보여줍니다. 또한 다중 지역 및 단일 지역 배포도 보여 줍니다.

| **MongoDB 3.4** | **Azure Cosmos DB(다중 영역)** | **Azure Cosmos DB(단일 영역)** |
| - | - | - |
| 선형화 가능 | 강력 | 강력 |
| 과반수 | 제한된 부실 | 강력 |
| Local | 일관적인 접두사 | 일관적인 접두사 |

## <a name="next-steps"></a>다음 단계

오픈 소스 API 및 Cosmos DB API 간의 일관성 수준 및 호환성에 대해 자세히 알아봅니다. 다음 문서를 참조하세요.

* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB MongoDB API에서 지원된 MongoDB 기능](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API에서 지원된 Apache Cassandra 기능](cassandra-support.md)
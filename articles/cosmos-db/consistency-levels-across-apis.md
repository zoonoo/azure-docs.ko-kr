---
title: 일관성 수준 및 Azure Cosmos DB API | Microsoft Docs
description: Azure Cosmos DB에서 API에 대한 일관성 수준 이해
keywords: 일관성, azure cosmos db, azure, 모델, mongodb, cassandra, 그래프, 테이블, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243998"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>일관성 수준 및 Cosmos DB API

5가지 일관성 모델은 Cosmos DB를 사용할 때 기본 API인 SQL API로 기본적으로 지원됩니다. SQL API 외에도 Cosmos DB는 MongoDB, Apache Cassandra, Gremlin 및 Azure Table과 같은 인기 있는 데이터베이스에 대해 유선 프로토콜 호환 API에 대한 기본 지원을 제공합니다. 이러한 데이터베이스는 일관성 수준에 대해 정확하게 정의된 일관성 모델 및 SLA 기반 보장을 제공하지 않으며, 일반적으로 Cosmos DB에서 제공하는 5가지 일관성 모델의 하위 집합만 제공합니다. SQL API, Gremlin API 및 Table API의 경우 Cosmos 계정에 구성된 기본 일관성 수준이 사용됩니다.

다음 표는 Cassandra API 및 MongoDB API를 사용할 때 Apache Cassandra 4.x 및 MongoDB 3.4용 OSS 클라이언트 드라이버로 요청된 데이터 일관성 간의 매핑을 각각 보여주며, 해당 Cosmos DB 일관성 수준을 보여줍니다.

## <a id="cassandra-mapping"></a>Apache Cassandra 및 Cosmos DB 일관성 수준 매핑

아래 표는 다중 영역 및 단일 영역 배포에 대한 Apache Cassandra 4.x 클라이언트와 Cosmos DB "기본" 일관성 수준 간의 읽기 일관성에 대한 매핑을 보여줍니다.

| **Apache Cassandra 4.x** | **Cosmos DB(다중 영역)** | **Cosmos DB(단일 영역)** |
| - | - | - |
| ONE, TWO, THREE | 일관적인 접두사 | 일관적인 접두사 |
| LOCAL_ONE | 일관적인 접두사 | 일관적인 접두사 |
| QUORUM, ALL, SERIAL | 제한된 부실(기본값) 또는 강력(비공개 미리 보기에서) | 강력 |
| LOCAL_QUORUM | 제한된 부실 | 강력 |
| LOCAL_SERIAL | 제한된 부실 | 강력 |

## <a id="mongo-mapping"></a>MongoDB 3.4 및 Cosmos DB 일관성 수준 간의 매핑

아래 표는 다중 영역 및 단일 영역 배포에 대한 MongoDB 3.4 의 "읽기 문제"와 Cosmos DB "기본" 일관성 수준에 대한 매핑을 보여줍니다.

| **MongoDB 3.4** | **Cosmos DB(다중 영역)** | **Cosmos DB(단일 영역)** |
| - | - | - |
| 선형화 가능 | 강력 | 강력 |
| 과반수 | 제한된 부실 | 강력 |
| Local | 일관적인 접두사 | 일관적인 접두사 |

## <a name="next-steps"></a>다음 단계

다음 문서의 오픈 소스 API와 함께 Cosmos DB API 간의 일관성 수준 및 호환성에 대해 자세히 알아봅니다.

* [다양한 일관성 수준에 대한 가용성 및 성능의 장단점](consistency-levels-tradeoffs.md)
* [Cosmos DB MongoDB API에서 지원하는 MongoDB 기능](mongodb-feature-support.md)
* [Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능](cassandra-support.md)
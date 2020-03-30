---
title: 일관성 수준 및 Azure Cosmos DB API
description: Azure 코스모스 DB와 아파치 카산드라, 몽고DB의 서로 다른 API 간의 일관성 수준 매핑 이해
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131466"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>일관성 수준 및 Azure Cosmos DB API

Azure Cosmos DB는 인기 있는 데이터베이스에 대 한 와이어 프로토콜 호환 API에 대 한 기본 지원을 제공 합니다. 여기에는 몽고DB, 아파치 카산드라, 그렘린 및 Azure 테이블 저장소가 포함됩니다. 이러한 데이터베이스는 정확하게 정의된 일관성 모델이나 일관성 수준에 대한 SLA 지원 보증을 제공하지 않습니다. 이러한 데이터베이스는 일반적으로 Azure Cosmos DB에서 제공된 5가지 일관성 모델의 하위 집합만 제공합니다. 

SQL API, 그렘린 API 및 테이블 API를 사용하는 경우 Azure Cosmos 계정에 구성된 기본 일관성 수준이 사용됩니다. 

MongoDB에 대한 카산드라 API 또는 Azure Cosmos DB의 API를 사용하는 경우 응용 프로그램은 아파치 카산드라와 몽고DB에서 제공하는 전체 일관성 수준 집합을 얻을 수 있으며 일관성과 내구성이 더욱 강화됩니다. 이 문서에서는 아파치 카산드라 및 MongoDB 일관성 수준에 대한 해당 Azure Cosmos DB 일관성 수준을 보여 줍니다.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Apache Cassandra 및 Azure Cosmos DB 일관성 수준 간 매핑

Azure 코스모스 DB와 달리 아파치 카산드라는 기본적으로 정확하게 정의된 일관성 보장을 제공하지 않습니다.  대신, 아파치 Cassandra 는 높은 가용성, 일관성 및 대기 시간 절충을 가능하게 하기 위해 쓰기 일관성 수준과 읽기 일관성 수준을 제공합니다. Azure 코스모스 DB의 카산드라 API를 사용하는 경우: 

* 아파치 카산드라의 쓰기 일관성 수준은 Azure Cosmos 계정에 구성된 기본 일관성 수준에 매핑됩니다. 쓰기 작업(CL)에 대한 일관성은 요청별로 변경할 수 없습니다.

* Azure Cosmos DB는 Cassandra 클라이언트 드라이버에서 지정한 읽기 일관성 수준을 읽기 요청에 따라 동적으로 구성된 Azure Cosmos DB 일관성 수준 중 하나에 동적으로 매핑합니다. 

다음 표에서는 카산드라 API를 사용할 때 네이티브 카산드라 일관성 수준이 Azure Cosmos DB의 일관성 수준에 매핑되는 방법을 보여 줍니다.  

[![카산드라 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>몽고DB와 Azure 코스모스 DB 일관성 수준 간의 매핑

Azure Cosmos DB와 달리 네이티브 MongoDB는 정확하게 정의된 일관성 보장을 제공하지 않습니다. 대신, 네이티브 MongoDB는 사용자가 다음과 같은 일관성 보장을 구성할 수 있습니다: 쓰기 문제, 읽기 문제 및 isMaster 지시문 - 읽기 작업을 기본 또는 보조 복제본으로 지시하여 원하는 일관성 수준을 달성할 수 있습니다. 

MongoDB에 Azure Cosmos DB의 API를 사용하는 경우 MongoDB 드라이버는 쓰기 영역을 기본 복제본으로 처리하고 다른 모든 영역은 읽기 복제본으로 처리합니다. Azure Cosmos 계정과 연결된 지역을 기본 복제본으로 선택할 수 있습니다. 

MongoDB에 Azure 코스모스 DB의 API를 사용하는 동안:

* 쓰기 문제는 Azure Cosmos 계정에 구성된 기본 일관성 수준에 매핑됩니다.
 
* Azure Cosmos DB는 MongoDB 클라이언트 드라이버에서 지정한 읽기 우려 내용을 읽기 요청에 따라 동적으로 구성된 Azure Cosmos DB 일관성 수준 중 하나에 동적으로 매핑합니다.  

* Azure Cosmos 계정과 연결된 특정 지역에 "마스터"로 추가하여 해당 지역을 첫 번째 쓰기 가능 지역으로 만들 수 있습니다. 

다음 표에서는 MongoDB에 대한 Azure Cosmos DB의 API를 사용할 때 네이티브 MongoDB 쓰기/읽기 문제가 Azure Cosmos 일관성 수준에 매핑되는 방법을 보여 줍니다.

[![MongoDB 일관성 모델 매핑](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>다음 단계

오픈 소스 API 및 Cosmos DB API 간의 일관성 수준 및 호환성에 대해 자세히 알아봅니다. 다음 문서를 참조하세요.

* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB의 API for MongoDB에서 지원되는 MongoDB 기능](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API에서 지원된 Apache Cassandra 기능](cassandra-support.md)

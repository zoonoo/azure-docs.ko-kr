---
title: Azure Cosmos DB의 데이터베이스, 컨테이너 및 항목으로 작업
description: 이 문서에서는 Azure Cosmos DB에서 데이터베이스, 컨테이너 및 항목을 만들고 사용하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246787"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터베이스, 컨테이너 및 항목으로 작업

Azure 구독에서 [Azure Cosmos DB 계정을](account-overview.md) 만든 후 데이터베이스, 컨테이너 및 항목을 만들어 계정의 데이터를 관리할 수 있습니다. 이 문서에서는 이러한 각 엔터티에 대해 설명합니다. 

다음 이미지는 Azure Cosmos DB 계정의 여러 엔터티계층을 보여 주며 다음과 같은 이미지입니다.

![Azure Cosmos 계정 엔터티](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 데이터베이스

계정 아래에 하나 또는 여러 개의 Azure Cosmos 데이터베이스를 만들 수 있습니다. 데이터베이스는 네임스페이스와 유사합니다. 데이터베이스는 Azure Cosmos 컨테이너 집합에 대한 관리 단위입니다. 다음 표에서는 Azure Cosmos 데이터베이스가 다양한 API 관련 엔터티에 매핑되는 방법을 보여줍니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 데이터베이스 | 데이터베이스 | Keyspace | 데이터베이스 | 데이터베이스 | 해당 없음 |

> [!NOTE]
> 테이블 API 계정을 사용하면 첫 번째 테이블을 만들 때 Azure Cosmos 계정에 기본 데이터베이스가 자동으로 만들어집니다.

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에서 작업

다음 표에 설명된 대로 Azure Cosmos API를 사용하여 Azure Cosmos 데이터베이스와 상호 작용할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
|모든 데이터베이스 열거| yes | yes | 예(데이터베이스가 keyspace에 매핑됨) | yes | 해당 없음 | 해당 없음 |
|데이터베이스 읽기| yes | yes | 예(데이터베이스가 keyspace에 매핑됨) | yes | 해당 없음 | 해당 없음 |
|새 데이터베이스 만들기| yes | yes | 예(데이터베이스가 keyspace에 매핑됨) | yes | 해당 없음 | 해당 없음 |
|데이터베이스 업데이트| yes | yes | 예(데이터베이스가 keyspace에 매핑됨) | yes | 해당 없음 | 해당 없음 |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 컨테이너

Azure Cosmos 컨테이너는 프로비저닝된 처리량 및 저장소모두에 대한 확장성의 단위입니다. 컨테이너는 가로로 분할된 다음, 여러 지역에 걸쳐 복제됩니다. 컨테이너에 추가하는 항목과 컨테이너에 프로비저닝하는 처리량은 파티션 키에 따라 논리 파티션 세트에 자동으로 분산됩니다. 분할 및 파티션 키에 대한 자세한 내용은 [파티션 데이터를](partition-data.md)참조하십시오. 

Azure Cosmos 컨테이너를 만들 때 다음 모드 중 하나로 처리량을 구성합니다.

* **전용 프로비저닝 처리량 모드**: 컨테이너에 프로비전된 처리량은 해당 컨테이너에 대해 독점적으로 예약되어 있으며 SLA에 의해 지원됩니다. 자세한 내용은 [Azure Cosmos 컨테이너에서 처리량을 프로비전하는 방법을](how-to-provision-container-throughput.md)참조하세요.

* **공유 프로비저닝 처리량 모드**: 이러한 컨테이너는 프로비저닝된 처리량을 동일한 데이터베이스의 다른 컨테이너와 공유합니다(전용 프로비저닝된 처리량으로 구성된 컨테이너 제외). 즉, 데이터베이스에서 프로비저닝된 처리량은 모든 "공유 처리량" 컨테이너 간에 공유됩니다. 자세한 내용은 [Azure Cosmos 데이터베이스에서 처리량을 프로비전하는 방법을](how-to-provision-database-throughput.md)참조하세요.

> [!NOTE]
> 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 컨테이너를 만든 후 전용 처리량 모드에서 공유 처리량 모드로 전환하거나 그 반대로 전환하려면 새 컨테이너를 만들고 데이터를 새 컨테이너로 마이그레이션해야 합니다. Azure Cosmos DB 변경 피드 기능을 사용하여 데이터를 마이그레이션할 수 있습니다.

Azure Cosmos 컨테이너는 전용 또는 공유 프로비저닝처리량 모드를 사용하여 컨테이너를 만들든 탄력적으로 확장할 수 있습니다.

Azure Cosmos 컨테이너는 스키마에 구애받지 않는 항목 컨테이너입니다. 컨테이너의 항목에는 임의의 스키마가 있을 수 있습니다. 예를 들어 사람을 나타내는 항목과 자동차를 나타내는 항목을 *동일한 컨테이너에*배치할 수 있습니다. 기본적으로 컨테이너에 추가하는 모든 항목은 명시적 인덱스 또는 스키마 관리없이 자동으로 인덱싱됩니다. 컨테이너에서 인덱싱 정책을 구성하여 [인덱싱](index-overview.md) 동작을 사용자 지정할 수 있습니다. 

Azure Cosmos 컨테이너의 선택된 항목또는 전체 컨테이너에 대해 [TTL(Time to Live)을](time-to-live.md) 설정하여 시스템에서 해당 항목을 정상적으로 제거할 수 있습니다. Azure Cosmos DB는 만료될 때 항목을 자동으로 삭제합니다. 또한 컨테이너에서 수행된 쿼리가 고정 바운드 내에서 만료된 항목을 반환하지 않도록 보장합니다. 자세한 내용은 [컨테이너의 TTL 구성을](how-to-time-to-live.md)참조하십시오.

[변경 피드를](change-feed.md) 사용하여 컨테이너의 각 논리 파티션에 대해 관리되는 작업 로그를 구독할 수 있습니다. 변경 피드는 컨테이너에서 수행된 모든 업데이트의 로그와 항목의 이전 및 이후 이미지를 제공합니다. 자세한 내용은 [변경 피드를 사용하여 반응형 응용 프로그램 빌드를](serverless-computing-database.md)참조하십시오. 컨테이너의 변경 피드 정책을 사용하여 변경 피드의 보존 기간을 구성할 수도 있습니다. 

[저장 프로시저, 트리거, 사용자 정의 함수(DF)](stored-procedures-triggers-udfs.md)및 Azure Cosmos 컨테이너에 대한 [프로시저를 병합할](how-to-manage-conflicts.md) 수 있습니다. 

Azure Cosmos 컨테이너에 [고유한 키 제약 조건을](unique-keys.md) 지정할 수 있습니다. 고유 키 정책을 만들면 논리 파티션 키당 하나 이상의 값의 고유성을 보장합니다. 고유한 키 정책을 사용하여 컨테이너를 만드는 경우 고유 키 제약 조건에 의해 지정된 값을 복제하는 값이 있는 새 항목이나 업데이트된 항목을 만들 수 없습니다. 자세한 내용은 [고유 키 제약 조건](unique-keys.md)을 참조하세요.

Azure Cosmos 컨테이너는 다음 표와 같이 API 관련 엔터티로 전문화됩니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 컨테이너 | 컨테이너 | 테이블 | 컬렉션 | 그래프 | 테이블 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너의 속성

Azure Cosmos 컨테이너에는 시스템 정의 속성 집합이 있습니다. 사용하는 API에 따라 일부 속성이 직접 노출되지 않을 수 있습니다. 다음 표는 시스템 정의 속성 목록을 설명합니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능 | 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_제거 | 시스템 생성 | 컨테이너의 고유 식별자 | yes | 예 | 예 | 예 | 예 |
|\_Etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | yes | 예 | 예 | 예 | 예 |
|\_Ts | 시스템 생성 | 컨테이너의 마지막 업데이트 타임스탬프 | yes | 예 | 예 | 예 | 예 |
|\_자체 | 시스템 생성 | 컨테이너의 주소 지정 가능 URI | yes | 예 | 예 | 예 | 예 |
|id | 사용자 구성 가능 | 컨테이너의 사용자 정의 고유 이름 | yes | yes | yes | yes | yes |
|indexingPolicy | 사용자 구성 가능 | 인덱스 경로, 인덱스 유형 및 인덱스 모드를 변경할 수 있는 기능을 제공합니다. | yes | 예 | 예 | 예 | yes |
|timeToLive | 사용자 구성 가능 | 설정된 기간 이후에 컨테이너에서 항목을 자동으로 삭제하는 기능을 제공합니다. 자세한 내용은 [라이브 시간을](time-to-live.md)참조하십시오. | yes | 예 | 예 | 예 | yes |
|changeFeedPolicy | 사용자 구성 가능 | 컨테이너의 항목에 대한 변경 내용을 읽는 데 사용됩니다. 자세한 내용은 [피드 변경](change-feed.md)을 참조하십시오. | yes | 예 | 예 | 예 | yes |
|uniqueKeyPolicy | 사용자 구성 가능 | 논리 파티션에서 하나 이상의 값의 고유성을 보장하는 데 사용됩니다. 자세한 내용은 [고유 키 제약 조건을](unique-keys.md)참조하십시오. | yes | 예 | 예 | 예 | yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너에서 작업

Azure Cosmos 컨테이너는 Azure Cosmos API를 사용할 때 다음 작업을 지원합니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 데이터베이스에 컨테이너 열거 | yes | yes | yes | yes | 해당 없음 | 해당 없음 |
| 컨테이너 읽기 | yes | yes | yes | yes | 해당 없음 | 해당 없음 |
| 새 컨테이너 만들기 | yes | yes | yes | yes | 해당 없음 | 해당 없음 |
| 컨테이너 업데이트 | yes | yes | yes | yes | 해당 없음 | 해당 없음 |
| 컨테이너 삭제 | yes | yes | yes | yes | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 항목

사용하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드 또는 가장자리를 나타낼 수 있습니다. 다음 표에서는 Azure Cosmos 항목에 API 관련 엔터티를 매핑하는 방법을 보여 주며 있습니다.

| Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 항목 | 문서 | 행 | 문서 | 노드 또는 모서리 | 항목 |

### <a name="properties-of-an-item"></a>항목의 속성

모든 Azure Cosmos 항목에는 다음과 같은 시스템 정의 속성이 있습니다. 사용하는 API에 따라 일부 API가 직접 노출되지 않을 수 있습니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능| 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_제거 | 시스템 생성 | 항목의 고유 식별자 | yes | 예 | 예 | 예 | 예 |
|\_Etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | yes | 예 | 예 | 예 | 예 |
|\_Ts | 시스템 생성 | 항목의 마지막 업데이트의 타임스탬프 | yes | 예 | 예 | 예 | 예 |
|\_자체 | 시스템 생성 | 항목의 주소 지정 가능 URI | yes | 예 | 예 | 예 | 예 |
|id | 여기서는 | 논리 파티션에서 사용자 정의 고유 이름입니다. | yes | yes | yes | yes | yes |
|임의의 사용자 정의 속성 | 사용자 정의 | API 네이티브 표현으로 표시되는 사용자 정의 속성(JSON, BSON 및 CQL 포함) | yes | yes | yes | yes | yes |

> [!NOTE]
> `id` 속성의 고유성은 각 논리 파티션 내에서만 적용됩니다. 여러 문서에 다른 `id` 파티션 키 값을 가진 동일한 속성을 가질 수 있습니다.

### <a name="operations-on-items"></a>항목에 대한 작업

Azure 코스모스 항목은 다음 작업을 지원합니다. Azure Cosmos API를 사용하여 작업을 수행할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 삽입, 바꾸기, 삭제, Upsert, 읽기 | 예 | yes | yes | yes | yes | yes |

## <a name="next-steps"></a>다음 단계

다음 작업 및 개념에 대해 알아봅니다.

* [Azure Cosmos 데이터베이스의 프로비전 처리량](how-to-provision-database-throughput.md)
* [Azure Cosmos 컨테이너의 프로비전 처리량](how-to-provision-container-throughput.md)
* [논리 파티션으로 작업](partition-data.md)
* [Azure 코스모스 컨테이너에서 TTL 구성](how-to-time-to-live.md)
* [변경 피드를 사용하여 반응형 응용 프로그램 빌드](change-feed.md)
* [Azure Cosmos 컨테이너에서 고유한 키 제약 조건 구성](unique-keys.md)

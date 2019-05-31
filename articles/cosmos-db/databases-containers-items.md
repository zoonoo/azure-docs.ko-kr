---
title: 데이터베이스, 컨테이너 및 Azure Cosmos DB에서 항목을 사용 하 여 작동 합니다.
description: 이 문서를 만들고 Azure Cosmos DB의 데이터베이스, 컨테이너 및 항목을 사용 하는 방법을 설명 합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389461"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>데이터베이스, 컨테이너 및 Azure Cosmos DB에서 항목을 사용 하 여 작동 합니다.

Azure 구독 아래에 [Azure Cosmos DB 계정](account-overview.md)을 생성한 후 데이터베이스, 컨테이너 및 항목을 만들어 계정의 데이터를 관리할 수 있습니다. 이 문서에서는 이러한 각 엔터티를 설명합니다. 

다음 이미지는 Azure Cosmos DB 계정에 다른 엔터티의 계층 구조를 보여줍니다.

![Azure Cosmos 계정 엔터티](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 데이터베이스

계정에서 하나 이상의 Azure Cosmos 데이터베이스를 만들 수 있습니다. 데이터베이스는 네임 스페이스와 비슷합니다. 데이터베이스는 단위는 Azure Cosmos 컨테이너 집합에 대 한 관리입니다. 다음 표에서는 Azure Cosmos 데이터베이스가 다양한 API 관련 엔터티에 매핑되는 방법을 보여줍니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 데이터베이스 | 데이터베이스 | Keyspace | 데이터베이스 | 데이터베이스 | 해당 없음 |

> [!NOTE]
> Table API 계정으로 첫 번째 테이블을 만들 때 기본 데이터베이스를 자동으로 만들어집니다 Azure Cosmos 계정의.

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에서 작업

다음 표에 설명 된 대로 Azure Cosmos Api를 사용 하 여 Azure Cosmos 데이터베이스를 조작할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
|모든 데이터베이스 열거| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 읽기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|새 데이터베이스 만들기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 업데이트| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 컨테이너

Azure Cosmos 컨테이너는 모두 프로 비전 된 처리량 및 저장소에 대 한 확장성 단위입니다. 컨테이너는 가로로 분할된 다음, 여러 지역에 걸쳐 복제됩니다. 컨테이너에 추가하는 항목과 컨테이너에 프로비저닝하는 처리량은 파티션 키에 따라 논리 파티션 세트에 자동으로 분산됩니다. 분할 및 파티션 키에 대 한 자세한 내용은 참조 하세요 [데이터를 분할](partition-data.md)합니다. 

Azure Cosmos 컨테이너를 만들면 다음 모드 중 하나에서 처리량을 구성할 수 있습니다.

* **전용된 프로 비전 된 처리량 모드**: 컨테이너에서 프로 비전 된 처리량은 해당 컨테이너에 대 한 단독으로 예약 되었습니다 및 Sla에서 지원 됩니다. 자세한 내용은 참조 하세요 [Azure Cosmos 컨테이너에 대 한 처리량을 프로 비전 하는 방법을](how-to-provision-container-throughput.md)합니다.

* **공유 프로 비전 된 처리량 모드**: 이러한 컨테이너는 동일한 데이터베이스 (전용된 프로 비전 된 처리량을 사용 하 여 구성 된 컨테이너 제외)의 다른 컨테이너를 사용 하 여 프로 비전된 된 처리량을 공유 합니다. 즉, 데이터베이스에서 프로 비전된 된 처리량은 모든 "공유 처리량" 컨테이너 간에 공유 됩니다. 자세한 내용은 참조 하세요 [Azure Cosmos 데이터베이스에서 처리량을 프로 비전 하는 방법을](how-to-provision-database-throughput.md)합니다.

> [!NOTE]
> 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 컨테이너를 만든 후 공유 처리량 모드 (또는 그 반대로) 전용된 처리량 모드에서 전환 하려면 새 컨테이너를 만들고 새 컨테이너에 데이터를 마이그레이션할 해야 합니다. Azure Cosmos DB 변경 피드 기능을 사용 하 여 데이터를 마이그레이션할 수 있습니다.

Azure Cosmos 컨테이너를 전용 또는 공유 프로 비전 된 처리량 모드를 사용 하 여 컨테이너를 만들어도 탄력적으로 확장할 수 있습니다.

Azure Cosmos 컨테이너는 스키마에 구애받지 않는 항목 컨테이너입니다. 항목 컨테이너에 임의의 스키마를 가질 수 있습니다. 예를 들어 사람을 나타내는 항목 및 자동차를 나타내는 항목 수에 배치 합니다 *동일한 컨테이너*합니다. 기본적으로 컨테이너에 추가 하는 모든 항목은 명시적 인덱스 또는 스키마 관리 하지 않고도 자동으로 인덱싱됩니다. 구성 하 여 인덱싱 동작을 사용자 지정할 수 있습니다 합니다 [인덱싱 정책](index-overview.md) 컨테이너에서. 

설정할 수 있습니다 [시간에 TTL (Live)](time-to-live.md) 정상적으로 시스템에서 해당 항목을 제거 하려면 전체 컨테이너 또는 Azure Cosmos 컨테이너에서 선택한 항목입니다. Azure Cosmos DB는 자동으로 만료 될 때 항목을 삭제 합니다. 또한 컨테이너에 수행 하는 쿼리 고정된 범위 내에서 만료 된 항목을 반환 하지는 보장 합니다. 자세한 내용은 참조 하세요 [컨테이너에서 TTL 구성](how-to-time-to-live.md)합니다.

사용할 수 있습니다 [변경 피드](change-feed.md) 컨테이너의 각 논리 파티션에 대 한 관리 되는 작업 로그를 구독할 수 있습니다. 변경 피드는와 함께 컨테이너에서 수행 된 모든 업데이트가의 로그를 제공 된 이전 및 이후 이미지 항목의 합니다. 자세한 내용은 [빌드 반응 형 응용 프로그램에서 사용 하 여 변경 피드](serverless-computing-database.md)합니다. 변경 피드 변경 피드는 컨테이너에서 정책을 사용 하 여 보존 기간을 구성할 수도 있습니다. 

등록할 수 있습니다 [저장된 프로시저, 트리거, 사용자 정의 함수 (Udf)](stored-procedures-triggers-udfs.md), 및 [프로시저 병합](how-to-manage-conflicts.md) Azure Cosmos 컨테이너에 대 한 합니다. 

지정할 수는 [unique key 제약 조건을](unique-keys.md) Azure Cosmos 컨테이너에서. 고유 키 정책을 만들면 논리 파티션 키당 하나 이상의 값의 고유성을 보장합니다. 고유 키 정책을 사용 하 여 컨테이너를 만들어야 하는 경우 고유 키 제약 조건에 의해 지정 된 값을 가진 없습니다 새롭거나 업데이트 된 항목을 만들 수 있습니다. 자세한 내용은 [고유 키 제약 조건](unique-keys.md)을 참조하세요.

다음 표에 나와 있는 것 처럼 Azure Cosmos 컨테이너를 API 관련 엔터티로 특수화:

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 컨테이너 | 컬렉션 | 테이블 | 컬렉션 | 그래프 | 테이블 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너의 속성

Azure Cosmos 컨테이너를 시스템에 정의 된 속성 집합이 있습니다. 사용할 API에 따라 일부 속성이 노출 되지 않는 직접. 다음 표에서 시스템 정의 속성 목록입니다.

| 시스템 정의 속성 | 시스템에서 생성 된 또는 사용자 구성 가능 | 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 컨테이너의 고유 식별자 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_Etag | System-generated | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_ts | System-generated | 컨테이너의 마지막 업데이트 타임스탬프 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_자체 | System-generated | 컨테이너의 주소 지정 가능 URI | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|id | User-configurable | 컨테이너의 사용자 정의 고유 이름 | 예 | 예 | 예 | 예 | 예 |
|indexingPolicy | User-configurable | 인덱스 경로, 인덱스 유형 및 인덱스 모드를 변경 하는 기능을 제공 합니다. | 예 | 아니오 | 아니요 | 아니요 | 예 |
|TimeToLive | User-configurable | 일정된 시간이 지나면 자동으로 컨테이너에서 항목을 삭제 하는 기능을 제공 합니다. 자세한 내용은 참조 하세요 [Time to Live](time-to-live.md)합니다. | 예 | 아니오 | 아니요 | 아니요 | 예 |
|changeFeedPolicy | User-configurable | 컨테이너의 항목에 대한 변경 내용을 읽는 데 사용됩니다. 자세한 내용은 참조 하세요 [변경 피드](change-feed.md)합니다. | 예 | 아니오 | 아니요 | 아니요 | 예 |
|uniqueKeyPolicy | User-configurable | 하나 이상의 논리 파티션의 값의 고유성을 보장 하는 데 사용 합니다. 자세한 내용은 [고유 키 제약 조건이](unique-keys.md)합니다. | 예 | 아니오 | 아니요 | 아니요 | 예 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너에서 작업

Azure Cosmos 컨테이너를 Azure Cosmos api를 사용 하는 경우 다음 작업을 지원 합니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 데이터베이스에 컨테이너 열거 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 읽기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 새 컨테이너 만들기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너를 업데이트 합니다. | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 삭제 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 항목

사용할 API에 따라 Azure Cosmos 항목 행을 테이블 또는 노드 컬렉션의 문서 또는 그래프의 가장자리를 나타낼 수 있습니다. 다음 표에서 Azure Cosmos 항목 API 관련 엔터티 매핑을 보여 줍니다.

| Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 항목 | 문서 | 행 | 문서 | 노드 또는 지 | 항목 |

### <a name="properties-of-an-item"></a>항목의 속성

모든 Azure Cosmos 항목에는 다음 시스템 정의 속성이 있습니다. 사용할 API에 따라 그 중 일부에 노출 되지 않는 직접.

| 시스템 정의 속성 | 시스템에서 생성 된 또는 사용자 구성 가능| 목적 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 항목의 고유 식별자 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_Etag | System-generated | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_ts | System-generated | 항목의 마지막 업데이트의 타임 스탬프 | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|\_자체 | System-generated | 항목의 주소 지정 가능 URI | 예 | 아니오 | 아니요 | 아니요 | 아닙니다. |
|id | 모두 | 사용자 정의 고유 이름 논리적 분할입니다. 사용자 ID를 지정 하지 않으면 시스템이 자동으로 생성 합니다. | 예 | 예 | 예 | 예 | 예 |
|임의의 사용자 정의 속성 | 사용자 정의 | API-네이티브 표현 (JSON, BSON을 CQL 등)으로 표시 하는 사용자 정의 속성 | 예 | 예 | 예 | 예 | 예 |

### <a name="operations-on-items"></a>항목에 대한 작업

Azure Cosmos 항목 작업을 지원 합니다. 작업을 수행 하려면 Azure Cosmos Api 중 하나를 사용할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 삽입, 바꾸기, 삭제, Upsert, 읽기 | 아닙니다. | 예 | 예 | 예 | 예 | 예 |

## <a name="next-steps"></a>다음 단계

이러한 태스크 및 개념에 알아봅니다.

* [Azure Cosmos 데이터베이스에서 프로 비전 처리량](how-to-provision-database-throughput.md)
* [Azure Cosmos 컨테이너에 프로 비전 처리량](how-to-provision-container-throughput.md)
* [논리 파티션 사용](partition-data.md)
* [Azure Cosmos 컨테이너에 TTL 구성](how-to-time-to-live.md)
* [변경 피드를 사용 하 여 반응 형 응용 프로그램 빌드](change-feed.md)
* [Azure Cosmos 컨테이너에서 unique key 제약 조건을 구성 합니다.](unique-keys.md)

---
title: Azure Cosmos DB 리소스 모델
description: 이 문서에서는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 항목을 포함 하는 리소스 모델 Azure Cosmos DB 설명 합니다. 또한 Azure Cosmos 계정에 이러한 요소의 계층 구조를 포함 합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 37f1c9f59b6ffb45e1b874d2a6969bf263d2d5eb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341368"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB 리소스 모델
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 완전 관리형 PaaS(platform-as-a-service)입니다. Azure Cosmos DB 사용을 시작 하려면 처음에 Azure 구독 및 데이터베이스, 컨테이너, 그 아래 항목에 Azure Cosmos 계정을 만들어야 합니다. 이 문서에서는 리소스 모델 계층 구조의 Azure Cosmos DB 리소스 모델 및 다른 엔터티를 설명 합니다.

Azure Cosmos 계정은 글로벌 배포와 고가용성을 위한 기본 단위입니다. Azure Cosmos 계정에는 고유한 DNS 이름이 포함되어 있으며, Azure Portal 및 Azure CLI를 사용하여 또는 다양한 언어별 SDK를 사용하여 계정을 관리할 수 있습니다. 자세한 내용은 [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)을 참조하세요. 여러 Azure 지역에 걸쳐 데이터와 처리량을 전역적으로 분산 하기 위해 언제 든 지 계정에 Azure 지역을 추가 하 고 제거할 수 있습니다. 단일 지역 또는 여러 쓰기 지역을 갖도록 계정을 구성할 수 있습니다. 자세한 내용은 [계정에 Azure 지역을 추가 및 제거 하는 방법](how-to-manage-database-account.md)을 참조 하세요. 계정에 대 한 [기본 일관성](consistency-levels.md) 수준을 구성할 수 있습니다.

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 계정의 요소

Azure Cosmos 컨테이너는 확장성의 기본 단위입니다. 하나의 컨테이너는 무제한으로 프로비전된 처리량(RU/s)과 스토리지를 무제한으로 가질 수 있습니다. Azure Cosmos DB는 사용자가 지정한 논리 파티션을 사용하여 컨테이너를 투명하게 분할함으로써 프로비전된 처리량과 스토리지를 탄력적으로 확장합니다.

현재 Azure 구독에서 최대 50 개의 Azure Cosmos 계정을 만들 수 있습니다 (지원 요청을 통해 증가 시킬 수 있는 소프트 제한). 하나의 Azure Cosmos 계정은 데이터와 프로비전된 처리량을 무제한으로 관리할 수 있습니다. 계정에서 하나 이상의 Azure Cosmos 데이터베이스를 만들고 이 데이터베이스 내에서 하나 이상의 컨테이너를 만들어서 데이터와 프로비전된 처리량을 관리할 수 있습니다. 다음 그림은 Azure Cosmos 계정의 요소 계층 구조를 보여 줍니다.

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Azure Cosmos 계정의 계층 구조" border="false":::

Azure 구독에서 계정을 만든 후에는 데이터베이스, 컨테이너 및 항목을 만들어 계정에서 데이터를 관리할 수 있습니다. 

다음 이미지는 Azure Cosmos DB 계정의 여러 엔터티 계층을 보여 줍니다.

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 계정 엔터티" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos 데이터베이스

계정에서 하나 또는 여러 개의 Azure Cosmos 데이터베이스를 만들 수 있습니다. 데이터베이스는 네임 스페이스와 유사 합니다. 데이터베이스는 Azure Cosmos 컨테이너 집합에 대 한 관리 단위입니다. 다음 표에서는 데이터베이스가 다양 한 API 특정 엔터티에 매핑되는 방법을 보여 줍니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 데이터베이스 | 데이터베이스 | Keyspace | 데이터베이스 | 데이터베이스 | 해당 없음 |

> [!NOTE]
> Table API 계정을 사용 하면 첫 번째 테이블을 만들 때 Azure Cosmos 계정에 기본 데이터베이스가 자동으로 만들어집니다.

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에서 작업

다음 표에서 설명 하는 것 처럼 Azure Cosmos Api를 사용 하 여 Azure Cosmos 데이터베이스와 상호 작용할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
|모든 데이터베이스 열거| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 읽기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|새 데이터베이스 만들기| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |
|데이터베이스 업데이트| 예 | 예 | 예(데이터베이스가 keyspace에 매핑됨) | 예 | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-containers"></a>Azure Cosmos 컨테이너

Azure Cosmos 컨테이너는 프로 비전 된 처리량 및 저장소에 대 한 확장성의 단위입니다. 컨테이너는 가로로 분할된 다음, 여러 지역에 걸쳐 복제됩니다. 컨테이너에 추가 하는 항목은 파티션 키에 따라 실제 파티션에 분산 된 논리적 파티션으로 자동 그룹화 됩니다. 컨테이너의 처리량은 실제 파티션에 균등 하 게 분산 됩니다. 분할 및 파티션 키에 대해 자세히 알아보려면 [파티션 데이터](partitioning-overview.md)를 참조 하세요. 

컨테이너를 만들 때 다음 모드 중 하나로 처리량을 구성 합니다.

* **전용 프로 비전 된 처리량 모드** : 컨테이너에 프로 비전 된 처리량은 해당 컨테이너에 대해서만 예약 되며 sla에서 지원 됩니다. 자세히 알아보려면 [컨테이너에서 처리량을 프로 비전 하는 방법](how-to-provision-container-throughput.md)을 참조 하세요.

* **공유 프로 비전 된 처리량 모드** : 이러한 컨테이너는 프로 비전 된 처리량을 동일한 데이터베이스의 다른 컨테이너와 공유 합니다 (전용 프로 비전 된 처리량으로 구성 된 컨테이너 제외). 즉, 데이터베이스에서 프로 비전 된 처리량은 모든 "공유 처리량" 컨테이너 간에 공유 됩니다. 자세히 알아보려면 [데이터베이스에서 처리량을 프로 비전 하는 방법](how-to-provision-database-throughput.md)을 참조 하세요.

> [!NOTE]
> 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 컨테이너를 만든 후 전용 처리량 모드에서 공유 처리량 모드로 전환하거나 그 반대로 전환하려면 새 컨테이너를 만들고 데이터를 새 컨테이너로 마이그레이션해야 합니다. Azure Cosmos DB 변경 피드 기능을 사용 하 여 데이터를 마이그레이션할 수 있습니다.

전용 또는 공유 프로 비전 된 처리량 모드를 사용 하 여 컨테이너를 만들지 여부에 관계 없이 Azure Cosmos 컨테이너는 탄력적으로를 확장할 수 있습니다.

컨테이너는 스키마에 관계 없이 항목의 컨테이너입니다. 컨테이너의 항목에는 임의의 스키마가 있을 수 있습니다. 예를 들어 개인을 나타내는 항목과 자동차를 나타내는 항목을 *같은 컨테이너* 에 배치할 수 있습니다. 기본적으로 컨테이너에 추가 하는 모든 항목은 명시적 인덱스 또는 스키마 관리를 요구 하지 않고 자동으로 인덱싱됩니다. 컨테이너에서 [인덱싱 정책을](index-overview.md) 구성 하 여 인덱싱 동작을 사용자 지정할 수 있습니다. 

컨테이너에서 선택한 항목에 대해 [TTL (Time To Live)](time-to-live.md) 을 설정 하거나 전체 컨테이너를 설정 하 여 시스템에서 해당 항목을 정상적으로 제거할 수 있습니다. Azure Cosmos DB 만료 되 면 항목이 자동으로 삭제 됩니다. 또한 컨테이너에 대해 수행 되는 쿼리가 고정 바운드 내에서 만료 된 항목을 반환 하지 않도록 보장 합니다. 자세히 알아보려면 [컨테이너에서 TTL 구성](how-to-time-to-live.md)을 참조 하세요.

[변경 피드](change-feed.md) 를 사용 하 여 컨테이너의 각 논리 파티션에 대해 관리 되는 작업 로그를 구독할 수 있습니다. 변경 피드는 항목의 이전 및 이후 이미지와 함께 컨테이너에서 수행 되는 모든 업데이트의 로그를 제공 합니다. 자세한 내용은 [변경 피드를 사용 하 여 사후 응용 프로그램 빌드](serverless-computing-database.md)를 참조 하세요. 컨테이너에서 변경 피드 정책을 사용 하 여 변경 피드에 대 한 보존 기간을 구성할 수도 있습니다.

[저장 프로시저, 트리거, udf (사용자 정의 함수)](stored-procedures-triggers-udfs.md)및 컨테이너에 대 한 [병합 프로시저](how-to-manage-conflicts.md) 를 등록할 수 있습니다.

Azure Cosmos 컨테이너에 [unique key 제약 조건을](unique-keys.md) 지정할 수 있습니다. 고유 키 정책을 만들면 논리 파티션 키당 하나 이상의 값의 고유성을 보장합니다. 고유 키 정책을 사용 하 여 컨테이너를 만드는 경우 unique key 제약 조건에 지정 된 값과 중복 되는 값이 있는 새 항목 또는 업데이트 된 항목을 만들 수 없습니다. 자세한 내용은 [고유 키 제약 조건](unique-keys.md)을 참조하세요.

컨테이너는 다음 표에 나와 있는 것 처럼 API 관련 엔터티로 특수화 됩니다.

| Azure Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 컨테이너 | 컨테이너 | 테이블 | 컬렉션 | 그래프 | 테이블 |

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너의 속성

Azure Cosmos 컨테이너에는 시스템 정의 속성 집합이 있습니다. 사용 하는 API에 따라 일부 속성은 직접 노출 되지 않을 수도 있습니다. 다음 표에서는 시스템 정의 속성의 목록에 대해 설명 합니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능 | 용도 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_없앨 | 시스템 생성 | 컨테이너의 고유 식별자 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_터미널 | 시스템 생성 | 컨테이너의 마지막 업데이트 타임스탬프 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_자체 | 시스템 생성 | 컨테이너의 주소 지정 가능 URI | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|id | 사용자 구성 가능 | 컨테이너의 사용자 정의 고유 이름 | 예 | 예 | 예 | 예 | 예 |
|indexingPolicy | 사용자 구성 가능 | 인덱스 경로, 인덱스 유형 및 인덱스 모드를 변경 하는 기능을 제공 합니다. | 예 | 아니요 | 아니요 | 아니요 | 예 |
|timeToLive | 사용자 구성 가능 | 설정 된 기간 후에 컨테이너에서 항목을 자동으로 삭제 하는 기능을 제공 합니다. 자세한 내용은 ttl ( [Time To Live](time-to-live.md))을 참조 하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |
|changeFeedPolicy | 사용자 구성 가능 | 컨테이너의 항목에 대한 변경 내용을 읽는 데 사용됩니다. 자세한 내용은 [변경 피드](change-feed.md)를 참조 하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |
|uniqueKeyPolicy | 사용자 구성 가능 | 논리 파티션에서 하나 이상의 값에 대 한 고유성을 보장 하는 데 사용 됩니다. 자세한 내용은 [Unique key 제약 조건](unique-keys.md)을 참조 하세요. | 예 | 아니요 | 아니요 | 아니요 | 예 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너에서 작업

Azure Cosmos 컨테이너는 Azure Cosmos Api를 사용 하는 경우 다음 작업을 지원 합니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 데이터베이스에 컨테이너 열거 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 읽기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 새 컨테이너 만들기 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 업데이트 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |
| 컨테이너 삭제 | 예 | 예 | 예 | 예 | 해당 없음 | 해당 없음 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 항목

사용하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드나 에지를 나타낼 수 있습니다. 다음 표에서는 Azure Cosmos 항목에 대 한 API 별 엔터티 매핑을 보여 줍니다.

| Cosmos 엔터티 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 항목 | 항목 | 행 | 문서 | 노드 또는에 지 | 항목 |

### <a name="properties-of-an-item"></a>항목의 속성

모든 Azure Cosmos 항목에는 다음과 같은 시스템 정의 속성이 있습니다. 사용 하는 API에 따라 일부는 직접 노출 되지 않을 수도 있습니다.

| 시스템 정의 속성 | 시스템 생성 또는 사용자 구성 가능| 용도 | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_없앨 | 시스템 생성 | 항목의 고유 식별자 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_etag | 시스템 생성 | 낙관적 동시성 제어에 사용되는 엔터티 태그 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_터미널 | 시스템 생성 | 항목의 마지막 업데이트에 대 한 타임 스탬프 | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|\_자체 | 시스템 생성 | 항목의 주소 지정 가능 URI | 예 | 아니요 | 아니요 | 아니요 | 아니요 |
|id | 여기서는 | 논리적 파티션의 사용자 정의 고유 이름입니다. | 예 | 예 | 예 | 예 | 예 |
|임의의 사용자 정의 속성 | 사용자 정의 | API 기본 표현으로 표현 되는 사용자 정의 속성 (JSON, BSON 및 CQL 포함) | 예 | 예 | 예 | 예 | 예 |

> [!NOTE]
> 속성의 고유성 `id` 은 각 논리 파티션 내 에서만 적용 됩니다. 여러 문서에는 `id` 파티션 키 값이 서로 다른 동일한 속성이 있을 수 있습니다.

### <a name="operations-on-items"></a>항목에 대한 작업

Azure Cosmos 항목은 다음 작업을 지원 합니다. Azure Cosmos Api를 사용 하 여 작업을 수행할 수 있습니다.

| 작업(Operation) | Azure CLI | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | 테이블 API |
| --- | --- | --- | --- | --- | --- | --- |
| 삽입, 바꾸기, 삭제, Upsert, 읽기 | 아니요 | 예 | 예 | 예 | 예 | 예 |

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리 하는 방법을 알아봅니다.

* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](how-to-configure-firewall.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

---
title: Azure Cosmos DB에서 분할
description: Azure Cosmos DB 분할 및 파티션 키를 선택할 때의 모범 사례와 논리적 파티션을 관리 하는 방법에 대해 알아봅니다.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 1a760b4cedad5e43a2ef9f186162675aaf6d5ea5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234182"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할

Azure Cosmos DB는 분할을 사용 하 여 응용 프로그램의 성능 요구에 맞게 데이터베이스의 개별 컨테이너를 확장 합니다. 분할에서 컨테이너의 항목은 *논리적 파티션*이라는 별개의 하위 집합으로 나뉩니다. 논리적 파티션은 컨테이너의 각 항목과 연결 된 *파티션 키* 의 값에 따라 형성 됩니다. 논리적 파티션의 모든 항목에는 동일한 파티션 키 값이 있습니다.

예를 들어 컨테이너는 항목을 포함 합니다. 각 항목에는 `UserID` 속성에 대 한 고유한 값이 있습니다. 가 `UserID` 컨테이너의 항목에 대 한 파티션 키로 사용 되 고 고유 `UserID` 값이 1000 인 경우 해당 컨테이너에 대해 1000 논리 파티션이 만들어집니다.

항목의 논리적 파티션을 결정 하는 파티션 키 외에도 컨테이너의 각 항목에는 *항목 ID* (논리 파티션 내에서 고유)가 있습니다. 파티션 키와 *항목 ID* 를 결합 하면 항목을 고유 하 게 식별 하는 항목의 *인덱스가*생성 됩니다.

[파티션 키를 선택](partitioning-overview.md#choose-partitionkey) 하는 것은 응용 프로그램의 성능에 영향을 주는 중요 한 결정입니다.

## <a name="managing-logical-partitions"></a>논리 파티션 관리

Azure Cosmos DB는 컨테이너의 확장성 및 성능 요구를 효율적으로 충족 하기 위해 물리적 파티션에서 논리적 파티션 배치를 투명 하 고 자동으로 관리 합니다. 응용 프로그램의 처리량 및 저장소 요구 사항이 증가 하면, Azure Cosmos DB 논리적 파티션을 이동 하 여 더 많은 수의 실제 파티션에 부하를 자동으로 분산 합니다. [실제 파티션에](partition-data.md#physical-partitions)대해 자세히 알아볼 수 있습니다.

Azure Cosmos DB 해시 기반 분할을 사용 하 여 실제 파티션에 논리적 파티션을 분산 합니다. Azure Cosmos DB는 항목의 파티션 키 값을 해시 합니다. 해시 된 결과는 실제 파티션을 결정 합니다. 그런 다음 Azure Cosmos DB는 실제 파티션에서 파티션 키 해시의 키 공간을 균등 하 게 할당 합니다.

트랜잭션 (저장 프로시저 또는 트리거)은 단일 논리적 파티션의 항목에 대해서만 허용 됩니다.

[Azure Cosmos DB 파티션을 관리 하는 방법](partition-data.md)에 대해 자세히 알아볼 수 있습니다. 응용 프로그램을 빌드하거나 실행 하기 위한 내부 세부 정보를 이해 하는 데 필요 하지는 않지만 여기에서 자세한 정보를 확인할 수 있습니다.

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>파티션 키 선택

파티션 키를 선택 하는 것은 Azure Cosmos DB에서 간단 하지만 중요 한 디자인 선택입니다. 파티션 키를 선택한 후에는 해당 키를 그대로 변경할 수 없습니다. 파티션 키를 변경 해야 하는 경우 새 파티션 키를 사용 하 여 새 컨테이너로 데이터를 이동 해야 합니다.

**모든** 컨테이너에 대해 파티션 키는 다음을 수행 해야 합니다.

* 값이 변경 되지 않는 속성 이어야 합니다. 속성이 파티션 키 인 경우 해당 속성의 값을 업데이트할 수 없습니다.
* 높은 카디널리티를 가집니다. 즉, 속성에는 다양 한 값을 사용할 수 있어야 합니다.
* 모든 논리적 파티션에 균등 하 게 분산 요청 단위 () 사용량과 데이터 저장소를 균등 하 게 분산 합니다. 이렇게 하면 실제 파티션에 대해 과도 한 사용량과 저장소 배포가 가능 합니다.

Azure Cosmos DB에서 [다중 항목 ACID 트랜잭션이](database-transactions-optimistic-concurrency.md#multi-item-transactions) 필요한 경우에는 [저장 프로시저 또는 트리거](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)를 사용 해야 합니다. 모든 JavaScript 기반 저장 프로시저와 트리거는 단일 논리 파티션으로 범위가 한정 됩니다.

## <a name="partition-keys-for-read-heavy-containers"></a>읽기 작업이 많은 컨테이너의 파티션 키

대부분의 컨테이너에서 위의 기준은 파티션 키를 선택할 때 고려해 야 할 사항입니다. 그러나 읽기 작업이 많은 컨테이너의 경우 쿼리에 필터로 자주 나타나는 파티션 키를 선택 하는 것이 좋습니다. 필터 조건자에 파티션 키를 포함 하 여 쿼리 [를 관련 실제 파티션으로 효율적으로 라우팅할](how-to-query-container.md#in-partition-query) 수 있습니다.

대부분의 워크 로드 요청이 쿼리이 고 대부분의 쿼리에 동일한 속성에 대 한 같음 필터가 있는 경우이 속성은 좋은 파티션 키를 선택할 수 있습니다. 예를 들어를 필터링 `UserID`하는 쿼리를 자주 실행 하는 경우 파티션 `UserID` 키로를 선택 하면 [파티션 간 쿼리](how-to-query-container.md#avoiding-cross-partition-queries)수가 줄어듭니다.

그러나 컨테이너가 작은 경우 파티션 간 쿼리의 성능 영향에 대해 걱정 하지 않아도 되는 물리적 파티션이 충분 하지 않을 수 있습니다. Azure Cosmos DB에서 대부분의 작은 컨테이너는 하나 또는 두 개의 실제 파티션만 필요 합니다.

컨테이너가 몇 개의 실제 파티션으로 확장 될 수 있는 경우 파티션 간 쿼리를 최소화 하는 파티션 키를 선택 해야 합니다. 다음 중 하나에 해당 하는 경우 컨테이너에 몇 개의 실제 파티션이 필요 합니다.

* 사용자의 컨테이너가 프로 비전 된 3만 이상의 기능을 제공 합니다.
* 컨테이너가 100 g b의 데이터를 저장 합니다.

## <a name="using-item-id-as-the-partition-key"></a>항목 ID를 파티션 키로 사용

컨테이너에 다양 한 범위의 값이 포함 된 속성이 있는 경우 좋은 파티션 키를 선택할 수 있습니다. 이러한 속성의 가능한 한 가지 예는 *항목 ID*입니다. 모든 크기의 작은 읽기-대량 컨테이너 또는 쓰기 작업량이 많은 컨테이너의 경우, *항목 ID* 는 기본적으로 파티션 키에 적합 합니다.

시스템 속성 *항목 ID* 는 Cosmos 컨테이너의 모든 항목에 존재 하도록 보장 됩니다. 항목의 논리적 ID를 나타내는 다른 속성이 있을 수 있습니다. 대부분의 경우에는 *항목 ID*와 동일한 이유로 적합 한 파티션 키를 선택 해야 합니다.

*항목 ID* 는 다음과 같은 이유로 적합 한 파티션 키입니다.

* 가능한 값의 범위는 다양 합니다 (항목당 하나의 고유 *항목 ID* ).
* 항목별 고유 *항목 id* 가 있기 때문에 *항목 id* 를 사용 하 여 매우 많은 작업을 수행 하 고 데이터 저장소를 균등 하 게 분산 합니다.
* *항목 ID*를 알고 있는 경우 항상 항목의 파티션 키를 알 수 있으므로 효율적인 시점 읽기를 쉽게 수행할 수 있습니다.

*항목 ID* 를 파티션 키로 선택 하는 경우 고려해 야 할 몇 가지 사항은 다음과 같습니다.

* *항목 ID* 가 파티션 키 인 경우 전체 컨테이너 전체에서 고유 식별자가 됩니다. 항목 *ID*가 중복 된 항목을 포함할 수 없습니다.
* [실제 파티션이](partition-data.md#physical-partitions)많은 읽기 전용 컨테이너를 사용 하는 경우 쿼리는 *항목 ID*를 사용 하는 같음 필터가 있는 경우 더 효율적입니다.
* 여러 논리 파티션에서 저장 프로시저나 트리거를 실행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 분할 및 수평 확장](partition-data.md)에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로 비전 된 처리량](request-units.md)에 대해 알아봅니다.
* [Azure Cosmos DB의 글로벌 배포](distribute-data-globally.md)에 대해 알아봅니다.

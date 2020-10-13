---
title: Azure Cosmos DB의 분할 및 수평적 크기 조정
description: Azure Cosmos DB에서 분할이 작동 하는 방식, 분할 및 파티션 키를 구성 하는 방법 및 응용 프로그램에 적합 한 파티션 키를 선택 하는 방법에 대해 알아봅니다.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 98cd28e8b770ebfb7ab395fbe7fff16a078e3529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826857"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB의 분할 및 수평적 크기 조정

이 문서에서는 논리적 파티션 및 물리적 파티션 간의 관계를 설명 합니다. 또한 분할에 대 한 모범 사례에 대해 설명 하 고 Azure Cosmos DB에서 수평 확장이 작동 하는 방식에 대 한 자세한 보기를 제공 합니다. 이러한 내부 세부 정보를 이해 하 여 [파티션 키를 선택할](partitioning-overview.md#choose-partitionkey) 필요는 없지만 Azure Cosmos DB의 작동 방식에 대 한 이해를 돕기 위해 설명 했습니다.

## <a name="logical-partitions"></a>논리 파티션

논리적 파티션은 동일한 파티션 키를 가진 항목 집합으로 구성 됩니다. 예를 들어 음식 영양 대 한 데이터가 포함 된 컨테이너에서 모든 항목에는 `foodGroup` 속성이 포함 됩니다. 를 `foodGroup` 컨테이너에 대 한 파티션 키로 사용할 수 있습니다. ,, 등의 특정 값이 있는 항목 그룹은 `foodGroup` `Beef Products` `Baked Products` `Sausages and Luncheon Meats` 고유한 논리 파티션을 형성 합니다. 기본 데이터를 삭제할 때 논리적 파티션을 삭제 하는 것에 대해 걱정할 필요가 없습니다.

또한 논리적 파티션은 데이터베이스 트랜잭션의 범위를 정의 합니다. [스냅숏 격리로 트랜잭션을](database-transactions-optimistic-concurrency.md)사용 하 여 논리적 파티션 내에서 항목을 업데이트할 수 있습니다. 새 항목이 컨테이너에 추가 되 면 시스템에서 새 논리적 파티션을 투명 하 게 만듭니다.

컨테이너의 논리 파티션 수에는 제한이 없습니다. 각 논리 파티션은 최대 20GB의 데이터를 저장할 수 있습니다. 좋은 파티션 키 선택 항목에는 다양 한 값을 사용할 수 있습니다. 예를 들어 모든 항목에 속성이 포함 된 컨테이너에서 `foodGroup` 논리 파티션 내의 데이터는 `Beef Products` 최대 20gb까지 증가할 수 있습니다. 범위가 광범위 한 [파티션 키를 선택](partitioning-overview.md#choose-partitionkey) 하면 컨테이너를 확장할 수 있습니다.

## <a name="physical-partitions"></a>실제 파티션

Azure Cosmos 컨테이너는 데이터와 처리량을 실제 파티션에 분산 하 여 확장 됩니다. 내부적으로 하나 이상의 논리 파티션은 단일 실제 파티션에 매핑됩니다. 대부분의 작은 Cosmos 컨테이너에는 많은 논리적 파티션이 있지만 단일 실제 파티션만 필요 합니다. 논리적 파티션과 달리 실제 파티션은 시스템의 내부 구현 이며 Azure Cosmos DB에 의해 완전히 관리 됩니다.

Cosmos 컨테이너의 실제 파티션 수는 다음에 따라 달라 집니다.

- 프로 비전 된 처리량 양 (각 개별 실제 파티션은 초당 최대 1만 요청 단위 처리량을 제공할 수 있음)
- 총 데이터 저장소 (각 개별 실제 파티션은 최대 50GB까지 저장할 수 있음)

컨테이너의 총 실제 파티션 수에는 제한이 없습니다. 프로 비전 된 처리량 또는 데이터 크기가 증가 함에 따라 Azure Cosmos DB는 기존 파티션을 분할 하 여 새 실제 파티션을 자동으로 만듭니다. 물리적 파티션 분할은 응용 프로그램의 가용성에 영향을 주지 않습니다. 실제 파티션이 분할 된 후 단일 논리적 파티션 내의 모든 데이터는 여전히 동일한 실제 파티션에 저장 됩니다. 물리적 파티션 분할은 단순히 실제 파티션에 논리적 파티션의 새 매핑을 만듭니다.

컨테이너에 대해 프로 비전 된 처리량은 실제 파티션 간에 균등 하 게 분할 됩니다. 요청을 균등 하 게 배포 하지 않는 파티션 키 디자인은 "핫"이 될 수 있는 작은 파티션 하위 집합으로 전달 되는 요청이 너무 많습니다. 핫 파티션은 프로 비전 된 처리량을 비효율적으로 사용 하 여 속도를 제한 하 고 더 높은 비용을 발생 시킬 수 있습니다.

Azure Portal **메트릭 블레이드의** **저장소** 섹션에서 컨테이너의 실제 파티션을 볼 수 있습니다.

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="실제 파티션 수 보기" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

파티션 키로 선택 된이 컨테이너 예제에서는 `/foodGroup` 각각의 세 사각형이 실제 파티션을 나타냅니다. 이미지에서 **파티션 키 범위** 는 실제 파티션과 같습니다. 선택한 실제 파티션에는 세 개의 논리 파티션, 및가 포함 되어 있습니다 `Beef Products` `Vegetable and Vegetable Products` `Soups, Sauces, and Gravies` .

초당 18000 요청 단위의 처리량을 프로 비전 하는 경우 세 개의 실제 파티션이 프로 비전 된 총 처리량의 1/3을 활용할 수 있습니다. 선택한 실제 파티션 내에서 논리적 파티션 키, `Beef Products` 및는 `Vegetable and Vegetable Products` 집합적으로 `Soups, Sauces, and Gravies` 물리적 파티션의 6000 프로 비전 된 r u/s를 활용할 수 있습니다. 프로 비전 된 처리량은 컨테이너의 실제 파티션에 균등 하 게 분할 되므로 [올바른 논리적 파티션 키를 선택](partitioning-overview.md#choose-partitionkey)하 여 처리량 소비를 균등 하 게 분산 하는 파티션 키를 선택 하는 것이 중요 합니다. 논리적 파티션 간에 처리량 소비를 균등 하 게 분산 하는 파티션 키를 선택 하는 경우 실제 파티션의 처리량 사용량이 균형을 유지 하 게 됩니다.

## <a name="replica-sets"></a>복제본 세트

각 실제 파티션은 [*복제본*](global-dist-under-the-hood.md)집합으로도 불리는 복제본 집합으로 구성 됩니다. 각 복제본 집합은 Azure Cosmos 데이터베이스 엔진의 인스턴스를 호스팅합니다. 복제본 세트를 사용 하면 실제 파티션 내에 저장 된 데이터를 지 속성, 고가용성 및 일관성을 유지할 수 있습니다. 실제 파티션을 구성 하는 각 복제본은 파티션의 저장소 할당량을 상속 합니다. 실제 파티션의 모든 복제본은 실제 파티션에 할당 된 처리량을 전체적으로 지원 합니다. Azure Cosmos DB은 복제본 세트를 자동으로 관리 합니다.

가장 작은 Cosmos 컨테이너에는 단일 실제 파티션만 필요 하지만 여전히 4 개 이상의 복제본이 있습니다.

다음 이미지에서는 논리 파티션이 전역적으로 배포된 실제 파티션에 매핑되는 방법을 보여 줍니다.

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="실제 파티션 수 보기" border="false":::

## <a name="next-steps"></a>다음 단계

* [파티션 키를 선택 하는](partitioning-overview.md#choose-partitionkey)방법에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로 비전 된 처리량](request-units.md)에 대해 알아봅니다.
* [Azure Cosmos DB의 글로벌 배포](distribute-data-globally.md)에 대해 알아봅니다.
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.

---
title: Azure Cosmos DB의 분할 및 수평적 크기 조정
description: Azure Cosmos DB에서 분할이 작동하는 방법, 파티션 및 파티션 키를 구성하는 방법 및 응용 프로그램에 적합한 파티션 키를 선택하는 방법에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246618"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB의 분할 및 수평적 크기 조정

이 문서에서는 Azure Cosmos DB의 물리적 파티션과 논리적 파티션을 설명합니다. 또한 크기 조정 및 분할에 대한 모범 사례도 설명합니다. 

## <a name="logical-partitions"></a>논리 파티션

논리 파티션은 동일한 파티션 키가 있는 항목 집합으로 구성됩니다. 예를 들어 모든 항목에 `City` 속성이 포함된 컨테이너에서 `City` 컨테이너의 파티션 키로 사용할 수 있습니다. `London`에 `Paris`대한 `City`특정 값이 있는 항목 그룹은 `NYC`에서 와 같은 고유한 논리 파티션을 형성합니다. 기본 데이터가 삭제될 때 파티션을 삭제하는 것에 대해 걱정할 필요가 없습니다.

Azure Cosmos DB에서 컨테이너는 확장성의 기본 단위입니다. 컨테이너에 추가된 데이터와 컨테이너에 프로비전하는 처리량은 논리 파티션 집합에 걸쳐 자동으로(가로) 분할됩니다. 데이터 및 처리량은 Azure Cosmos 컨테이너에 대해 지정한 파티션 키에 따라 분할됩니다. 자세한 내용은 [Azure Cosmos 컨테이너 만들기를](how-to-create-container.md)참조하십시오.

논리 파티션은 데이터베이스 트랜잭션의 범위도 정의합니다. [스냅숏 격리가 있는 트랜잭션을](database-transactions-optimistic-concurrency.md)사용하여 논리 파티션 내에서 항목을 업데이트할 수 있습니다. 새 항목이 컨테이너에 추가되면 시스템에서 새 논리 파티션이 투명하게 만들어집니다.

## <a name="physical-partitions"></a>실제 파티션

Azure Cosmos 컨테이너는 많은 수의 논리 파티션에 걸쳐 데이터와 처리량을 분산하여 확장됩니다. 내부적으로 하나 이상의 논리 파티션은 [*복제본*](global-dist-under-the-hood.md)집합으로 구성된 실제 파티션에 매핑됩니다. 각 복제본 집합은 Azure Cosmos 데이터베이스 엔진의 인스턴스를 호스팅합니다. 복제본 집합은 물리적 파티션 내에 저장된 데이터를 지속가능하고 가용성이 높으며 일관되게 만듭니다. 물리적 파티션은 최대 저장 및 요청 단위(R)를 지원합니다. 실제 파티션을 구성하는 각 복제본은 파티션의 저장소 할당량을 상속합니다. 실제 파티션의 모든 복제본은 실제 파티션에 할당된 처리량을 집합적으로 지원합니다. 

다음 이미지에서는 논리 파티션이 전역적으로 배포된 실제 파티션에 매핑되는 방법을 보여 줍니다.

![Azure 코스모스 DB 분할을 보여 주는 이미지](./media/partition-data/logical-partitions.png)

컨테이너에 대해 프로비전된 처리량은 실제 파티션 간에 균등하게 분할됩니다. 처리량 요청을 균등하게 배포하지 않는 파티션 키 디자인은 "핫" 파티션을 만들 수 있습니다. 핫 파티션으로 인해 속도 제한이 있고 프로비저닝된 처리량의 비효율적인 사용과 비용이 높아질 수 있습니다.

논리 파티션과 달리 실제 파티션은 시스템의 내부 구현입니다. 실제 파티션의 크기, 배치 또는 개수를 제어할 수 없으며 논리 파티션과 실제 파티션 간의 매핑을 제어할 수 없습니다. 그러나 올바른 논리 파티션 키를 선택하여 논리 파티션 수와 데이터, 워크로드 및 처리량의 [분포를 제어할](partitioning-overview.md#choose-partitionkey)수 있습니다.

## <a name="next-steps"></a>다음 단계

* [파티션 키 를 선택하는](partitioning-overview.md#choose-partitionkey)방법에 대해 알아봅니다.
* Azure [Cosmos DB에서 프로비저닝된 처리량에](request-units.md)대해 알아봅니다.
* Azure [코스모스 DB에서 글로벌 배포에](distribute-data-globally.md)대해 자세히 알아보기
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.

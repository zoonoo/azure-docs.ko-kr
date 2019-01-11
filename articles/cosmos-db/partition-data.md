---
title: Azure Cosmos DB의 분할 및 수평적 크기 조정
description: Azure Cosmos DB에서 분할 작동 방법, 분할 및 파티션 키를 구성하는 방법 및 애플리케이션에 대한 올바른 파티션 키를 선택하는 방법을 알아봅니다.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 38f587fc24478beff3ab236207de3ed8a892c915
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998951"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB의 분할 및 수평적 크기 조정

이 문서에서는 Azure Cosmos DB의 실제 및 논리 파티션과 크기 조정 및 분할에 대한 모범 사례를 설명합니다. 

## <a name="logical-partitions"></a>논리 파티션

논리 파티션은 파티션 키가 동일한 항목 세트로 구성됩니다. 예를 들어 모든 항목에 `City` 속성이 포함된 컨테이너를 고려한 다음, `City`를 컨테이너에 대한 파티션 키로 사용할 수 있습니다. `City`에 대한 특정 값(예: "런던", "파리", "뉴욕" 등)이 있는 항목 그룹은 고유한 논리 파티션을 형성합니다.

Azure Cosmos DB에서 컨테이너는 확장성의 기본 단위입니다. 컨테이너에 추가된 데이터와 컨테이너에 프로비전한 처리량은 논리 파티션 세트에서 자동(수평)으로 분할됩니다. Cosmos 컨테이너에 대해 지정한 파티션 키에 따라 분할됩니다. 자세한 내용은 [Cosmos 컨테이너에 대한 파티션 키를 지정하는 방법](how-to-create-container.md) 문서를 참조하세요.

논리 파티션은 데이터베이스 트랜잭션의 범위를 정의합니다. 스냅숏 격리가 있는 트랜잭션을 사용하여 논리 파티션 내의 항목을 업데이트할 수 있습니다.

새 항목이 컨테이너에 추가되거나 컨테이너에 프로비전된 처리량이 증가하면 시스템에서 새 논리 파티션이 투명하게 만들어집니다.

## <a name="physical-partitions"></a>실제 파티션

Cosmos 컨테이너는 많은 수의 논리 파티션에 데이터와 처리량을 분산하여 크기를 조정합니다. 내부적으로 하나 이상의 논리 파티션이 복제본 세트라고도 하는 일단의 복제본으로 구성된 **실제 파티션**에 매핑됩니다. 각 복제본 세트는 Cosmos 데이터베이스 엔진의 인스턴스를 호스팅합니다. 복제본 세트는 실제 파티션 내에 저장된 데이터를 내구성과 가용성이 높고 일관되게 만듭니다. 실제 파티션은 고정된 최대 스토리지 및 RU 크기를 지원합니다. 실제 파티션을 구성하는 각 복제본은 스토리지 할당량을 상속합니다. 그리고 실제 파티션의 모든 복제본은 실제 파티션에 할당된 처리량을 전체적으로 지원합니다. 다음 이미지에서는 논리 파티션이 전역적으로 배포된 실제 파티션에 매핑되는 방법을 보여 줍니다.

![Azure Cosmos DB 분할](./media/partition-data/logical-partitions.png)

컨테이너에 프로비전된 처리량은 실제 파티션 간에 균등하게 분할됩니다. 따라서 처리량 요청을 균등하게 분산하지 않는 파티션 키 설계에서는 "핫" 파티션을 만들 수 있습니다. 하지만 핫 파티션으로 인해 속도가 제한되고 프로비전된 처리량이 비효율적으로 사용될 수 있습니다.

논리 파티션과 달리 실제 파티션은 시스템의 내부 구현입니다. 논리 파티션과 실제 파티션 간의 크기, 배치, 개수 또는 매핑은 제어할 수 없습니다. 그러나 논리 파티션의 수와 데이터 및 처리량의 배포는 올바른 파티션 키를 선택하여 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 데이터 분할의 개요와 Azure Cosmos DB의 크기 조정 및 분할에 대한 모범 사례를 제공했습니다. 

* [Azure Cosmos DB에서 프로비전된 처리량](request-units.md)에 대한 자세한 정보
* [Azure Cosmos DB에서 글로벌 배포](distribute-data-globally.md)에 대한 자세한 정보
* [파티션 키 선택](partitioning-overview.md#choose-partitionkey)에 대한 자세한 정보
* [Cosmos 컨테이너의 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md)에 대한 자세한 정보
* [Cosmos 데이터베이스의 처리량을 프로비전하는 방법](how-to-provision-database-throughput.md) 알아보기

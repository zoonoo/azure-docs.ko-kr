---
title: Azure Cosmos DB에서 처리량 크기 조정
description: 이 문서에서는 Azure Cosmos DB에서 처리량을 탄력적으로 크기 조정하는 방법을 설명합니다.
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: c5e4720a6b830158581d17bbdcb9aba49e13183b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064494"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB에서 처리량 크기 조정

Azure Cosmos DB에서 프로비전된 처리량은 요청 단위/초(RU/s, 복수: RUs)로 표시됩니다. RU는 다음 이미지에서 표시된 대로 Cosmos 컨테이너에 대한 읽기 및 쓰기 작업의 비용을 모두 측정합니다.

![요청 단위](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Cosmos 컨테이너 또는 Cosmos 데이터베이스에서 RU를 프로비전할 수 있습니다. 컨테이너에서 프로비전된 RU는 해당 컨테이너에서 수행된 작업에 대해 단독으로 사용할 수 있습니다. 데이터베이스에서 프로비전된 RU는 데이터베이스 내의 모든 컨테이너에서 공유됩니다(독점적으로 할당된 RU가 있는 모든 컨테이너 제외).

처리량의 크기를 탄력적으로 조정하기 위해 프로비전된 RU/s를 언제든지 늘리거나 줄일 수 있습니다. 자세한 내용은 [처리량을 프로비전하는 방법](set-throughput.md) 및 Cosmos 컨테이너 및 데이터베이스의 크기를 탄력적으로 조정하는 방법을 참조하세요. 처리량을 전역적으로 크기 조정하기 위해 언제든지 Cosmos 계정에서 영역을 추가하거나 제거할 수 있습니다. 자세한 내용은 [데이터베이스 계정에서 Azure 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요. 여러 영역을 Cosmos 계정과 연결하는 것은 전세계에서 낮은 대기 시간 및 [고가용성](high-availability.md)을 달성하기 위해 많은 시나리오에서 중요합니다.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>프로비전된 처리량을 영역에 분산하는 방법

Cosmos 컨테이너(또는 데이터베이스)에서 'R' RU를 프로비전하는 경우 Cosmos DB를 통해 Cosmos 계정과 연결된 *각* 영역에서 'R' RU를 사용할 수 있습니다. 계정에 새 영역을 추가할 때마다 Cosmos DB는 새로 추가된 영역에서 'R' RU를 자동으로 프로비전합니다. Cosmos 컨테이너에 대해 수행되는 작업은 각 영역에서 'R' RU를 가져오는 것이 보장됩니다. 특정 영역에 RU를 선택적으로 할당할 수 없습니다. Cosmos 컨테이너(또는 데이터베이스)에 대해 프로비전된 RU는 Cosmos 계정과 연결된 모든 영역에 대해 프로비전됩니다.

Cosmos 컨테이너가 'R' RU로 구성되어 있고 Cosmos 계정과 연결된 'N'개의 영역이 있다고 가정합니다.

- Cosmos 계정이 단일 쓰기 영역으로 구성된 경우 컨테이너에서 전역적으로 사용할 수 있는 총 RU는 R x N입니다.

- Cosmos 계정이 여러 쓰기 영역으로 구성된 경우 컨테이너에서 전역적으로 사용할 수 있는 총 RU는 R x (N+1)입니다. 추가 R RU는 영역에서 업데이트 충돌 및 엔트로피 방지 트래픽을 처리하도록 자동으로 프로비전됩니다.

[일관성 모델](consistency-levels.md)에 대한 선택도 처리량에 영향을 줍니다. 제한된 부실 또는 강력한 일관성과 비교하여 세션, 일관된 접두사 및 최종 일관성에 약 2배의 읽기 처리량을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로 다음 문서를 사용하여 처리량을 구성하는 방법을 알아볼 수 있습니다.

* [컨테이너 및 데이터베이스에 대한 처리량 가져오기 및 설정](set-throughput.md) 


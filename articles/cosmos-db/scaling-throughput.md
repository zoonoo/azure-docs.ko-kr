---
title: Azure Cosmos DB에서 처리량 크기 조정
description: 이 문서에서는 Azure Cosmos DB가 Azure Cosmos 계정이 프로비전되는 여러 지역에서 처리량을 확장하는 방법을 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873508"
---
# <a name="globally-scale-provisioned-throughput"></a>전역적으로 프로비전된 처리량 크기 조정 

Azure Cosmos DB에서 프로비저닝된 처리량은 요청 단위/초(RU/s 또는 복수 형식 RU)로 표시됩니다. RU는 다음 이미지에서 표시된 대로 Cosmos 컨테이너에 대한 읽기 및 쓰기 작업의 비용을 모두 측정합니다.

![요청 단위](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Cosmos 컨테이너 또는 Cosmos 데이터베이스에서 RU를 프로비전할 수 있습니다. 컨테이너에 프로비전된 R은 해당 컨테이너에서 수행되는 작업에만 사용할 수 있습니다. 데이터베이스에서 프로비전된 RU는 데이터베이스 내의 모든 컨테이너에서 공유됩니다(독점적으로 할당된 RU가 있는 모든 컨테이너 제외).

탄력적으로 프로비저닝된 처리량을 늘리거나 줄이면 언제든지 프로비저닝된 RU/s를 늘리거나 줄일 수 있습니다. 자세한 내용은 [처리량을 프로비전하고](set-throughput.md) Cosmos 컨테이너 및 데이터베이스를 탄력적으로 확장하는 방법을 참조하십시오. 전역 크기 조정 처리량의 경우 언제든지 Cosmos 계정에서 영역을 추가하거나 제거할 수 있습니다. 자세한 내용은 [데이터베이스 계정에서 Azure 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요. Cosmos 계정과 여러 지역을 연결하는 것은 전 세계의 낮은 대기 시간과 [고가용성을](high-availability.md) 달성하기 위해 많은 시나리오에서 중요합니다.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>프로비전된 처리량을 영역에 분산하는 방법

코스모스 컨테이너(또는 데이터베이스)에 *'R' R을* 프로비전하는 경우 Cosmos DB는 코스모스 계정과 연결된 *각* 지역에서 *'R' R을* 사용할 수 있도록 합니다. 계정에 새 리전을 추가할 때마다 Cosmos DB는 새로 추가된 리전에서 *'R' R을* 자동으로 프로비저닝합니다. Cosmos 컨테이너에 대해 수행되는 작업은 각 지역에서 *'R' R을* 얻을 수 있습니다. 특정 영역에 RU를 선택적으로 할당할 수 없습니다. 코스모스 컨테이너(또는 데이터베이스)에 프로비전된 RUs는 Cosmos 계정과 연결된 모든 지역에 프로비전됩니다.

코스모스 컨테이너가 *'R'* R로 구성되어 있고 코스모스 계정과 연결된 *'N'* 영역이 있다고 가정하면 다음을 수행합니다.

- Cosmos 계정이 단일 쓰기 영역으로 구성된 경우 컨테이너에서 전역적으로 사용할 수 있는 총 RS = *R* x *N*.

- Cosmos 계정이 여러 쓰기 영역으로 구성된 경우 컨테이너에서 전역적으로 사용할 수 있는 총 RS = *R* x(N+1)입니다.*N* 추가 *R* 루는 자동으로 프로비저닝되어 지역 간 업데이트 충돌 및 안티 엔트로피 트래픽을 처리합니다.

[일관성 모델을](consistency-levels.md) 선택하는 것도 처리량에 영향을 줍니다. 더 강력한 일관성 수준(예: *경계가 있는 부실* 또는 *강한* 일관성)에 비해 보다 완화된 일관성 수준(예: *세션,* *일관된 접두사* 및 *최종* 일관성)에 대해 약 2배의 읽기 처리량을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로 컨테이너 또는 데이터베이스에서 처리량을 구성하는 방법을 배울 수 있습니다.

* [컨테이너 및 데이터베이스에 대한 처리량 가져오기 및 설정](set-throughput.md) 


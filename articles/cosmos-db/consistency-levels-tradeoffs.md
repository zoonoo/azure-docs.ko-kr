---
title: Azure Cosmos DB에서 다양한 일관성 수준의 가용성 및 성능 절충 | Microsoft Docs
description: Azure Cosmos DB에서 다양한 일관성 수준의 가용성 및 성능 절충
keywords: 일관성, 성능, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514777"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB에서 다양한 일관성 수준의 가용성 및 성능 절충

고가용성이나 짧은 대기 시간 또는 둘 다를 위해 복제에 의존하는 분산 데이터베이스는 읽기 일관성과 가용성, 대기 시간과 처리량을 근본적으로 절충합니다. Azure Cosmos DB는 두 가지의 강력하고 극단적인 일관성 대신 선택의 범위로 데이터 일관성에 접근합니다. Cosmos DB에서는 개발자가 일관성 범위(가장 강력함에서 가장 약함 순으로 **강력**, **제한된 부실**, **세션**, **일관된 접두사** 및 **최종**)의 잘 정의된 5가지 일관성 모델 중에서 선택할 수 있습니다. 5가지 일관성 모델은 각각 가용성 및 성능 장단점을 제공하고 포괄적인 SLA로 지원됩니다.

## <a name="consistency-levels-and-latency"></a>일관성 수준 및 대기 시간

- 모든 일관성 수준에 대한 **읽기 대기 시간**은 항상 99번째 백분위 수에서 10밀리초 미만을 보장하며 SLA로 지원됩니다. 평균(50번째 백분위 수) 읽기 대기 시간은 일반적으로 2밀리초 이하입니다.

- Cosmos 계정이 여러 지역에 걸쳐 있고 강력한 일관성으로 구성된 경우 외에는, 모든 일관성 수준에 대한 **쓰기 대기 시간**은 항상 99번째 백분위수에서 10밀리초 미만으로 보장됩니다. 이 쓰기 대기 시간은 SLA를 통해 지원됩니다. 평균(50번째 백분위 수) 쓰기 대기 시간은 일반적으로 5밀리초 이하입니다.

- 강력한 일관성으로 구성된 몇 지역의 Cosmos 계정의 경우 **쓰기 대기 시간**은 99번째 백분위수에서 < (2 * 왕복 시간/RTT) + 10밀리초 미만으로 보장됩니다. RTT는 Cosmos 계정과 연결된 두 영역 중 가장 먼 영역 사이를 말합니다. 정확한 RTT 대기 시간은 속도의 광원 거리 함수와 정확한 Azure 네트워킹 토폴로지입니다. Azure 네트워킹은 두 Azure 지역 간에 RTT에 대한 대기 시간 SLA를 제공하지 않습니다. Cosmos DB 복제 대기 시간은 사용자 Cosmos 계정의 Azure Portal에 표시되므로 사용자가 자신의 Cosmos 계정에 관련된 다양한 지역 간 복제 대기 시간을 모니터링할 수 있습니다.

## <a name="consistency-levels-and-throughput"></a>일관성 수준 및 처리량

- 동일한 요청 단위 수의 경우 세션, 일관된 접두사 및 최종 일관성 수준은 강력하고 제한된 부실에 비해 약 2배의 읽기 처리량을 제공합니다.

- 지정된 쓰기 작업의 유형의 경우(예: 삽입, 바꾸기, upsert, 삭제 등) 요청 단위에 대한 쓰기 처리량은 모든 일관성 수준에 대해 동일합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 글로벌 배포 및 분산 시스템의 일반적인 일관성 절충에 대해 알아볼 수 있습니다.

* [최신 분산 데이터베이스 시스템 디자인의 일관성 절충](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [고가용성](high-availability.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

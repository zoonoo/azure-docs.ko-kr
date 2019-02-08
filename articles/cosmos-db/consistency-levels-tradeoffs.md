---
title: Azure Cosmos DB에서 다양한 일관성 수준의 가용성 및 성능 절충
description: Azure Cosmos DB에서 다양한 일관성 수준의 가용성 및 성능 절충
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: f56b1b06e8be46c1a111e920cfb98bc58d4f6636
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466866"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>일관성, 가용성 및 성능의 장단점 

고가용성, 낮은 대기 시간 또는 둘 다를 위해 복제에 의존하는 분산 데이터베이스는 절충을 해야 합니다. 읽기 일관성과 가용성, 대기 시간 및 처리량의 절충이 필요합니다. 

Azure Cosmos DB는 선택 사항 스펙트럼으로 데이터 일관성에 접근합니다. 이 접근 방식에는 두 가지의 극단적인 강력한 최종 일관성보다 많은 옵션이 포함됩니다. 일관성 스펙트럼의 잘 정의된 5가지 모델 중에서 선택할 수 있습니다. 해당 모델은 가장 강력한 모델부터 가장 약한 모델 순으로 다음과 같습니다.

- 강력 
- 제한된 부실 
- 세션 
- 일관적인 접두사 
- 최종 

각 모델은 가용성과 성능의 절충을 제공하며 포괄적인 SLA로 지원됩니다.

## <a name="consistency-levels-and-latency"></a>일관성 수준 및 대기 시간

- 모든 일관성 수준에 대한 읽기 대기 시간은 항상 99번째 백분위 수에서 10밀리초 미만을 보장합니다. 이 읽기 대기 시간은 SLA로 지원됩니다. 50번째 백분위 수의 평균 읽기 대기 시간은 일반적으로 2밀리초 이하입니다. 여러 Azure 지역에 걸쳐 있으며 강력한 일관성을 사용하여 구성된 Azure Cosmos 계정은 이 보장의 예외입니다.

-  나머지 일관성 수준에 대한 쓰기 대기 시간은 항상 99번째 백분위 수에서 10밀리초 미만을 보장합니다. 이 쓰기 대기 시간은 SLA를 통해 지원됩니다. 50번째 백분위 수의 평균 쓰기 대기 시간은 일반적으로 5밀리초 이하입니다.

일부 Azure Cosmos 계정에는 강력한 일관성을 사용하여 구성된 여러 Azure 지역이 있을 수 있습니다. 이 경우 쓰기 대기 시간은 99번째 백분위수에서 2회 미만의 RTT(왕복 시간)에 10밀리초를 더한 시간 미만으로 보장됩니다. 가장 먼 두 Azure 지역 간의 RTT가 Azure Cosmos 계정과 연결됩니다. Azure Cosmos 계정과 연결된 가장 먼 두 Azure 지역 간의 RTT와 같습니다. 이 옵션은 현재 미리 보기로 제공되고 있습니다. 

정확한 RTT 대기 시간은 광속 거리와 Azure 네트워킹 토폴로지의 함수입니다. Azure 네트워킹은 두 Azure 지역 간의 RTT에 대해 대기 시간 SLA를 제공하지 않습니다. Azure Cosmos 계정의 경우 Azure Portal에 복제 대기 시간이 표시됩니다. Azure Portal을 사용하여 계정과 연결된 다양한 Azure 지역 간의 복제 대기 시간을 모니터링할 수 있습니다.

## <a name="consistency-levels-and-throughput"></a>일관성 수준 및 처리량

- 동일한 요청 단위 수의 경우 세션, 일관된 접두사 및 최종 일관성 수준은 강력하고 제한된 부실에 비해 약 2배의 읽기 처리량을 제공합니다.

- 지정된 쓰기 작업 유형(예: 삽입, 바꾸기, upsert, 삭제 등)의 경우 요청 단위에 대한 쓰기 처리량은 모든 일관성 수준에서 동일합니다.

## <a name="next-steps"></a>다음 단계

전역 분산 및 분산 시스템의 일반적인 일관성 절충에 대해 알아봅니다. 다음 문서를 참조하세요.

* [최신 분산 데이터베이스 시스템 디자인의 일관성 절충](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [고가용성](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

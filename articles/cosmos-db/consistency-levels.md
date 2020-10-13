---
title: Azure Cosmos DB의 일관성 수준
description: Azure Cosmos DB에는 최종 일관성, 가용성 및 대기 시간을 절충하여 조정하는 데 유용한 5가지 일관성 수준이 있습니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 27c1a896d25a0db00ff5f263d949f6657a658e3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567205"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준은 무엇입니까?

고가용성이나 짧은 대기 시간 또는 둘 다를 위해 복제에 의존하는 분산 데이터베이스는 읽기 일관성과 가용성, 대기 시간과 처리량을 근본적으로 절충합니다. 가장 상업적으로 사용할 수 있는 분산 된 데이터베이스는 개발자에 게 *강력한* 일관성과 *최종* 일관성 모델 중에서 선택할 수 있도록 합니다. 강력한 일관성 모델의 선형화 가능성는 데이터 프로그래밍의 골드 표준입니다. 그러나 더 높은 쓰기 대기 시간 (안정적인 상태)과 가용성 감소 (오류 중)의 가격이 추가 됩니다. 반면, 최종 일관성은 고가용성과 향상 된 성능을 제공 하지만 응용 프로그램을 프로그래밍 하는 데 사용 하기 어렵습니다.

Azure Cosmos DB는 두 가지의 극단적인 일관성 모델이 아니라 선택 사항 스펙트럼으로 데이터 일관성에 접근합니다. 개발자는 이러한 옵션을 사용 하 여 고가용성 및 성능과 관련 하 여 정밀 하 게 선택 하 고 세분화 된 균형을 유지할 수 있습니다.

Azure Cosmos DB를 통해 개발자는 일관성 스펙트럼에 대해 잘 정의 된 5 가지 일관성 수준에서 선택할 수 있습니다. 이러한 수준에는 *강력*, *제한 된 부실*, *세션*, *일관 된 접두사*및 *최종* 일관성이 포함 됩니다. 수준은 잘 정의 되 고 직관적 이며 특정 실제 시나리오에 사용할 수 있습니다. 각 수준은 [가용성 및 성능 장단점](consistency-levels-tradeoffs.md) 을 제공 하며 sla를 통해 지원 됩니다. 다음 이미지는 다양 한 일관성 수준을 스펙트럼으로 보여 줍니다.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="스펙트럼으로 일관성" border="false" :::

일관성 수준은 지역에 구애 받지 않으며 읽기 및 쓰기가 처리 되는 지역, Azure Cosmos 계정에 연결 된 지역 수 또는 계정이 단일 또는 다중 쓰기 지역으로 구성 되었는지 여부에 관계 없이 모든 작업에 대해 보장 됩니다.

## <a name="scope-of-the-read-consistency"></a>읽기 일관성 범위

읽기 일관성은 논리적 파티션 내에서 범위가 지정 된 단일 읽기 작업에 적용 됩니다. 읽기 작업은 원격 클라이언트 또는 저장 프로시저에서 실행할 수 있습니다.

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

언제든지 Azure Cosmos 계정의 기본 일관성 수준을 구성할 수 있습니다. 계정에 구성 된 기본 일관성 수준은 해당 계정의 모든 Azure Cosmos 데이터베이스 및 컨테이너에 적용 됩니다. 컨테이너 또는 데이터베이스에 대해 실행된 모든 읽기 및 쿼리는 기본적으로 지정된 일관성 수준을 사용합니다. 자세히 알아보려면 [기본 일관성 수준을 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)하는 방법을 참조하세요. 특정 요청에 대 한 기본 일관성 수준을 재정의할 수도 있습니다. 자세한 내용은 [기본 일관성 수준을 재정의](how-to-manage-consistency.md?#override-the-default-consistency-level) 하는 방법 문서를 참조 하세요.

## <a name="guarantees-associated-with-consistency-levels"></a>일관성 수준과 연결된 보장

Azure Cosmos DB에서 제공하는 포괄적인 SLA는 읽기 요청의 100%가 선택한 일관성 수준의 일관성 보장을 충족하도록 합니다. 일관성 수준과 연결된 모든 일관성 보증이 충족되면 읽기 요청이 일관성 SLA를 충족합니다. TLA + 사양 언어를 사용 하 Azure Cosmos DB에서 5 가지 일관성 수준에 대 한 정확한 정의는 [Azure-Cosmos](https://github.com/Azure/azure-cosmos-tla) GitHub 리포지토리에서 제공 됩니다.

5가지 일관성 수준의 의미 체계는 여기에 설명되어 있습니다.

- **Strong**: 강력한 일관성은 선형화 가능성 보장을 제공 합니다. 선형화 가능성은 요청을 동시에 처리 하는 것을 의미 합니다. 읽기를 통해 항목의 최신 커밋된 버전 반환이 보장됩니다. 클라이언트는 커밋되지 않은 쓰기 또는 부분 쓰기를 볼 수 없습니다. 사용자는 항상 최신 커밋 쓰기의 읽기가 보장됩니다.

  다음 그림은 악기 노트와의 강력한 일관성을 보여 줍니다. "미국 서 부 2" 지역에 데이터를 쓴 후 다른 지역의 데이터를 읽으면 최신 값을 얻게 됩니다.

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="스펙트럼으로 일관성" 외부의 총 글로벌 주문을 제공 합니다. 클라이언트에서 쓰기를 허용 하는 지역 내에서 읽기 작업을 수행 하는 경우, 제한 된 부실 일관성을 통해 제공 되는 보장은 강력한 일관성을 통해 보장 되는 것과 동일 합니다.

부실 기간 내에서 제한 된 부실은 다음과 같은 일관성을 보장 합니다.

- 단일 쓰기 지역이 있는 계정에 대해 동일한 지역의 클라이언트에 대 한 일관성 = 강함
- 단일 쓰기 지역이 있는 계정에 대해 서로 다른 지역의 클라이언트에 대 한 일관성 = 일관적인 접두사
- 여러 쓰기 지역이 있는 계정에 대해 단일 지역에 쓰는 클라이언트의 일관성 = 일치 하는 접두사
- 여러 쓰기 지역이 있는 계정에 대해 다른 지역에 쓰는 클라이언트의 일관성 = 최종

  제한 된 부실은 일반적으로 낮은 쓰기 대기 시간을 예상 하지만 총 글로벌 주문 보증이 필요한 전 세계적으로 분산 된 응용 프로그램에 의해 선택 됩니다. 제한 된 부실은 그룹 공동 작업 및 공유, 주식 시세, 게시-구독/큐 등을 갖춘 응용 프로그램에 적합 합니다. 다음 그림은 음악 메모와의 제한 된 부실 일관성을 보여 줍니다. "미국 서 부 2" 지역에 데이터를 쓴 후 "미국 동부 2" 및 "오스트레일리아 동부" 지역은 구성 된 최대 지연 시간 또는 최대 작업을 기반으로 작성 된 값을 읽습니다.

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="스펙트럼으로 일관성" 세션을 가정 하거나 여러 작성기에 대해 세션 토큰을 공유 하는 것으로 가정 합니다.

쓰기를 수행 하는 세션 외부의 클라이언트는 다음과 같은 보증을 확인할 수 있습니다.

- 단일 쓰기 지역이 있는 계정에 대해 동일한 지역의 클라이언트에 대 한 일관성 = 일관적인 접두사
- 단일 쓰기 지역이 있는 계정에 대해 서로 다른 지역의 클라이언트에 대 한 일관성 = 일관적인 접두사
- 여러 쓰기 지역이 있는 계정에 대해 단일 지역에 쓰는 클라이언트의 일관성 = 일치 하는 접두사
- 여러 쓰기 지역이 있는 계정에 대 한 여러 지역에 쓰는 클라이언트의 일관성 = 최종

  세션 일관성은 단일 지역 뿐만 아니라 전 세계적으로 분산 된 응용 프로그램 모두에서 가장 널리 사용 되는 일관성 수준입니다. 최종 일관성과 유사 하 게 쓰기 대기 시간, 가용성 및 읽기 처리량을 제공 하지만, 사용자의 컨텍스트에서 작동 하도록 작성 된 응용 프로그램의 요구에 맞는 일관성 보장도 제공 합니다. 다음 그림은 악기 노트와의 세션 일관성을 보여 줍니다. "미국 서 부 2 작성기" 및 "미국 서 부 2 판독기"는 동일한 세션 (세션 A)을 사용 하 고 있으므로 동일한 데이터를 동시에 읽을 수 있습니다. "오스트레일리아 동부" 지역은 "세션 B"를 사용 하는 반면 나중에 쓰기와 동일한 순서로 데이터를 받습니다.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="스펙트럼으로 일관성":::

- **일관적인 접두사**: 반환 되는 업데이트에는 간격이 없는 모든 업데이트의 일부 접두사가 포함 됩니다. 일관 된 접두사 일관성 수준에 따라 읽기가 잘못 된 쓰기를 표시 하지 않습니다.

쓰기를 순서 대로 수행 `A, B, C` 하는 경우 클라이언트는, 또는 중 하나를 볼 `A` `A,B` `A,B,C` 뿐만 아니라 또는와 같이 순서가 잘못 된 순열은 표시 하지 않습니다 `A,C` `B,A,C` . 일관 된 접두사는 최종 일관성과 비교할 수 있는 쓰기 대기 시간, 가용성 및 읽기 처리량을 제공 하지만 순서가 중요 한 시나리오의 요구 사항에 적합 한 주문 보장도 제공 합니다. 

다음은 일관 된 접두사에 대 한 일관성 보장입니다.

- 단일 쓰기 지역이 있는 계정에 대해 동일한 지역의 클라이언트에 대 한 일관성 = 일관적인 접두사
- 단일 쓰기 지역이 있는 계정에 대해 서로 다른 지역의 클라이언트에 대 한 일관성 = 일관적인 접두사
- 여러 쓰기 지역이 있는 계정에 대해 단일 지역에 쓰는 클라이언트의 일관성 = 일관적인 접두사
- 여러 쓰기 지역이 있는 계정에 대 한 여러 지역에 쓰는 클라이언트의 일관성 = 최종

다음 그림은 음악 메모와의 일관성 접두사 일관성을 보여 줍니다. 모든 지역에서 읽기는 순서가 잘못 된 쓰기를 표시 하지 않습니다.

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="스펙트럼으로 일관성":::

- **최종**: 읽기에 대 한 순서가 보장 되지 않습니다. 추가 쓰기가 없으면 복제본이 결과적으로 수렴합니다.  
최종 일관성은 클라이언트에서 이전에 읽은 값 보다 오래 된 값을 읽을 수 있기 때문에 가장 약한 형태의 일관성입니다. 최종 일관성은 응용 프로그램에서 순서를 보장 하지 않아도 되는 경우에 적합 합니다. 예를 들면, 좋아요, 좋아요 또는 비 스레드된 주석의 개수가 포함 됩니다. 다음 그림은 악기 노트와의 최종 일관성을 보여 줍니다.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="스펙트럼으로 일관성":::

## <a name="additional-reading"></a>추가 자료

일관성 개념에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Cosmos DB에서 제공하는 5가지 일관성 수준에 대한 고급 TLA+ 사양](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry에서 야구 (비디오)를 통해 복제 된 데이터 일관성을 설명 합니다.](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry에서 야구 (백서)를 통해 복제 된 데이터 일관성을 설명 했습니다.](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [약하게 일관된 복제 데이터에 대한 세션 보장](https://dl.acm.org/citation.cfm?id=383631)
- [최신 분산 데이터베이스 시스템 디자인의 일관성 절충: CAP는 스토리의 일부일 뿐입니다.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [최종 일관성 - 재고되었습니다.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 일관성 수준에 대한 자세한 내용은 다음 문서를 참조하세요.

- [애플리케이션에 대한 올바른 일관성 수준 선택](consistency-levels-choosing.md)
- [Azure Cosmos DB API 간 일관성 수준](consistency-levels-across-apis.md)
- [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
- [기본 일관성 수준 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [기본 일관성 수준 재정의](how-to-manage-consistency.md#override-the-default-consistency-level)

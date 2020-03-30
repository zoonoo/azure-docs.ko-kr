---
title: Azure Cosmos DB의 일관성 수준
description: Azure Cosmos DB에는 최종 일관성, 가용성 및 대기 시간을 절충하여 조정하는 데 유용한 5가지 일관성 수준이 있습니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530260"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준

고가용성이나 짧은 대기 시간 또는 둘 다를 위해 복제에 의존하는 분산 데이터베이스는 읽기 일관성과 가용성, 대기 시간과 처리량을 근본적으로 절충합니다. 대부분의 상용 분산 데이터베이스는 개발자에게 *강력한* 일관성과 *최종* 일관성이라는 두 가지 극단적인 일관성 모델 중에서 선택하도록 요청합니다. 선형화 성 또는 강력한 일관성 모델은 데이터 프로그래밍 가능성의 표준입니다. 그러나 대기 시간이 증가하고(정상 상태) 가용성이 저하됩니다(오류 발생 시). 반면에 최종 일관성은 더 높은 가용성과 더 나은 성능을 제공하지만 응용 프로그램을 프로그래밍하기가 어렵습니다. 

Azure Cosmos DB는 두 가지의 극단적인 일관성 모델이 아니라 선택 사항 스펙트럼으로 데이터 일관성에 접근합니다. 강력한 일관성과 최종 일관성은 스펙트럼의 끝에 있지만 스펙트럼을 따라 많은 일관성 선택이 있습니다. 개발자는 이러한 옵션을 사용하여 고가용성 및 성능과 관련하여 정확한 선택과 세분화된 장단점을 선택할 수 있습니다. 

Azure Cosmos DB를 사용하여 개발자는 일관성 스펙트럼에서 5개의 잘 정의된 일관성 모델에서 선택할 수 있습니다. 가장 강한에서 더 편안한에 이르기까지, 모델은 *강한,* *경계 부실,* *세션,* *일관된 접두사*및 *최종* 일관성을 포함한다. 모델은 잘 정의되고 직관적이며 특정 실제 시나리오에 사용할 수 있습니다. 각 모델은 [가용성 및 성능 절충을](consistency-levels-tradeoffs.md) 제공하며 SLA가 뒷받침합니다. 다음 이미지는 스펙트럼으로 서로 다른 일관성 수준을 보여 줍니다.

![스펙트럼으로 일관성](./media/consistency-levels/five-consistency-levels.png)

일관성 수준은 지역에 구애받지 않으며 읽기 및 쓰기가 제공되는 지역, Azure Cosmos 계정과 연결된 지역 수 또는 계정이 단일 계정으로 구성되어 있는지 여부에 관계없이 모든 작업에 대해 보장됩니다. 또는 여러 쓰기 영역.

## <a name="scope-of-the-read-consistency"></a>읽기 일관성 범위

읽기 일관성은 파티션 키 범위 또는 논리 파티션 내의 단일 읽기 작업 범위에 적용됩니다. 읽기 작업은 원격 클라이언트 또는 저장 프로시저에서 실행할 수 있습니다.

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

언제든지 Azure Cosmos 계정의 기본 일관성 수준을 구성할 수 있습니다. 계정에 구성된 기본 일관성 수준은 해당 계정의 모든 Azure Cosmos 데이터베이스 및 컨테이너에 적용됩니다. 컨테이너 또는 데이터베이스에 대해 실행된 모든 읽기 및 쿼리는 기본적으로 지정된 일관성 수준을 사용합니다. 자세히 알아보려면 [기본 일관성 수준을 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)하는 방법을 참조하세요.

## <a name="guarantees-associated-with-consistency-levels"></a>일관성 수준과 연결된 보장

Azure Cosmos DB에서 제공하는 포괄적인 SLA는 읽기 요청의 100%가 선택한 일관성 수준의 일관성 보장을 충족하도록 합니다. 일관성 수준과 연결된 모든 일관성 보증이 충족되면 읽기 요청이 일관성 SLA를 충족합니다. TLA+ 사양 언어를 사용하는 Azure Cosmos DB의 5가지 일관성 수준에 대한 정확한 정의는 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 리포지토리에 제공됩니다.

5가지 일관성 수준의 의미 체계는 여기에 설명되어 있습니다.

- **강한**: 강한 일관성은 선형성을 보장합니다. 선형화 가능성은 동시에 요청을 게재하는 것을 말합니다. 읽기를 통해 항목의 최신 커밋된 버전 반환이 보장됩니다. 클라이언트는 커밋되지 않은 쓰기 또는 부분 쓰기를 볼 수 없습니다. 사용자는 항상 최신 커밋 쓰기의 읽기가 보장됩니다.

  다음 그래픽은 음표와의 강한 일관성을 보여줍니다. 데이터가 "미국 서부 2" 리전에 기록된 후 다른 리전의 데이터를 읽을 때 가장 최근 값을 얻게 됩니다.

  ![video](media/consistency-levels/strong-consistency.gif)

- **경계 부실**: 읽기는 일관된 접두사 보장을 준수하도록 보장됩니다. 읽기는 항목의 대부분 *"K"* 버전(즉, "업데이트")이나 *"T"* 시간 간격으로 작성보다 지연될 수 있습니다. 즉, 경계가 지정된 부실을 선택하면 "부실"을 두 가지 방법으로 구성할 수 있습니다. 

  * 항목의 버전*수(K)*
  * 읽기가 쓰기보다 뒤쳐질 수 있는 시간*간격(T)* 

  제한된 부실은 "부실 창" 내부를 제외하고 전체 전역 순서를 제공합니다. 부실 창 내부 및 외부 지역에 단조 읽기 보증이 존재합니다. 강력한 일관성은 경계가 있는 부실에서 제공하는 의미와 동일합니다. 부실 기간은 영(0)입니다. 제한된 부실을 시간 지연 원자성이라고도 합니다. 클라이언트가 쓰기를 허용하는 리전 내에서 읽기 작업을 수행하는 경우 바인딩된 부실 일관성에서 제공하는 보장은 강력한 일관성에 의해 보장된 보장과 동일합니다.

  경계 가없는 부실은 쓰기 시간이 적지만 전체 글로벌 주문 보증이 필요한 전역 분산 응용 프로그램에서 자주 선택됩니다. 경계 부실은 그룹 공동 작업 및 공유, 주식 시세, 게시 - 구독 / 큐 등을 갖춘 응용 프로그램에 적합합니다. 다음 그래픽은 음표와 경계 부실 일관성을 보여줍니다. 데이터가 "미국 서부 2" 지역에 기록된 후 "미국 동부 2" 및 "오스트레일리아 동부" 지역은 구성된 최대 지연 시간 또는 최대 작업을 기준으로 작성된 값을 읽습니다.

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **세션**: 단일 클라이언트 세션 내에서 읽기는 일관된 접두사 (단일 "기록기"세션 가정), 단조로운 읽기, 단조로운 쓰기, 읽기 - 당신의 쓰기 및 쓰기 - 팔로우 - 읽기 보장을 준수하도록 보장됩니다. 쓰기를 수행하는 세션 외부의 클라이언트는 최종 일관성을 볼 수 있습니다.

  세션 일관성은 단일 리전과 전역적으로 분산된 응용 프로그램 모두에 널리 사용되는 일관성 수준입니다. 쓰기 대기 시간, 가용성 및 읽기 처리량을 최종 일관성과 비교할 수 있지만 사용자의 컨텍스트에서 작동하도록 작성된 응용 프로그램의 요구에 맞는 일관성 보장도 제공합니다. 다음 그래픽은 음표와의 세션 일관성을 보여 줍니다. "미국 서부 2" 지역과 "미국 동부 2" 리전은 동일한 세션(세션 A)을 사용하므로 둘 다 동시에 데이터를 읽습니다. "오스트레일리아 동부" 리전은 "세션 B"를 사용하지만 나중에 는 쓰기와 동일한 순서로 데이터를 수신합니다.

  ![video](media/consistency-levels/session-consistency.gif)

- **일관된 접두사**: 반환되는 업데이트에는 간격이 없는 모든 업데이트의 일부 접두사가 포함되어 있습니다. 일관된 접두사 일관성 수준은 읽기가 순서가 바도가 없는 쓰기를 볼 수 없다는 것을 보장합니다.

  쓰기가 `A, B, C` 순서로 수행된 경우 클라이언트는 `A`, `A,B` 또는 `A,B,C`를 볼 수 있지만 `A,C` 또는 `B,A,C`처럼 잘못된 것은 볼 수 없습니다. 일관된 접두사는 쓰기 대기 시간, 가용성 및 읽기 처리량을 최종 일관성과 비교할 수 있는 정보제공뿐만 아니라 주문이 중요한 시나리오의 요구에 맞는 순서 보장도 제공합니다. 다음 그래픽은 음표와의 일관성 접두사 일관성을 보여 줍니다. 모든 리전에서 읽기는 순서가 바되지 않습니다.

  ![video](media/consistency-levels/consistent-prefix.gif)

- **최종**: 읽기에 대한 주문 보장은 없습니다. 추가적인 쓰기가 없는 경우 복제본이 결국 통합됩니다.  
클라이언트가 이전에 읽은 값보다 오래된 값을 읽을 수 있기 때문에 최종 일관성은 가장 약한 형태의 일관성입니다. 최종 일관성은 응용 프로그램이 주문 보증을 필요로하지 않는 경우에 이상적입니다. 예를 들어 리트윗, 좋아요 또는 비스레드 댓글 의 수가 있습니다. 다음 그래픽은 음표와의 최종 일관성을 보여줍니다.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>추가 참조 자료

일관성 개념에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Cosmos DB에서 제공하는 5가지 일관성 수준에 대한 고급 TLA+ 사양](https://github.com/Azure/azure-cosmos-tla)
- [더그 테리에 의해 야구 (비디오)를 통해 설명 복제 된 데이터 일관성](https://www.youtube.com/watch?v=gluIh8zd26I)
- [더그 테리에 의해 야구 (백서)를 통해 설명 복제 된 데이터 일관성](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [약하게 일관된 복제 데이터에 대한 세션 보장](https://dl.acm.org/citation.cfm?id=383631)
- [최신 분산 데이터베이스 시스템 디자인의 일관성 절충: CAP는 스토리의 일부일 뿐입니다.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [최종 일관성 - 재고되었습니다.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 일관성 수준에 대한 자세한 내용은 다음 문서를 참조하세요.

* [애플리케이션에 대한 올바른 일관성 수준 선택](consistency-levels-choosing.md)
* [Azure Cosmos DB API 간 일관성 수준](consistency-levels-across-apis.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [기본 일관성 수준 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [기본 일관성 수준 재정의](how-to-manage-consistency.md#override-the-default-consistency-level)


---
title: 신뢰할 수 있는 컬렉션의 트랜잭션 및 잠금 모드
description: Azure Service Fabric 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 트랜잭션 및 잠금.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: d1094462ebabcea1fbead3d5b30fdfb8dda6463a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500285"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric 신뢰할 수 있는 컬렉션의 트랜잭션 및 잠금 모드

## <a name="transaction"></a>트랜잭션

트랜잭션은 하나의 논리적 작업 단위로 수행되는 일련의 작업입니다. 데이터베이스 트랜잭션의 일반적인 [ACID](https://en.wikipedia.org/wiki/ACID) (*원자성*, *일관성*, *격리*, *내구성*) 속성을 보여 주는 것입니다.

* **원자성**: 트랜잭션은 작업의 원자 단위여야 합니다. 즉, 모든 데이터 수정 작업이 수행되거나, 또는 하나도 수행되지 않아야 합니다.
* **일관성**: 완료되면 트랜잭션은 모든 데이터를 일관된 상태로 유지해야 합니다. 모든 내부 데이터 구조는 트랜잭션이 끝날 때 정확해야 합니다.
* **격리**: 동시 트랜잭션에 의한 수정은 다른 동시 트랜잭션과 격리되어야 합니다. [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) 내에서 작업에 사용 되는 격리 수준은 작업을 수행 하는 [IReliableState](/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) 에 의해 결정 됩니다.
* **내구성**: 트랜잭션이 완료되면 그 영향은 영구적으로 시스템에 적용됩니다. 수정은 시스템에 오류가 발생한 경우에도 지속됩니다.

### <a name="isolation-levels"></a>격리 수준

격리 수준은 트랜잭션은 다른 트랜잭션에 의해 수정되지 않도록 격리해야 하는 정도를 정의합니다.
신뢰할 수 있는 컬렉션에서 지원되는 두 격리 수준이 있습니다.

* **반복 가능한 읽기**: 문은 수정되었지만 다른 트랜잭션에서 아직 커밋되지 않은 데이터를 읽을 수 없으며 현재 트랜잭션이 완료될 때까지 다른 트랜잭션은 현재 트랜잭션에서 읽은 데이터를 수정할 수 없음을 지정합니다.
* **Snapshot**: 트랜잭션의 문에서 읽은 데이터가 트랜잭션 시작 시 존재 하는 데이터의 트랜잭션 일치 버전 임을 지정 합니다.
  트랜잭션은 트랜잭션 시작 전에 커밋된 데이터 수정 내용만 인식할 수 있습니다.
  현재 트랜잭션이 시작된 후 다른 트랜잭션에서 수정한 데이터는 현재 트랜잭션에서 실행되는 문에 표시되지 않습니다.
  따라서 트랜잭션의 문이 트랜잭션 시작 당시 커밋된 데이터의 스냅샷을 가져오는 것처럼 보입니다.
  스냅샷은 신뢰할 수 있는 컬렉션 간에 일관됩니다.

신뢰할 수 있는 컬렉션은 트랜잭션을 만들 당시의 작업 및 복제본의 역할에 따라 자동으로 특정 읽기 작업에 사용할 격리 수준을 선택합니다.
다음 테이블에는 신뢰할 수 있는 사전 및 큐 작업에 대한 기본 격리 수준이 나와 있습니다.

| 작업 \ 역할 | 기본 | 보조 |
| --- |:--- |:--- |
| 단일 엔터티 읽기 |반복 가능한 읽기 |스냅샷 |
| 열거형, 개수 |스냅샷 |스냅샷 |

> [!NOTE]
> 단일 엔터티 작업에 대한 일반적인 예제는 `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`입니다.
> 

신뢰할 수 있는 사전 및 신뢰할 수 있는 큐는 모두 *쓰기를 읽도록*지원 합니다.
즉, 특정 트랜잭션 내 모든 쓰기가 동일한 트랜잭션에 속하는 다음 읽기에 표시됩니다.

## <a name="locks"></a>Locks

신뢰할 수 있는 컬렉션의 모든 트랜잭션은 엄격한 2단계 잠금을 구현합니다. 트랜잭션은 중단 또는 커밋으로 인해 종료되어야만 확보한 잠금을 해제합니다.

신뢰할 수 있는 사전은 모든 단일 엔터티 작업에 대해 행 수준 잠금을 사용 합니다.
신뢰할 수 있는 큐와 엄격한 트랜잭션 FIFO 속성의 동시성은 서로 균형을 유지합니다.
신뢰할 수 있는 큐는 `TryPeekAsync` and/or 및 트랜잭션 하나를 한 번에 하나의 트랜잭션으로 허용 하는 작업 수준 잠금을 사용 `TryDequeueAsync` `EnqueueAsync` 합니다.
FIFO를 유지하기 위해 `TryPeekAsync` 또는 `TryDequeueAsync`는 신뢰할 수 있는 큐가 비어 있음을 확인하면 `EnqueueAsync`도 잠급니다.

쓰기 작업은 항상 배타적 잠금을 수행합니다.
읽기 작업의 경우 잠금은 다음과 같은 몇 가지 요인에 따라 달라 집니다.

- 스냅숏 격리를 사용 하 여 수행 된 모든 읽기 작업은 잠금이 필요 하지 않습니다.
- 모든 반복 가능한 읽기 작업은 기본적으로 공유 잠금을 수행합니다.
- 그러나 사용자는 반복 가능한 읽기를 지원하는 모든 읽기 작업에 대해 공유 잠금 대신 업데이트 잠금을 요청할 수 있습니다.
업데이트 잠금에는 이후의 잠재적 업데이트를 위해 여러 트랜잭션이 리소스를 잠글 때 발생하는 일반적인 형태의 교착 상태를 방지하는 데 사용되는 비대칭 잠금입니다.

잠금 호환성 매트릭스는 다음 테이블에서 확인할 수 있습니다.

| 요청 \ 부여 | 없음 | 공유 | 업데이트 | 단독 |
| --- |:--- |:--- |:--- |:--- |
| 공유 |충돌 없음 |충돌 없음 |충돌 |충돌 |
| 업데이트 |충돌 없음 |충돌 없음 |충돌 |충돌 |
| 단독 |충돌 없음 |충돌 |충돌 |충돌 |

신뢰할 수 있는 컬렉션 Api의 시간 제한 인수는 교착 상태 검색에 사용 됩니다.
예를 들어 두 트랜잭션(T1과 T2)은 K1을 읽고 업데이트하려고 합니다.
둘 다 결국 공유된 잠금을 가지게 되기 때문에 교착 상태가 될 수 있습니다.
이 경우 작업 중 하나 또는 둘 다의 시간이 초과 됩니다. 이 시나리오에서 업데이트 잠금은 이러한 교착 상태를 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 알림](service-fabric-reliable-services-notifications.md)
* [Reliable Services 백업 및 복원(재해 복구)](service-fabric-reliable-services-backup-restore.md)
* [신뢰할 수 있는 상태 관리자 구성](service-fabric-reliable-services-configuration.md)
* [신뢰할 수 있는 컬렉션에 대한 개발자 참조](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)

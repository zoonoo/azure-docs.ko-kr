---
title: Redis 용 Azure Cache의 고가용성
description: Redis 고가용성 기능 및 옵션에 대 한 Azure Cache에 대해 알아보기
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: 145be11436eb4d0c4f6b892e5239ccacd838d780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654165"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Redis 용 Azure Cache의 고가용성

Redis 용 Azure Cache에는 고가용성이 기본적으로 제공 됩니다. 고가용성 아키텍처의 목표는 기본 Vm (가상 머신)이 계획 되거나 계획 되지 않은 중단의 영향을 받는 경우에도 관리 되는 Redis 인스턴스가 작동 하 고 있는지 확인 하는 것입니다. 단일 VM에서 Redis을 호스트 하 여 realistic 된 것 보다 훨씬 더 높은 비율의 요금을 제공 합니다.

Redis 용 Azure Cache는 캐시에 대해 *노드*라고 하는 여러 vm을 사용 하 여 고가용성을 구현 합니다. 이는 조정 된 방식의에서 데이터 복제 및 장애 조치 (failover)가 발생 하도록 이러한 노드를 구성 합니다. 또한 Redis 소프트웨어 패치와 같은 유지 관리 작업을 오케스트레이션 합니다. Standard 및 Premium 계층에서는 다양 한 고가용성 옵션을 사용할 수 있습니다.

| 옵션 | Description | 가용성 | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [표준 복제](#standard-replication)| 자동 장애 조치 (failover)를 사용 하 여 단일 데이터 센터 또는 가용성 영역 (AZ)의 이중 노드 복제 구성 | 99.9% |✔|✔|
| [여러 복제본](#multiple-replicas) | 자동 장애 조치 (failover)를 사용 하는 하나 이상의 AZs에서 다중 노드 복제 구성 | 99.95% (영역 중복성 포함) |-|✔|
| [영역 중복](#zone-redundancy) | 자동 장애 조치 (failover)를 사용 하 여 AZs에서 다중 노드 복제 구성 | 99.95% (여러 복제본 포함) |-|✔|
| [지역에서 복제](#geo-replication) | 사용자 제어 장애 조치 (failover)를 사용 하 여 두 지역의 연결 된 캐시 인스턴스 | 99.9% (단일 지역) |-|✔|

## <a name="standard-replication"></a>표준 복제

표준 또는 프리미엄 계층의 Redis 용 Azure 캐시는 기본적으로 Redis 서버 쌍에서 실행 됩니다. 두 서버는 전용 Vm에서 호스팅됩니다. 오픈 소스 Redis를 사용 하면 한 서버에서 데이터 쓰기 요청을 처리할 수 있습니다. 이 서버는 *주* 노드이며 다른 *복제본*입니다. 서버 노드를 프로 비전 한 후 Redis 용 Azure Cache는 주 역할과 복제본 역할을 할당 합니다. 일반적으로 주 노드는 Redis 클라이언트의 읽기 요청 뿐만 아니라 쓰기 서비스를 담당 합니다. 쓰기 작업을 수행 하면 새 키와 키 업데이트가 내부 메모리에 커밋되고 즉시 클라이언트에 응답 합니다. 작업을 복제본으로 비동기적으로 전달 합니다.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="데이터 복제 설정":::
   
>[!NOTE]
>일반적으로 Redis 클라이언트는 모든 읽기 및 쓰기 요청에 대해 Redis cache의 주 노드와 통신 합니다. 특정 Redis 클라이언트는 복제 노드를 읽도록 구성할 수 있습니다.
>
>

Redis cache의 주 노드를 사용할 수 없는 경우 복제본이 자동으로 새 주 노드가 되도록 수준을 올립니다. 이 프로세스를 *장애 조치 (failover)* 라고 합니다. 주 노드를 신속 하 게 복구 하는 경우에는 복제본이 충분히 오랜 시간이 지나면 대기 합니다. 장애 조치 (failover)가 발생 하면 Azure Cache for Redis는 새 VM을 프로 비전 하 고이를 복제 노드로 캐시에 조인 합니다. 복제본은 캐시 데이터의 다른 복사본을 포함 하도록 주 복제본과 전체 데이터 동기화를 수행 합니다.

주 노드는 Redis 소프트웨어 또는 운영 체제 업데이트와 같은 계획 된 유지 관리 작업의 일부로 서비스를 벗어날 수 있습니다. 기본 하드웨어, 소프트웨어 또는 네트워크의 오류와 같은 계획 되지 않은 이벤트로 인해 작동이 중지 될 수도 있습니다. [Redis 용 Azure Cache의 장애 조치 (Failover) 및 패치](cache-failover.md) 는 Redis 장애 조치 (failover) 유형에 대 한 자세한 설명을 제공 합니다. Azure Cache for Redis는 수명 동안 많은 장애 조치 (failover)를 거칩니다. 고가용성 아키텍처는 캐시 내에서 가능한 한 해당 클라이언트에 투명 하 게 이러한 변경을 수행 하도록 설계 되었습니다.

## <a name="multiple-replicas"></a>여러 복제본

>[!NOTE]
>이는 미리 보기로 제공 됩니다.
>
>

Redis 용 Azure Cache는 프리미엄 계층의 추가 복제본 노드를 허용 합니다. [다중 복제본 캐시](cache-how-to-multi-replicas.md) 는 최대 3 개의 복제본 노드를 사용 하 여 구성할 수 있습니다. 복제본이 많을 수록 일반적으로 주 복제본을 백업 하는 추가 노드로 인해 복원 력이 향상 됩니다. 복제본이 많을 경우에도 Redis 인스턴스에 대 한 Azure Cache는 데이터 센터 또는 AZ 전체 중단의 심각한 영향을 받을 수 있습니다. [영역 중복성](#zone-redundancy)과 함께 여러 복제본을 사용 하 여 캐시 가용성을 높일 수 있습니다.

## <a name="zone-redundancy"></a>영역 중복

>[!NOTE]
>이는 미리 보기로 제공 됩니다.
>
>

Redis 용 Azure Cache는 프리미엄 계층에서 영역 중복 구성을 지원 합니다. [영역 중복 캐시](cache-how-to-zone-redundancy.md) 는 동일한 지역에 있는 서로 다른 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview) 에 노드를 둘 수 있습니다. 단일 실패 지점으로 데이터 센터 또는 AZ 중단을 제거 하 고 캐시의 전체 가용성을 높입니다.

다음 다이어그램은 영역 중복 구성을 보여 줍니다.

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="데이터 복제 설정":::
   
Redis 용 Azure Cache는 선택한 AZs에 대해 라운드 로빈 방식으로 영역 중복 캐시의 노드를 배포 합니다. 또한 초기에 기본으로 사용할 노드를 결정 합니다.

영역 중복 캐시는 자동 장애 조치 (failover)를 제공 합니다. 현재 주 노드를 사용할 수 없는 경우 복제본 중 하나가 사용 됩니다. 새 주 노드가 다른 AZ에 있는 경우 응용 프로그램에 더 높은 캐시 응답 시간이 발생할 수 있습니다. AZs는 지리적으로 구분 됩니다. 한 AZ에서 다른 AZ로 전환 하면 응용 프로그램과 캐시가 호스트 되는 위치 간의 물리적인 거리가 변경 됩니다. 이 변경은 응용 프로그램에서 캐시로의 왕복 네트워크 대기 시간에 영향을 줍니다. 추가 대기 시간은 대부분의 응용 프로그램에 허용 되는 범위에 포함 될 것으로 예상 됩니다. 응용 프로그램을 테스트 하 여 영역 중복 캐시에서 제대로 수행할 수 있는지 확인 하는 것이 좋습니다.

## <a name="geo-replication"></a>지역에서 복제

지역에서 복제는 주로 재해 복구를 위해 설계 되었습니다. 다른 Azure 지역에서 Redis 인스턴스에 대 한 Azure Cache를 구성 하 여 기본 캐시를 백업 하는 기능을 제공 합니다. [Redis 용 Azure 캐시에 대 한 지역에서 복제 설정](cache-how-to-geo-replication.md) 에서는 지역에서 복제의 작동 방식에 대 한 자세한 설명을 제공 합니다.

## <a name="next-steps"></a>다음 단계

Redis 고가용성 옵션을 위해 Azure Cache를 구성 하는 방법에 대해 자세히 알아보세요.

* [Redis Premium 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
* [Redis 용 Azure Cache에 복제본 추가](cache-how-to-multi-replicas.md)
* [Redis에 대 한 Azure 캐시에 영역 중복성 사용](cache-how-to-zone-redundancy.md)
* [Redis 용 Azure 캐시에 대 한 지역에서 복제 설정](cache-how-to-geo-replication.md)

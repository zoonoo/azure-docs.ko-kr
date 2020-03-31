---
title: Redis에 대 한 Azure 캐시에 대 한 지역 복제를 설정 하는 방법 | 마이크로 소프트 문서
description: 지리적 지역 간에 Azure Cache for Redis 인스턴스를 복제하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129421"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Redis에 대 한 Azure 캐시에 대 한 지역 복제를 설정 하는 방법

지역 복제는 두 개의 프리미엄 계층 Azure Cache for Redis 인스턴스를 연결하는 메커니즘을 제공합니다. 한 캐시는 기본 연결된 캐시로, 다른 캐시는 보조 연결된 캐시로 선택됩니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다. 이 문서에서는 Redis 인스턴스에 대한 프리미엄 계층 Azure 캐시에 대한 지역 복제를 구성하는 방법을 제공합니다.

## <a name="geo-replication-prerequisites"></a>지역에서 복제 필수 조건

두 캐시 간에 지역 복제를 구성하려면 다음 필수 구성 조건을 충족해야 합니다.

- 두 캐시 모두 [프리미엄 계층](cache-premium-tier-intro.md) 캐시입니다.
- 두 캐시가 모두 동일한 Azure 구독에 있습니다.
- 보조 연결된 캐시는 동일한 캐시 크기이거나 기본 연결된 캐시보다 큰 캐시 크기입니다.
- 두 캐시가 만들어지고 실행 중인 상태입니다.

일부 기능은 지역 복제로 지원되지 않습니다.

- 지속성은 지역 복제를 통해 지원되지 않습니다.
- 두 캐시모두 클러스터링을 사용하도록 설정하고 샤드 수가 같은 경우 클러스터링이 지원됩니다.
- 동일한 VNET의 캐시가 지원됩니다.
- 다른 VNET의 캐시는 주의 사항으로 지원됩니다. 자세한 내용은 [VNET에서 내 캐시와 함께 지역 복제를 사용할 수 있습니까?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

지역 복제를 구성한 후 연결된 캐시 쌍에 다음 제한 사항이 적용됩니다.

- 보조 연결된 캐시는 읽기 전용입니다. 이 캐시에서 읽을 수는 있지만 데이터를 쓸 수 없습니다. 
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 그러나 나중에 지역 복제가 제거되면 복제된 데이터는 보조 연결된 캐시에 남아 있습니다.
- 캐시가 연결된 동안에는 캐시를 [확장할](cache-how-to-scale.md) 수 없습니다.
- 캐시에 클러스터링이 활성화된 경우 [샤드 수를 변경할](cache-how-to-premium-clustering.md) 수 없습니다.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- 두 캐시 중 하나에서 [내보낼](cache-how-to-import-export-data.md#export) 수 있습니다.
- 보조 연결된 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 없습니다.
- 캐시의 연결을 해제할 때까지 연결된 캐시 또는 연결된 캐시를 포함하는 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 캐시가 다른 지역에 있는 경우 네트워크 송신 비용은 여러 지역에서 이동된 데이터에 적용됩니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 기본 및 보조 연결된 캐시 간에는 자동 장애 조치(failover)가 발생하지 않습니다. 클라이언트 응용 프로그램을 장애 조치 하는 방법에 대 한 자세한 내용은 [보조 연결 된 캐시 작업에 장애 는 어떻게 참조?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>지역 복제 링크 추가

1. 지역 복제를 위해 두 개의 캐시를 함께 연결하려면 기본 연결된 캐시로 하려는 캐시의 리소스 메뉴에서 **지리적 복제를** 마우스를 클릭합니다. 다음으로 지리적 복제 블레이드에서 **캐시 복제 링크 추가를** **클릭합니다.**

    ![링크 추가](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 호환 가능한 캐시 목록에서 의도한 보조 캐시의 이름을 **클릭합니다.** 보조 캐시가 목록에 표시되지 않으면 보조 캐시에 대한 [지리적 복제 필수 구성 조건이](#geo-replication-prerequisites) 충족되는지 확인합니다. 지역별로 캐시를 필터링하려면 맵의 영역을 클릭하여 호환 되는 캐시 목록에 해당 캐시만 **표시합니다.**

    ![지역에서 복제 호환되는 캐시](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    컨텍스트 메뉴를 사용하여 연결 프로세스를 시작하거나 보조 캐시에 대한 세부 정보를 볼 수도 있습니다.

    ![지역에서 복제 상황에 맞는 메뉴](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **링크**를 클릭하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.

    ![캐시 연결](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **지역에서 복제** 블레이드에서 복제 프로세스의 진행률을 볼 수 있습니다.

    ![연결 상태](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    주 캐시와 보조 캐시에 대한 **개요** 블레이드에서 연결 상태를 볼 수도 있습니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    복제 프로세스가 완료되면 **링크 상태**가 **성공**으로 바뀝니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    기본 연결된 캐시는 연결 프로세스 중에 사용할 수 있습니다. 연결 프로세스가 완료될 때까지 보조 연결된 캐시를 사용할 수 없습니다.

## <a name="remove-a-geo-replication-link"></a>지역 복제 링크 제거

1. 두 캐시 간의 링크를 제거하고 지역 복제를 중지하려면 **지리적 복제** 블레이드에서 연결 **해제 캐시를** 클릭합니다.
    
    ![캐시 연결 해제](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역 복제 링크가 제거되면 기본 연결된 캐시에서 복제된 데이터는 보조 캐시에 남아 있습니다.
>
>

## <a name="geo-replication-faq"></a>지역에서 복제 FAQ

- [표준 또는 기본 계층 캐시를 사용하여 지역 복제를 사용할 수 있습니까?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [둘 이상의 캐시를 함께 연결할 수 있나요?](#can-i-link-more-than-two-caches-together)
- [서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [크기가 다른 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-with-different-sizes)
- [클러스터링을 사용하도록 설정한 상태로 지역 복제를 사용할 수 있습니까?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET에서 캐시와 함께 지역 복제를 사용할 수 있습니까?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redis 지역에서 복제의 복제 일정이란?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [지역에서 복제에 걸리는 기간은 얼마나 되나요?](#how-long-does-geo-replication-replication-take)
- [복제 복구 지점이 보장되나요?](#is-the-replication-recovery-point-guaranteed)
- [PowerShell 또는 Azure CLI를 사용하여 지역 복제를 관리할 수 있습니까?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>표준 또는 기본 계층 캐시를 사용하여 지역 복제를 사용할 수 있습니까?

아니요, 지역 복제는 프리미엄 계층 캐시에만 사용할 수 있습니다.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?

- 연결할 때 연결 프로세스가 완료되는 동안 기본 연결 캐시를 사용할 수 있습니다.
- 연결할 때 연결 프로세스가 완료될 때까지 보조 연결 캐시를 사용할 수 없습니다.
- 연결을 해제하면 연결 해제 프로세스가 완료되는 동안 두 캐시를 모두 사용할 수 있습니다.

### <a name="can-i-link-more-than-two-caches-together"></a>둘 이상의 캐시를 함께 연결할 수 있나요?

아니요, 두 개의 캐시만 함께 연결할 수 있습니다.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?

아니요, 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.

### <a name="can-i-link-two-caches-with-different-sizes"></a>크기가 다른 두 캐시를 연결할 수 있나요?

예, 보조 연결된 캐시가 주 연결된 캐시보다 큰 경우 연결할 수 있습니다.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>클러스터링을 사용하도록 설정한 상태로 지역 복제를 사용할 수 있습니까?

예, 두 캐시의 분할된 데이터베이스 수가 동일한 경우 사용할 수 있습니다.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET에서 캐시와 함께 지역 복제를 사용할 수 있습니까?

예. VNET의 캐시 의 지역 복제는 주의 사항으로 지원됩니다.

- 동일한 VNET에 있는 캐시 간의 지역에서 복제가 지원됩니다.
- 다른 VNET의 캐시 간의 지역 복제도 지원됩니다.
  - VNET가 동일한 지역에 있는 경우 [VNET 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 또는 VPN [게이트웨이 VNET-VNET 연결을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)사용하여 연결할 수 있습니다.
  - VNET가 다른 지역에 있는 경우 기본 내부 로드 밸로드커의 제약 조건으로 인해 VNET 피어링을 사용하는 지역 복제가 지원되지 않습니다. VNET 피어링 제약 조건에 대한 자세한 내용은 [가상 네트워크 - 피어링 - 요구 사항 및 제약 조건을](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)참조하십시오. 권장되는 해결 방법은 VPN 게이트웨이 VNET-VNET 연결을 사용하는 것입니다.

[이 Azure 템플릿을](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)사용하면 VPN 게이트웨이 VNET-VNET 연결로 연결된 VNET에 두 개의 지리적 복제 캐시를 신속하게 배포할 수 있습니다.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis 지역에서 복제의 복제 일정이란?

복제는 연속적이고 비동기적이며 특정 일정에 따라 발생하지 않습니다. 기본에 수행된 모든 쓰기는 즉시 비동기적으로 보조 에서 복제됩니다.

### <a name="how-long-does-geo-replication-replication-take"></a>지역에서 복제에 걸리는 기간은 얼마나 되나요?

복제는 증분, 비동기 및 연속이며 수행된 시간은 지역 전체의 대기 시간과 크게 다르지 않습니다. 특정 상황에서 보조 캐시는 주 데이터에서 데이터의 전체 동기화를 수행해야 할 수 있습니다. 이 경우 복제 시간은 기본 캐시에 로드, 사용 가능한 네트워크 대역폭 및 지역 간 대기 시간 등의 여러 요소에 따라 달라집니다. 전체 53GB 지리적 복제 쌍에 대한 복제 시간은 5~10분 사이일 수 있습니다.

### <a name="is-the-replication-recovery-point-guaranteed"></a>복제 복구 지점이 보장되나요?

지리적 복제 모드의 캐시의 경우 지속성이 비활성화됩니다. 고객이 시작한 장애 조치(failover)와 같이 지리적 복제 쌍이 연결해제된 경우 보조 연결된 캐시는 동기화된 데이터를 해당 시점까지 유지합니다. 이러한 상황에서는 복구 지점이 보장되지 않습니다.

복구 지점을 얻으려면 두 캐시 중 하나에서 [내보내기합니다.](cache-how-to-import-export-data.md#export) 나중에 기본 연결된 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 있습니다.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell 또는 Azure CLI를 사용하여 지역 복제를 관리할 수 있습니까?

예. Azure 포털, PowerShell 또는 Azure CLI를 사용하여 지역 복제를 관리할 수 있습니다. 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) 또는 [Azure CLI 문서를](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)참조하십시오.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?

지역 복제를 사용하는 경우 기본 연결된 캐시의 데이터가 보조 연결된 캐시로 복제됩니다. 연결된 두 캐시가 동일한 지역에 있는 경우 데이터 전송에 대한 요금이 부과되지 않습니다. 연결된 두 캐시가 서로 다른 지역에 있는 경우 데이터 전송 요금은 어느 리전에서 든 데이터 전송 비용이 됩니다. 자세한 내용은 [대역폭 가격 세부 정보를](https://azure.microsoft.com/pricing/details/bandwidth/)참조하십시오.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?

지역 복제 링크를 제거할 때까지 연결된 동안에는 지리적 복제 된 캐시및 해당 리소스 그룹을 삭제할 수 없습니다. 연결된 캐시 중 하나 또는 둘 다를 포함하는 리소스 그룹을 삭제하려고 하면, 리소스 그룹의 다른 리소스는 삭제되지만 해당 리소스 그룹은 `deleting` 상태로 유지되고 이 리소스 그룹의 연결된 캐시는 모두 `running` 상태로 남아 있습니다. 리소스 그룹과 리소스 그룹 내의 연결된 캐시를 완전히 삭제하려면 [지역 복제 링크 제거에](#remove-a-geo-replication-link)설명된 대로 캐시의 연결을 해제합니다.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?

일반적으로 캐시에 액세스하는 응용 프로그램과 동일한 Azure 지역에 캐시가 있는 것이 좋습니다. 별도의 기본 및 대체 영역이 있는 응용 프로그램의 경우 동일한 지역에 기본 및 보조 캐시가 있는 것이 좋습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [모범 사례 - Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?

Azure 리전에서 자동 장애 조치(failover)는 지리적으로 복제된 캐시에 대해 지원되지 않습니다. 재해 복구 시나리오에서 고객은 백업 영역에서 조정된 방식으로 전체 응용 프로그램 스택을 불러와야 합니다. 개별 응용 프로그램 구성 요소가 자체적으로 백업으로 전환할 시기를 결정하게 하면 성능에 부정적인 영향을 미칠 수 있습니다. Redis의 주요 이점 중 하나는 대기 시간이 매우 짧은 저장소라는 것입니다. 고객의 주 응용 프로그램이 캐시와 다른 지역에 있는 경우 추가된 왕복 시간은 성능에 현저한 영향을 미칩니다. 따라서 일시적인 가용성 문제로 인해 자동으로 장애 중지가 발생하지 않습니다.

고객이 시작한 장애 조치(failover)를 시작하려면 먼저 캐시의 연결을 해제합니다. 그런 다음 Redis 클라이언트를 변경하여 (이전에 연결된) 보조 캐시의 연결 끝점을 사용합니다. 두 캐시가 연결해제되면 보조 캐시는 다시 일반 읽기-쓰기 캐시가 되고 Redis 클라이언트에서 직접 요청을 수락합니다.

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis 프리미엄 계층](cache-premium-tier-intro.md)에 대해 자세히 알아봅니다.

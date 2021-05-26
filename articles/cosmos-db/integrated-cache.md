---
title: Azure Cosmos DB 통합 캐시
description: Azure Cosmos DB 통합 캐시는 요청 볼륨이 증가할 때 비용을 관리하고 짧은 대기 시간을 보장하는 데 도움이 되는 메모리 내 캐시입니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: f0a0556ce2a46f922e387d96d20b6425ab362580
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386445"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Azure Cosmos DB 통합 캐시 - 개요(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 통합 캐시는 요청 볼륨이 증가할 때 비용을 관리하고 짧은 대기 시간을 보장하는 데 도움이 되는 메모리 내 캐시입니다. 통합 캐시는 쉽게 설정할 수 있으며 캐시 무효화를 위한 사용자 지정 코드를 작성하거나 백 엔드 인프라를 관리하는 데 시간을 낭비할 필요가 없습니다. 통합 캐시는 Azure Cosmos DB 계정 내에서 [전용 게이트웨이](dedicated-gateway.md)를 사용합니다. 통합 캐시는 성능 향상을 위해 전용 게이트웨이를 활용하는 여러 Azure Cosmos DB 기능 중 첫 번째 기능입니다. 워크로드에 필요한 코어 수 및 메모리에 따라 세 가지 전용 게이트웨이 크기 중에 선택할 수 있습니다.

통합 캐시는 전용 게이트웨이 내에서 자동으로 구성됩니다. 통합 캐시는 다음과 같은 두 부분으로 나뉩니다. 

* 지점 읽기를 위한 항목 캐시 
* 쿼리를 위한 쿼리 캐시

통합 캐시는 LRU(오래 전에 사용한 항목) 제거 정책을 사용하는 읽기-통과 쓰기-통과 캐시입니다. 항목 캐시와 쿼리 캐시는 통합 캐시 내에서 동일한 용량을 공유하며 LRU 제거 정책은 두 쿼리에 모두 적용됩니다. 즉, 지점 읽기인지 아니면 쿼리인지에 관계없이 오래 전에 사용한 항목을 기준으로 캐시에서 데이터가 제거됩니다.

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>통합 캐시의 이점을 활용할 수 있는 워크로드

통합 캐시의 주요 목표는 읽기 작업이 많은 워크로드 비용을 절감하는 것입니다. Azure Cosmos DB는 이미 캐싱 없이 빠르기 때문에 짧은 대기 시간은 좋기는 하지만 통합 캐시의 주요 이점은 아닙니다.

통합 캐시에 도달한 지점 읽기 및 쿼리는 RU를 사용하지 않습니다. 즉, 캐시 적중 시에 RU 요금은 0입니다. 캐시 적중 시 백 엔드 데이터베이스에서 읽는 것보다 작업당 비용이 훨씬 낮습니다.

다음 특징에 해당하는 워크로드는 통합 캐시가 비용 절감에 도움이 되는지 평가해야 합니다.

-   읽기 작업이 많은 워크로드
-   많은 항목에서 지점 읽기가 반복
-   높은 RU 쿼리가 많이 반복
-   읽기의 핫 파티션 키

예상 절감액을 좌우하는 가장 큰 요인은 읽기가 반복되는 정도입니다. 워크로드에서 짧은 시간 동안 동일한 지점 읽기 또는 쿼리를 지속적으로 실행하는 경우 통합 캐시를 사용하면 매우 좋습니다. 반복되는 읽기에 통합 캐시를 사용하면 첫 번째 읽기에만 RU 기능이 사용됩니다. 동일한 전용 게이트웨이 노드(`MaxIntegratedCacheStaleness` 창 내부 및 데이터를 제거하지 않은 경우)를 통해 라우팅되는 후속 읽기는 처리량을 사용하지 않습니다.

다음과 같은 일부 워크로드는 통합 캐시를 사용하지 않는 것이 좋습니다.

-   쓰기 작업이 많은 워크로드
-  가끔 반복되는 지점 읽기 또는 쿼리

## <a name="item-cache"></a>항목 캐시

지점 읽기에 항목 캐시를 사용할 수 있습니다. 즉, 키/값이 항목 ID 및 파티션 키를 기준으로 조회됩니다.

### <a name="populating-the-item-cache"></a>항목 캐시 채우기

- 새 쓰기, 업데이트 및 삭제가 자동으로 항목 캐시에 채워집니다.
- 앱이 이전에는 캐시에 없었던(캐시 누락) 특정 항목을 읽으려고 하면 해당 항목이 이제는 항목 캐시에 저장됩니다.

### <a name="item-cache-invalidation-and-eviction"></a>항목 캐시 무효화 및 제거

- 항목 업데이트 또는 삭제
- LRU(오래 전에 사용한 항목)
- 캐시 보존 시간(즉, `MaxIntegratedCacheStaleness`)

## <a name="query-cache"></a>쿼리 캐시

쿼리 캐시를 사용하여 쿼리를 캐시할 수 있습니다. 쿼리 캐시는 쿼리를 키/값 조회로 변환합니다. 여기서 키는 쿼리 텍스트이고 값은 쿼리 결과입니다. 통합 캐시는 쿼리 엔진이 없으며 각 쿼리에 대한 키/값 조회만 저장합니다.

### <a name="populating-the-query-cache"></a>쿼리 캐시 채우기

- 캐시에 해당 쿼리에 대한 결과가 없으면(캐시 누락) 쿼리가 백 엔드로 전송됩니다. 쿼리가 실행된 후에는 캐시가 해당 쿼리에 대한 결과를 저장합니다.

### <a name="query-cache-eviction"></a>쿼리 캐시 제거

- LRU(오래 전에 사용한 항목)
- 캐시 보존 시간(즉, `MaxIntegratedCacheStaleness`)

### <a name="working-with-the-query-cache"></a>쿼리 캐시 작업

쿼리의 결과 페이지가 여러 개 있더라도 쿼리 캐시를 작업할 때 특수 코드가 필요하지 않습니다. 쿼리가 통합 캐시에 도달하든 백 엔드 쿼리 엔진에서 실행되든, 쿼리 페이지 지정에 대한 모범 사례와 코드는 동일합니다.

쿼리 캐시는 해당하는 경우 쿼리 연속 토큰을 자동으로 캐시합니다. 쿼리의 결과 페이지가 여러 개 있는 경우 통합 캐시에 저장된 모든 페이지에는 RU 요금이 부과되지 않습니다. 쿼리 결과의 후속 페이지에서 백 엔드 실행이 필요한 경우 이전 작업과 중복되지 않도록 이전 페이지의 연속 토큰이 사용됩니다.

> [!NOTE]
> 서로 다른 전용 게이트웨이 노드에 있는 통합 캐시 인스턴스의 캐시는 서로 독립적입니다. 한 노드 내에서 캐시된 데이터가 다른 노드에서도 반드시 캐시되는 것은 아닙니다.

## <a name="integrated-cache-consistency"></a>통합 캐시 일관성

통합 캐시는 최종 [일관성](consistency-levels.md)만 지원합니다. 읽기에 일관적인 접두사, 세션, 제한된 부실 또는 강력한 일관성이 있는 경우 읽기는 항상 통합 캐시를 무시합니다.

모든 읽기에 대해 최종 일관성을 구성하는 가장 쉬운 방법은 [계정 수준에서 설정](consistency-levels.md#configure-the-default-consistency-level)하는 것입니다. 그러나 일부 읽기만 최종 일관성을 유지하려면 [요청 수준](how-to-manage-consistency.md#override-the-default-consistency-level)에서 일관성을 구성할 수도 있습니다.

## <a name="integrated-cache-retention-time"></a>통합 캐시 보존 시간

캐시 보존 시간은 캐시된 데이터의 최대 보존 시간입니다. 미리 보기 중에는 `MaxIntegratedCacheStaleness`가 항상 5분으로 설정되며 사용자 지정할 수 없습니다.

## <a name="metrics"></a>메트릭

통합 캐시를 사용하는 경우 몇 가지 주요 메트릭을 모니터링하면 도움이 됩니다. 통합 캐시 메트릭은 다음과 같습니다.

- `DedicatedGatewayCpuUsage` - 각 전용 게이트웨이 노드의 CPU 사용량
- `DedicatedGatewayMemoryUsage` - 각 전용 게이트웨이 노드가 라우팅 요청 및 캐싱에 사용한 메모리 사용량
- `DedicatedGatewayRequests` - 각 전용 게이트웨이 노드를 통해 라우팅된 요청 수
- `IntegratedCacheEvictedEntriesSize` – 통합 캐시에서 제거된 데이터 양
- `IntegratedCacheTTLExpirationCount` - 캐시된 데이터가 `MaxIntegratedCacheStaleness` 시간을 초과하여 통합 캐시에서 제거된 항목 수
- `IntegratedCacheHitRate` – (통합 캐시를 사용하려고 시도한 모든 전용 게이트웨이 요청에서) 통합 캐시를 사용한 시점 읽기 및 쿼리의 비율

모든 기존 메트릭은 기본적으로 메트릭 클래식이 아닌 **메트릭 블레이드** 에서 사용할 수 있습니다.

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="통합 캐시 메트릭의 위치를 보여주는 이미지" border="false":::

모든 메트릭은 모든 전용 게이트웨이 노드에서 평균으로 노출됩니다. 예를 들어 노드가 5개인 전용 게이트웨이 클러스터를 프로비전하는 경우 메트릭은 5개 노드의 평균 값을 반영합니다.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

아래 예제에서는 몇 가지 일반적인 시나리오를 디버그하는 방법을 보여줍니다.

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>애플리케이션에서 전용 게이트웨이를 사용하고 있는지 알 수 없습니다.

`DedicatedGatewayRequests`를 확인합니다. 이 메트릭은 요청이 통합 캐시에 도달했는지 여부에 관계없이 전용 게이트웨이를 사용하는 모든 요청을 포함합니다. 애플리케이션에서 표준 게이트웨이 또는 직접 모드와 원본 연결 문자열을 사용하는 경우 오류 메시지가 표시되지 않지만 `DedicatedGatewayRequests`는 0입니다.

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>요청이 통합 캐시에 도달하는지 알 수 없습니다.

`IntegratedCacheHitRate`를 확인합니다. 이 값이 0이면 요청이 통합 캐시에 도달하지 않는 것입니다. 전용 게이트웨이 연결 문자열을 사용 중이고 게이트웨이 모드로 연결 중이며 최종 일관성을 설정했는지 확인해야 합니다.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>전용 게이트웨이가 너무 작은지 알아보려 합니다.

`IntegratedCacheHitRate`를 확인합니다. 이 값이 크면(예: 0.5-0.6 이상) 전용 게이트웨이가 충분히 크다는 신호입니다.

`IntegratedCacheHitRate`가 작으면 `IntegratedCacheEvictedEntriesSize`를 살펴봅니다. `IntegratedCacheEvictedEntriesSize`가 크면 전용 게이트웨이 크기를 늘리는 것이 더 좋다는 의미일 수 있습니다.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>전용 게이트웨이가 너무 큰지 알아보려 합니다.

이것은 측정하기 어렵습니다. 일반적으로 처음에는 작게 시작하고 `IntegratedCacheHitRate`가 더 이상 향상되지 않을 때까지 전용 게이트웨이 크기를 서서히 늘려야 합니다.

대부분의 데이터가 LRU가 아닌 `MaxIntegratedCacheStaleness`를 초과하여 캐시에서 제거되는 경우 캐시가 필요 이상으로 큰 것일 수 있습니다. `IntegratedCacheTTLExpirationCount`가 `IntegratedCacheEvictedEntriesSize`와 거의 비슷한지 확인합니다. 거의 비슷한 경우 전용 게이트웨이 크기를 줄이고 성능을 비교할 수 있습니다.

`DedicatedGatewayMemoryUsage`를 확인하고 전용 게이트웨이 크기와 비교합니다. `DedicatedGatewayMemoryUsage`가 전용 게이트웨이 크기보다 작은 경우 전용 게이트웨이 크기를 더 줄여 보아야 합니다.

## <a name="next-steps"></a>다음 단계

- [통합 캐시 FAQ](integrated-cache-faq.md)
- [통합 캐시 구성](how-to-configure-integrated-cache.md)
- [전용 게이트웨이](dedicated-gateway.md)
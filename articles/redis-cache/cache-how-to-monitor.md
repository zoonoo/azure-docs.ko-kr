---
title: "Azure Redis Cache를 모니터링하는 방법 | Microsoft Docs"
description: "Azure Redis Cache 인스턴스의 상태와 성능을 모니터링하는 방법을 알아봅니다."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 65385aa918222837468f88246d0527c22c677ba7
ms.openlocfilehash: 3ef5dcbcb5f8f6a57de575af20028875b9f920ea


---
# <a name="how-to-monitor-azure-redis-cache"></a>Azure Redis Cache를 모니터링하는 방법
Azure Redis Cache에서는 캐시 인스턴스를 모니터링하기 위한 몇 가지 옵션을 제공합니다. 메트릭을 보고, 메트릭 차트를 시작 보드에 고정하고, 모니터링 차트의 날짜 및 시간 범위를 사용자 지정하고, 차트에서 메트릭을 추가 및 제거하고, 특정 조건이 충족될 경우의 경고를 설정할 수 있습니다. 이러한 도구는 Azure Redis Cache 인스턴스의 상태를 모니터링할 수 있게 해주며 캐싱 응용 프로그램 관리에 도움이 됩니다.

캐시 진단을 사용하도록 설정하면 Azure Redis Cache 인스턴스에 대한 메트릭이 약 30초마다 수집되고 저장되므로 이러한 메트릭이 메트릭 차트에 표시되고 경고 규칙에 따라 평가될 수 있습니다.

캐시 메트릭은 Redis [INFO](http://redis.io/commands/info) 명령을 사용하여 수집됩니다. 각 캐시 메트릭에 사용되는 다양한 INFO 값에 대한 자세한 내용은 [사용 가능한 메트릭 및 보고 간격](#available-metrics-and-reporting-intervals)을 참조하세요.

캐시 메트릭을 보려면 [Azure Portal](https://portal.azure.com)에서 캐시 인스턴스를 [찾아보세요](cache-configure.md#configure-redis-cache-settings). Azure Redis Cache 인스턴스 메트릭은 **Redis 메트릭** 블레이드에서 액세스합니다.

![Redis 메트릭][redis-cache-redis-metrics-blade]

> [!IMPORTANT]
> 다음 메시지가 **Redis 메트릭** 에 표시되면 [캐시 진단 사용](#enable-cache-diagnostics) 섹션의 단계를 수행하여 캐시 진단을 사용하도록 설정하세요.
> 
> `Monitoring may not be enabled. Click here to turn on Diagnostics.`
> 
> 

**Redis 메트릭** 블레이드에는 캐시 메트릭을 표시하는 **모니터링** 차트가 있습니다. 각 차트는 메트릭을 추가하거나 제거하고 보고 간격을 변경하여 사용자 지정할 수 있습니다. **Redis Cache** 블레이드에는 작업 및 경고를 보고 구성할 수 있도록 캐시 **이벤트** 및 **경고 규칙**을 표시하는 **작업** 섹션도 있습니다.

## <a name="enable-cache-diagnostics"></a>캐시 진단 사용
Azure Redis Cache에서는 진단 데이터를 저장소 계정에 저장하는 기능을 제공하므로 원하는 도구를 사용하여 직접 해당 데이터에 액세스하여 데이터를 처리할 수 있습니다. 캐시 진단을 수집하여 저장하고 Azure 포털에 표시하려면 저장소 계정을 구성해야 합니다. 동일한 지역 및 구독의 캐시는 동일한 진단 저장소 계정을 공유하며 구성이 변경되면 해당 지역에 있는 구독의 모든 캐시에 변경 내용이 적용됩니다.

캐시 진단을 사용하도록 설정하고 구성하려면 캐시 인스턴스의 **Redis Cache** 블레이드로 이동합니다. 진단을 사용하도록 설정하지 않으면 진단 차트 대신 메시지가 표시됩니다.

![캐시 진단 사용][redis-cache-enable-diagnostics]

메시지를 클릭하여 **메트릭** 블레이드를 표시하고 **진단 설정**을 클릭하여 캐시 서비스 인스턴스에 대한 진단 설정을 사용하도록 설정하고 구성합니다.

![진단 설정][redis-cache-diagnostic-settings]

![진단 구성][redis-cache-configure-diagnostics]

**켜기** 단추를 클릭하여 캐시 진단을 사용하도록 설정하고 진단 구성을 표시합니다.

**저장소 계정** 오른쪽의 화살표를 클릭하여 진단 데이터를 저장할 저장소 계정을 선택합니다. 최상의 성능을 위해 캐시와 동일한 지역의 저장소 계정을 선택합니다.

진단 설정이 구성되면 **저장** 을 클릭하여 구성을 저장합니다. 변경 사항이 적용되는 데 다소 시간이 걸릴 수 있습니다.

> [!IMPORTANT]
> 동일한 지역 및 구독의 캐시는 동일한 진단 저장소 설정을 공유하며 구성이 변경되면(진단 활성화/비활성화 또는 저장소 계정 변경) 해당 지역에 있는 구독의 모든 캐시에 변경 내용이 적용됩니다.
> 
> 

저장된 메트릭을 보려면 `WADMetrics`로 시작하는 이름의 테이블을 저장소 계정에서 검사합니다. Azure 포털 외부에 저장된 메트릭에 액세스하는 방법에 대한 자세한 내용은 [Redis Cache 모니터링 데이터 액세스](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 샘플을 참조하세요.

> [!NOTE]
> 선택한 저장소 계정에 저장된 메트릭만 Azure 포털에 표시됩니다. 저장소 계정을 변경하는 경우 이전에 구성된 저장소 계정의 데이터는 계속 다운로드할 수는 있으나 Azure 포털에 표시되지는 않습니다.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>사용 가능한 메트릭 및 보고 간격
캐시 메트릭은 **지난 시간**, **오늘**, **지난 주** 및 **사용자 지정**을 포함한 몇 가지 보고 간격을 사용하여 보고됩니다. 각 메트릭 차트의 **메트릭** 블레이드에는 차트의 각 메트릭에 대한 평균값, 최소값 및 최대값이 표시되고 일부 메트릭의 경우 보고 간격에 대한 총계가 표시됩니다. 

각 메트릭은 두 가지 버전을 포함합니다. 하나의 메트릭은 전체 캐시 및 [클러스터링](cache-how-to-premium-clustering.md)을 사용하는 캐시에 대한 성능을 측정합니다. 이름에 `(Shard 0-9)`를 포함하는 메트릭의 차기 버전은 캐시에서 단일 분할에 대한 성능을 측정합니다. 예를 들어 캐시에 4개의 분할이 있는 경우 `Cache Hits`은 전체 캐시에 대한 총 적중 수이고 `Cache Hits (Shard 3)`는 캐시의 해당 분할에 대한 적중입니다.

> [!NOTE]
> 활성 클라이언트 응용 프로그램이 연결되어 있지 않아서 캐시가 유휴 상태인 경우에도 연결된 클라이언트, 메모리 사용, 수행 중인 작업 등 일부 캐시 활동이 나타날 수 있습니다. Azure Redis Cache 인스턴스 작업 중에는 이러한 활동이 일반적으로 나타납니다.
> 
> 

| 메트릭 | 설명 |
| --- | --- |
| 캐시 적중 |지정한 보고 간격 동안 성공한 키 조회 수입니다. 이 값은 Redis [INFO](http://redis.io/commands/info) 명령에서 `keyspace_hits`에 매핑됩니다. |
| 캐시 누락 |지정한 보고 간격 동안 실패한 키 조회 수입니다. 이 값은 Redis INFO 명령에서 `keyspace_misses` 에 매핑됩니다. 캐시 누락이 반드시 캐시에 문제가 있음을 의미하는 것은 아닙니다. 예를 들어 캐시 배제 프로그래밍 패턴을 사용하는 경우 응용 프로그램은 먼저 캐시에서 항목을 찾습니다. 항목이 캐시에 없으면(캐시 누락) 데이터베이스에서 항목을 검색하고 다음 검색을 위해 캐시에 항목을 추가합니다. 캐시 누락은 캐시 배제 프로그래밍 패턴의 일반적인 동작입니다. 캐시 누락 수가 예상보다 높은 경우 캐시를 채우고 캐시에서 읽는 응용 프로그램 논리를 검사합니다. 메모리 압력 때문에 캐시에서 항목이 제거되고 있는 경우 캐시 누락이 있을 수 있으며 메모리 압력을 모니터링하는 데 더 유용한 메트릭은 `Used Memory` 또는 `Evicted Keys`입니다. |
| 연결된 클라이언트 |지정한 보고 간격 동안 캐시에 설정된 클라이언트 연결 수입니다. 이 값은 Redis INFO 명령에서 `connected_clients` 에 매핑됩니다. [연결 제한](cache-configure.md#default-redis-server-configuration) 에 도달하면 캐시에 대한 후속 연결 시도가 실패합니다. 활성 클라이언트 응용 프로그램이 없는 경우에도 내부 프로세스 및 연결 때문에 연결된 클라이언트 인스턴스가 여전히 몇 개 있을 수 있습니다. |
| 제거된 키 |지정한 보고 간격 동안 `maxmemory` 제한 때문에 캐시에서 제거된 항목의 수입니다. 이 값은 Redis INFO 명령에서 `evicted_keys` 에 매핑됩니다. |
| 만료된 키 |지정한 보고 간격 동안 캐시에서 만료된 항목의 수입니다. 이 값은 Redis INFO 명령에서 `expired_keys` 에 매핑됩니다. |
| 가져오기 |지정한 보고 간격 동안 캐시에서 수행된 가져오기 작업의 수입니다. 이 값은 모든 Redis INFO 명령 `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` 및 `cmdstat_getrange` 값의 합계이며 보고 간격 동안의 캐시 적중 및 누락 합계에 해당합니다. |
| Redis 서버 부하 |Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 이 카운터가 100이 되면 Redis 서버가 성능 한계에 도달하여 CPU가 더 빨리 작업을 처리할 수 없습니다. Redis 서버 부하가 높으면 클라이언트에 시간 제한 예외가 표시됩니다. 이 경우 강화나 여러 캐시로의 데이터 분할을 고려해야 합니다. |
| 설정 |지정한 보고 간격 동안 캐시에 수행된 설정 작업의 수입니다. 이 값은 모든 Redis INFO 명령 `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` 및 `cmdstat_setnx` 값의 합계입니다. |
| 총 작업 |지정한 보고 간격 동안 캐시 서버에서 처리한 총 명령 수입니다. 이 값은 Redis INFO 명령에서 `total_commands_processed` 에 매핑됩니다. Azure Redis Cache가 pub/sub에만 사용되는 경우 `Cache Hits`, `Cache Misses`, `Gets` 또는 `Sets`에 대한 메트릭은 없으나 pub/sub 작업의 캐시 사용량을 반영하는 `Total Operations` 메트릭은 있습니다. |
| 사용된 메모리 |지정한 보고 간격 동안 캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB)입니다. 이 값은 Redis INFO 명령에서 `used_memory` 에 매핑됩니다. 메타데이터 또는 조각화를 포함하지 않습니다. |
| 사용된 메모리 RSS |조각화 및 메타데이터를 포함하여 지정한 보고 간격 동안 사용된 캐시 메모리의 양(MB)입니다. 이 값은 Redis INFO 명령에서 `used_memory_rss` 에 매핑됩니다. |
| CPU |지정한 보고 간격 동안의 Azure Redis Cache 서버 CPU 사용률(%)입니다. 이 값은 운영 체제 `\Processor(_Total)\% Processor Time` 성능 카운터에 매핑됩니다. |
| 캐시 읽기 |지정한 보고 간격 동안 캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양입니다. 이 값은 캐시를 호스트하는 가상 컴퓨터를 지원하는 네트워크 인터페이스 카드에서 가져오며 Redis에 특정한 값이 아닙니다. **이 값은 캐시에서 사용되는 네트워크 대역폭에 해당합니다. 서버 쪽 네트워크 대역폭 제한에 대한 경고를 설정하려면 `Cache Read` 카운터를 사용하여 경고를 생성합니다. 다양한 캐시 가격 책정 계층 및 크기에 대해 관찰된 대역폭 제한은 [이 테이블](cache-faq.md#cache-performance)을 참조하세요.** |
| 캐시 쓰기 |지정한 보고 간격 동안 캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양입니다. 이 값은 캐시를 호스트하는 가상 컴퓨터를 지원하는 네트워크 인터페이스 카드에서 가져오며 Redis에 특정한 값이 아닙니다. 이 값은 클라이언트에서 캐시로 전송되는 데이터의 네트워크 대역폭에 해당됩니다. |

## <a name="how-to-view-metrics-and-customize-charts"></a>메트릭을 보고 차트를 사용자 지정하는 방법
**Redis 메트릭** 블레이드에서 캐시에 대한 메트릭의 개요를 볼 수 있습니다. **Redis 메트릭** 블레이드에 액세스하려면 **모든 설정** > **Redis 메트릭**을 선택합니다.

![Redis 메트릭][redis-cache-redis-metrics]

**Redis 메트릭** 블레이드에서 다음 차트가 포함됩니다.

| Redis 메트릭 차트 | 표시된 메트릭 |
| --- | --- |
| 캐시 읽기 및 캐시 쓰기 |캐시 읽기 |
| 캐시 쓰기 | |
| 연결된 클라이언트 |연결된 클라이언트 |
| 적중 및 누락 |캐시 적중 |
| 캐시 누락 | |
| 총 명령 |총 작업 |
| 가져오기 및 설정 |가져오기 |
| 설정 | |
| CPU 사용량 |CPU |
| 메모리 사용량 |사용된 메모리 |
| 사용된 메모리 RSS | |
| Redis 서버 부하 |서버 부하 |
| 키 수 |전체 키 |
| 제거된 키 | |
| 만료된 키 | |

특정 차트의 메트릭을 더욱 자세히 보고 차트를 사용자 지정하려면 **Redis 메트릭** 블레이드에서 원하는 차트를 클릭하여 해당 차트의 **메트릭** 블레이드를 표시합니다.

![메트릭 블레이드][redis-cache-metric-blade]

차트에 표시되는 메트릭에 설정된 모든 경고가 해당 차트의 **메트릭** 블레이드 아래쪽에 나열됩니다.

메트릭을 추가 또는 제거하거나 보고 간격을 변경하려면 **차트 편집**을 선택합니다.

차트에서 메트릭을 추가하거나 제거하려면 메트릭의 이름 옆에 있는 확인란을 클릭합니다. 보고 간격을 변경하려면 원하는 간격을 클릭합니다. **차트 종류**를 변경하려면 원하는 스타일을 클릭합니다. 원하는 대로 변경했으면 **저장**을 클릭합니다. 

![차트 편집][redis-cache-edit-chart]

**저장**을 클릭하면 **메트릭** 블레이드를 떠날 때까지 변경 사항이 유지됩니다. 나중에 돌아오면 원래 메트릭 및 시간 범위가 다시 표시됩니다. 차트 사용자 지정에 대한 자세한 내용은 [서비스 메트릭 모니터링](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)을 참조하세요.

차트에서 특정 기간에 대한 메트릭을 보려면 원하는 시간에 해당하는 차트의 특정 막대 또는 지점 중 하나 위로 마우스를 가져갑니다. 그러면 해당 간격에 대한 메트릭이 표시됩니다.

![차트 세부 정보 보기][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>클러스터링을 사용하는 프리미엄 캐시를 모니터링하는 방법
[클러스터링](cache-how-to-premium-clustering.md)을 사용하도록 설정된 프리미엄 캐시는 최대 10개의 분할된 데이터베이스를 가질 수 있습니다. 각 분할된 데이터베이스에는 자체 메트릭이 있으며 이러한 메트릭은 집계되어 캐시에 메트릭을 전체로 제공합니다. 각 메트릭은 두 가지 버전을 포함합니다. 하나의 메트릭은 전체 캐시에 대한 성능을 측정합니다. 이름에 `(Shard 0-9)`을 포함하는 메트릭의 차기 버전은 캐시에서 단일 분할된 데이터베이스에 대한 성능을 측정합니다. 예를 들어 캐시에 3개의 분할된 데이터베이스가 있는 경우 `Cache Hits`은 전체 캐시에 대한 총 적중 수이고 `Cache Hits (Shard 2)`는 캐시의 해당 분할된 데이터베이스에 대한 적중입니다.

각 모니터링 차트는 각 캐시의 분할된 데이터베이스에 대한 메트릭과 함께 캐시의 최상위 수준 메트릭을 표시합니다.

![모니터][redis-cache-premium-monitor]

데이터 요소 위에 마우스를 놓으면 특정 시점에 대한 세부 정보를 표시합니다. 

![모니터][redis-cache-premium-point-summary]

값이 클수록 일반적으로 캐시의 집계 값이며 값이 작을수록 분할된 데이터베이스의 개별 메트릭입니다. 이 예제에는 3개의 분할된 데이터베이스가 있으며 캐시 적중이 분할된 데이터베이스에 균등하게 배포되어 있습니다.

![모니터][redis-cache-premium-point-shard]

자세한 내용을 보려면 차트를 클릭하여 **메트릭** 블레이드에 대한 확장된 보기를 봅니다.

![모니터][redis-cache-premium-chart-detail]

기본적으로 각 차트에는 개별 분할된 데이터베이스에 대한 성능 카운터 및 최상위 캐시 성능 카운터가 포함되어 있습니다. **차트 편집** 블레이드에서 이들을 사용자 지정할 수 있습니다.

![모니터][redis-cache-premium-edit]

사용 가능한 성능 카운터에 대한 자세한 내용은 [사용 가능한 메트릭 및 보고 간격](#available-metrics-and-reporting-intervals)을 참조하세요.

## <a name="operations-and-alerts"></a>작업 및 경고
**Redis Cache** 블레이드의 **작업** 섹션에는 **이벤트** 및 **경고 규칙** 섹션이 있습니다.

![작업][redis-cache-operations-events]

최근 캐시 작업 목록을 보려면 **이벤트** 차트를 클릭하여 **이벤트** 블레이드를 표시합니다. 작업의 예로는 액세스 키 검색 및 재생성, 경고 규칙 활성화 및 해결 등이 있습니다. 각 이벤트에 대한 자세한 내용을 보려면 **이벤트** 블레이드에서 이벤트를 클릭합니다.

이벤트에 대한 자세한 내용은 [이벤트 및 감사 로그 보기](../monitoring-and-diagnostics/insights-debugging-with-events.md)를 참조하세요.

**경고 규칙** 섹션에는 캐시 인스턴스에 대한 경고 개수가 표시됩니다. 경고 규칙을 사용하여 캐시 인스턴스를 모니터링하고 특정 메트릭 값이 규칙에 정의된 임계값에 도달할 때마다 전자 메일을 받을 수 있습니다. 

경고 규칙은 약 5분마다 평가되며 경고 규칙이 활성화되면 구성된 모든 알림이 전송됩니다. 경고 규칙 활성화 및 알림은 즉시 처리되지 않습니다. 경고 규칙이 활성화되고 알림이 전송되기 전에 몇 분 정도 시간이 지연될 수 있습니다.

경고 규칙은 특정 모니터링 차트의 **메트릭** 블레이드 또는 **경고 규칙** 블레이드에서 보고 설정할 수 있습니다.

경고 규칙에는 다음과 같은 속성이 있습니다.

| 경고 규칙 속성 | 설명 |
| --- | --- |
| 리소스 |경고 규칙으로 평가하는 리소스입니다. Redis Cache에서 경고 규칙을 만드는 경우 해당 캐시가 리소스입니다. |
| 이름 |현재 캐시 인스턴스 내 경고 규칙을 고유하게 식별하는 이름입니다. |
| 설명 |경고 규칙에 대한 선택적 설명입니다. |
| 메트릭 |경고 규칙으로 모니터링할 메트릭입니다. 캐시 메트릭 목록은 사용 가능한 메트릭 및 보고 간격을 참조하세요. |
| 조건 |경고 규칙에 대한 조건 연산자입니다. 선택할 수 있는 항목은 보다 큼, 크거나 같음, 보다 작음, 작거나 같음입니다. |
| 임계값 |조건 속성에 지정된 연산자를 사용하여 메트릭과 비교하는 데 사용되는 값입니다. 메트릭에 따라 이 값의 단위는 바이트/초, 바이트, % 또는 개수가 될 수 있습니다. |
| 기간 |경고 규칙 비교에 사용되는 메트릭의 평균 값에 대한 기간을 지정합니다. 예를 들어 기간이 [Over the last hour(마지막 시간)]인 경우 이전 시간 간격의 메트릭 평균 값이 비교에 사용됩니다. 작업이 급증하여 임계값에 도달하는 경우 알림을 받으려면 짧은 기간이 적합합니다. 임계값을 초과하는 작업이 지속되는 경우 알림을 받으려면 긴 기간을 사용합니다. |
| 전자 메일 서비스 및 공동 관리자 |True로 설정되면 경고가 활성화되는 경우 서비스 관리자 및 공동 관리자에게 전자 메일이 전송됩니다. |
| 추가 관리자 전자 메일 |경고가 활성화되는 경우 알림을 받을 추가 관리자의 선택적 전자 메일 주소입니다. |

경고 규칙이 활성화될 때마다 알림이 하나씩만 전송됩니다. 규칙에 대한 임계값이 초과되고 알림이 전송되면 메트릭이 임계값 아래로 떨어질 때까지 규칙이 다시 평가되지 않습니다. 이후에 메트릭이 임계값을 초과하면 경고가 다시 활성화되고 새로운 알림이 전송됩니다.

캐시 인스턴스에 대한 경고 규칙을 모두 보려면 **Redis Cache** 블레이드의 **경고 규칙** 부분을 클릭하세요. 특정 메트릭을 사용하는 경고 규칙만 보려면 해당 메트릭이 포함된 차트의 **메트릭** 블레이드로 이동합니다.

![경고 규칙][redis-cache-alert-rules]

경고 규칙을 추가하려면 **메트릭** 블레이드 또는 **경고 규칙** 블레이드에서 **경고 추가**를 클릭합니다. 

원하는 규칙 조건을 **경고 추가** 규칙 블레이드에 입력하고 **확인**을 클릭합니다. 

![경고 규칙 추가][redis-cache-add-alert]

> [!NOTE]
> **메트릭** 블레이드에서 **경고 추가**를 클릭하여 경고 규칙을 만들면 해당 블레이드의 차트에 표시되는 메트릭만 **메트릭** 드롭다운 목록에 나타납니다. **경고 규칙** 블레이드에서 **경고 추가**를 클릭하여 경고 규칙을 만들면 모든 캐시 메트릭을 **메트릭** 드롭다운 목록에서 사용할 수 있습니다.
> 
> 

경고 규칙이 저장되면 **경고 규칙** 블레이드뿐만 아니라 이 경고 규칙에 사용된 메트릭을 표시하는 모든 차트의 **메트릭** 블레이드에도 이 경고 규칙이 표시됩니다. 경고 규칙을 편집하려면 경고 규칙의 이름을 클릭하여 **규칙 편집** 블레이드를 표시합니다. **규칙 편집** 블레이드에서는 규칙의 속성을 편집하거나, 경고 규칙을 삭제하거나, 경고 규칙을 사용하지 않도록 설정하거나, 이전에 사용하지 않도록 설정한 규칙을 다시 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 규칙 속성 변경 내용이 **경고 규칙** 블레이드 또는 **메트릭** 블레이드에 반영되려면 약간의 시간이 걸릴 수 있습니다.
> 
> 

경고 규칙이 활성화되면 경고 규칙 구성에 따라 전자 메일이 전송되고 **Redis Cache** 블레이드의 **경고 규칙** 부분에 경고 아이콘이 표시됩니다.

경고 조건이 더 이상 true로 평가되지 않으면 경고 규칙이 해결된 것으로 간주됩니다. 경고 규칙 조건이 해결되면 경고 아이콘이 확인 표시로 바뀝니다. 경고 활성화 및 해결에 대한 세부 정보는 **Redis Cache** 블레이드의 **이벤트** 부분을 클릭하여 **이벤트** 블레이드의 이벤트를 참조하세요.

Azure의 경고에 대한 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.

## <a name="metrics-on-the-redis-cache-blade"></a>Redis Cache 블레이드의 메트릭
**Redis Cache** 블레이드는 다음과 같은 범주의 메트릭을 표시합니다.

* [모니터링 차트](#monitoring-charts)
* [사용 현황 차트](#usage-charts)

### <a name="monitoring-charts"></a>모니터링 차트
**모니터링** 섹션에는 **적중 및 누락**, **가져오기 및 설정**, **연결** 및 **총 명령** 차트가 있습니다.

![모니터링 차트][redis-cache-monitoring-part]

**모니터링** 차트에는 다음 메트릭이 표시됩니다.

| 모니터링 차트 | 캐시 메트릭 |
| --- | --- |
| 적중 및 누락 |캐시 적중 |
| 캐시 누락 | |
| 가져오기 및 설정 |가져오기 |
| 설정 | |
| 연결 |연결된 클라이언트 |
| 총 명령 |총 작업 |

메트릭을 보고 이 섹션의 개별 차트를 사용자 지정하는 방법에 대한 자세한 내용은 다음 [메트릭을 보고 메트릭 차트를 사용자 지정하는 방법](#how-to-view-metrics-and-customize-charts) 섹션을 참조하세요.

### <a name="usage-charts"></a>사용 현황 차트
**사용 현황** 섹션에는 **Redis 서버 부하**, **메모리 사용량**, **네트워크 대역폭** 및 **CPU 사용량** 차트가 있으며 캐시 인스턴스의 **가격 책정 계층**도 표시됩니다.

![사용 현황 차트][redis-cache-usage-part]

**가격 책정 계층** 은 캐시 가격 책정 계층을 표시하며 다른 가격 책정 계층으로 캐시 [크기를 조정](cache-how-to-scale.md) 하는 데 사용할 수 있습니다.

**사용 현황** 차트에는 다음 메트릭이 표시됩니다.

| 사용 현황 차트 | 캐시 메트릭 |
| --- | --- |
| Redis 서버 부하 |서버 부하 |
| 메모리 사용량 |사용된 메모리 |
| 네트워크 대역폭 |캐시 쓰기 |
| CPU 사용량 |CPU |

메트릭을 보고 이 섹션의 개별 차트를 사용자 지정하는 방법에 대한 자세한 내용은 다음 [메트릭을 보고 메트릭 차트를 사용자 지정하는 방법](#how-to-view-metrics-and-customize-charts) 섹션을 참조하세요.

<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png






<!--HONumber=Jan17_HO2-->



---
title: Azure Cache for Redis 데이터 손실 문제 해결
description: 'Redis에 대 한 Azure 캐시에 대 한 데이터 손실 문제를 해결 하는 방법을 알아봅니다(예: 키의 부분 손실, 키 만료 또는 키의 완전 손실)'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530904"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Azure Cache for Redis 데이터 손실 문제 해결

이 문서에서는 Redis용 Azure 캐시에서 발생할 수 있는 실제 또는 인식된 데이터 손실을 진단하는 방법에 대해 설명합니다.

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="partial-loss-of-keys"></a>키의 부분 손실

Redis용 Azure 캐시는 메모리에 저장된 후 키를 임의로 삭제하지 않습니다. 그러나 만료 또는 제거 정책에 대한 응답과 명시적 키 삭제 명령에 대한 응답으로 키를 제거합니다. Redis 인스턴스에 대한 프리미엄 또는 표준 Azure 캐시에서 마스터 노드에 기록된 키는 복제본에서 즉시 사용하지 못할 수도 있습니다. 데이터는 비동기 및 비차단 방식으로 마스터에서 복제본으로 복제됩니다.

캐시에서 키가 사라진 경우 다음과 같은 가능한 원인을 확인하십시오.

| 원인 | 설명 |
|---|---|
| [키 만료](#key-expiration) | 키는 설정된 시간 때문에 제거됩니다. |
| [키 제거](#key-eviction) | 키는 메모리 압력하에서 제거됩니다. |
| [키 삭제](#key-deletion) | 키는 명시적 삭제 명령에 의해 제거됩니다. |
| [비동기 복제](#async-replication) | 데이터 복제 지연으로 인해 복제본에서 키를 사용할 수 없습니다. |

### <a name="key-expiration"></a>키 만료

Redis용 Azure 캐시는 키에 시간 시간이 할당되고 해당 기간이 경과한 경우 키를 자동으로 제거합니다. Redis 키 만료에 대한 자세한 내용은 [EXPIRE](https://redis.io/commands/expire) 명령 설명서를 참조하십시오. 시간 시간 설정 값은 [SET,](https://redis.io/commands/set) [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)및 기타 ** \*STORE** 명령을 사용하여 설정할 수도 있습니다.

만료된 키 수에 대한 통계를 얻으려면 [INFO](https://redis.io/commands/info) 명령을 사용합니다. 이 `Stats` 섹션에는 만료된 키의 총 수가 표시됩니다. 이 `Keyspace` 섹션에서는 시간 시간과 평균 시간 시간 시간 값을 가진 키 수에 대한 자세한 정보를 제공합니다.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

캐시에 대한 진단 메트릭을 확인하여 키가 누락된 시기와 만료된 키의 스파이크 간에 상관 관계가 있는지 확인할 수도 있습니다. 키 공간 알림 또는 **모니터를** 사용하여 이러한 유형의 문제를 디버깅하는 것에 대한 자세한 내용은 [Redis 키스페이스 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 부록을 참조하십시오.

### <a name="key-eviction"></a>키 제거

Redis용 Azure 캐시에는 데이터를 저장하는 메모리 공간이 필요합니다. 필요한 경우 사용 가능한 메모리를 확보하도록 키를 제거합니다. [INFO](https://redis.io/commands/info) 명령의 **used_memory** 또는 **used_memory_rss** 값이 구성된 **maxmemory** 설정에 접근하면 Redis용 Azure 캐시는 [캐시 정책에](https://redis.io/topics/lru-cache)따라 메모리에서 키를 제거하기 시작합니다.

[INFO](https://redis.io/commands/info) 명령을 사용하여 제거된 키 수를 모니터링할 수 있습니다.

```
# Stats

evicted_keys:13224
```

캐시에 대한 진단 메트릭을 확인하여 키가 누락된 시기와 제거된 키의 급증 사이에 상관 관계가 있는지 확인할 수도 있습니다. 키 공간 알림 또는 **모니터를** 사용하여 이러한 유형의 문제를 디버깅하는 것에 대한 자세한 내용은 [Redis 키스페이스 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 부록을 참조하십시오.

### <a name="key-deletion"></a>키 삭제

Redis 클라이언트는 [DEL](https://redis.io/commands/del) 또는 [HDEL](https://redis.io/commands/hdel) 명령을 통해 Redis용 Azure 캐시에서 키를 명시적으로 제거할 수 있습니다. [INFO](https://redis.io/commands/info) 명령을 사용하여 삭제 작업 수를 추적할 수 있습니다. **DEL** 또는 **HDEL** 명령이 호출된 경우 `Commandstats` 섹션에 나열됩니다.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>비동기 복제

표준 또는 프리미엄 계층의 Redis 인스턴스에 대한 모든 Azure 캐시는 마스터 노드와 하나 이상의 복제본으로 구성됩니다. 데이터는 백그라운드 프로세스를 사용하여 마스터에서 복제본으로 비동기적으로 복사됩니다. [redis.io](https://redis.io/topics/replication) 웹 사이트는 Redis 데이터 복제가 일반적으로 작동하는 방식을 설명합니다. 클라이언트가 Redis에 자주 쓰는 시나리오의 경우 이 복제가 즉시 보장되므로 부분적인 데이터 손실이 발생할 수 있습니다. 예를 들어 클라이언트가 키를 기록한 *후* 마스터가 다운되지만 백그라운드 프로세스에서 해당 키를 복제본으로 보낼 수 *있는* 경우 복제본이 새 마스터로 인계될 때 키가 손실됩니다.

## <a name="major-or-complete-loss-of-keys"></a>키의 주요 또는 완전 손실

캐시에서 대부분의 키 또는 모든 키가 사라진 경우 다음과 같은 가능한 원인을 확인하십시오.

| 원인 | 설명 |
|---|---|
| [키 플러싱](#key-flushing) | 키가 수동으로 제거되었습니다. |
| [잘못된 데이터베이스 선택](#incorrect-database-selection) | Redis용 Azure 캐시는 기본이 아닌 데이터베이스를 사용하도록 설정됩니다. |
| [인스턴스 오류 다시 수정](#redis-instance-failure) | Redis 서버를 사용할 수 없습니다. |

### <a name="key-flushing"></a>키 플러싱

클라이언트는 [FLUSHDB](https://redis.io/commands/flushdb) 명령을 호출하여 *단일* 데이터베이스의 모든 키를 제거하거나 [FLUSHALL을](https://redis.io/commands/flushall) 호출하여 Redis 캐시의 *모든* 데이터베이스에서 모든 키를 제거할 수 있습니다. 키가 플러시되었는지 확인하려면 [INFO](https://redis.io/commands/info) 명령을 사용합니다. 이 `Commandstats` 섹션에서는 **FLUSH** 명령이 호출되었는지 여부를 보여 주십습니다.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>잘못된 데이터베이스 선택

Redis용 Azure 캐시는 기본적으로 **db0** 데이터베이스를 사용합니다. 다른 데이터베이스(예: **db1)로**전환하여 키를 읽으려고 하면 Redis용 Azure 캐시에서 키를 찾을 수 없습니다. 모든 데이터베이스는 논리적으로 분리된 단위이며 다른 데이터 집합을 보유합니다. [SELECT](https://redis.io/commands/select) 명령을 사용하여 사용 가능한 다른 데이터베이스를 사용하고 각 데이터베이스에서 키를 찾습니다.

### <a name="redis-instance-failure"></a>인스턴스 오류 다시 수정

Redis는 인메모리 데이터 저장소입니다. 데이터는 Redis 캐시를 호스트하는 물리적 또는 가상 시스템에 보관됩니다. 기본 계층의 Redis 인스턴스에 대한 Azure 캐시는 단일 가상 시스템(VM)에서만 실행됩니다. 해당 VM이 다운되면 캐시에 저장한 모든 데이터가 손실됩니다. 

표준 및 프리미엄 계층의 캐시는 복제된 구성에서 두 개의 VM을 사용하여 데이터 손실에 대한 복원력을 훨씬 높입니다. 이러한 캐시의 마스터 노드가 실패하면 복제본 노드가 인계되어 자동으로 데이터를 처리합니다. 이러한 VM은 오류 및 업데이트에 대한 별도의 도메인에 위치하여 둘 다 동시에 사용할 수 없게 될 가능성을 최소화합니다. 그러나 주요 데이터 센터 가동 중단이 발생하는 경우에도 VM은 여전히 함께 다운될 수 있습니다. 이러한 드문 경우지만 데이터가 손실됩니다.

[Redis 데이터 지속성](https://redis.io/topics/persistence) 및 [지역 복제를](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) 사용하여 이러한 인프라 오류에 대한 데이터 보호를 개선하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)

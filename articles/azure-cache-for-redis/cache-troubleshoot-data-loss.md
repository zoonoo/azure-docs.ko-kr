---
title: Redis 데이터 손실에 대 한 Azure 캐시 문제 해결 | Microsoft Docs
description: Redis 용 Azure Cache를 사용 하 여 데이터 손실 문제를 해결 하는 방법 알아보기
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 523f4a302eb1f4679eb34bc959efc895fa5408ec
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821049"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Redis 데이터 손실에 대 한 Azure 캐시 문제 해결

이 섹션에서는 Azure Cache for Redis에서 발생할 수 있는 실제 또는 인식 된 데이터 손실을 진단 하는 방법을 설명 합니다.

- [키의 부분 손실](#partial-loss-of-keys)
- [키의 주 또는 전체 손실](#major-or-complete-loss-of-keys)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="partial-loss-of-keys"></a>키의 부분 손실

Redis은 메모리에 저장 된 키를 임의로 삭제 하지 않습니다. 그러나 만료 또는 제거 정책 뿐만 아니라 명시적인 키 삭제 명령에 대 한 응답으로 키를 제거 합니다. 또한 Premium 또는 Standard Azure Cache for Redis의 마스터 노드에 기록 된 키를 복제본에서 바로 사용할 수 없습니다. 데이터는 비동기 및 비 블로킹 방식으로 마스터에서 복제본으로 복제 됩니다.

캐시에서 키가 사라진 경우 다음을 확인 하 여 원인을 확인할 수 있습니다.

| 원인 | 설명 |
|---|---|
| [키 만료](#key-expiration) | 키가 설정 된 시간 제한으로 인해 키가 제거 됩니다. |
| [키 제거](#key-eviction) | 메모리가 부족 하 여 키가 제거 됩니다. |
| [키 삭제](#key-deletion) | 명시적 delete 명령으로 키를 제거 합니다. |
| [비동기 복제](#async-replication) | 데이터 복제 지연으로 인해 복제본에서 키를 사용할 수 없습니다. |

### <a name="key-expiration"></a>키 만료

Redis는 시간 제한이 할당 되 고 해당 기간이 경과 하면 자동으로 키를 제거 합니다. Redis 키 만료와 관련 된 자세한 내용은 [만료](http://redis.io/commands/expire) 명령 설명서에서 확인할 수 있습니다. 제한 시간 값도 [set](http://redis.io/commands/set), [setex](https://redis.io/commands/setex), [getset](https://redis.io/commands/getset)및 기타 \*STORE 명령을 통해 설정할 수 있습니다.

[INFO](http://redis.io/commands/info) 명령을 사용 하 여 만료 된 키 수에 대 한 통계를 얻을 수 있습니다. *통계* 섹션에는 만료 된 키의 총 수가 표시 됩니다. *Keyspace* 섹션에서는 시간 제한 및 평균 시간 제한 값을 사용 하 여 키 수에 대 한 추가 정보를 제공 합니다.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

또한 캐시의 진단 메트릭을 확인 하 여 키가 누락 된 시간과 만료 된 키의 스파이크 사이에 상관 관계가 있는지 확인할 수 있습니다. Keyspace 알림 또는 모니터를 사용 하 여 이러한 유형의 문제를 디버깅 하는 방법에 대 한 자세한 내용은 [부록](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 을 참조 하세요.

### <a name="key-eviction"></a>키 제거

Redis에는 데이터를 저장 하기 위한 메모리 공간이 필요 합니다. 필요한 경우 사용 가능한 메모리를 확보 하기 위해 키를 제거 합니다. [INFO](http://redis.io/commands/info) 명령에서 **used_memory** 또는 **used_memory_rss** 값이 구성 된 **maxmemory** 설정을 사용 하는 경우 Redis는 [캐시 정책을](http://redis.io/topics/lru-cache)기반으로 메모리에서 키 제거를 시작 합니다.

[INFO](http://redis.io/commands/info) 명령을 사용 하 여 제거 된 키 수를 모니터링할 수 있습니다.

```
# Stats

evicted_keys:13224
```

또한 캐시의 진단 메트릭을 살펴보면 키가 누락 된 시간과 제거 된 키의 스파이크 간에 상관 관계가 있는지 확인할 수 있습니다. Keyspace 알림 또는 모니터를 사용 하 여 이러한 유형의 문제를 디버깅 하는 방법에 대 한 자세한 내용은 [부록](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 을 참조 하세요.

### <a name="key-deletion"></a>키 삭제

Redis 클라이언트는 [DEL](http://redis.io/commands/del) 또는 [hdel](http://redis.io/commands/hdel) 명령을 실행 하 여 Redis에서 키를 명시적으로 제거할 수 있습니다. [INFO](http://redis.io/commands/info) 명령을 사용 하 여 삭제 작업의 수를 추적할 수 있습니다. DEL 또는 HDEL 명령이 호출 되 면 *Commandstats* 섹션에 나열 됩니다.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>비동기 복제

Standard 또는 Premium 계층의 Redis에 대 한 모든 Azure Cache는 마스터 노드 및 하나 이상의 복제본으로 구성 됩니다. 백그라운드 프로세스를 사용 하 여 데이터를 마스터에서 복제본으로 비동기적으로 복사 합니다. [Redis.io](http://redis.io/topics/replication) 웹 사이트에서는 redis 데이터 복제가 일반적으로 작동 하는 방식을 설명 합니다. 클라이언트가 자주 Redis에 쓰는 시나리오의 경우이 복제가 즉각적으로 보장 되기 때문에 부분 데이터 손실이 발생할 수 있습니다. 예를 들어 클라이언트가 키를 작성 _한 후_ 에 마스터의 작동이 중단 된 경우에도 백그라운드 프로세스에서 복제본에이 키를 보낼 수 있게 _되기 전에_ 복제본이 새 마스터로 전환 될 때 키가 손실 됩니다.

## <a name="major-or-complete-loss-of-keys"></a>키의 주 또는 전체 손실

캐시에서 대부분 또는 모든 키가 사라진 경우 다음을 확인 하 여 원인을 확인할 수 있습니다.

| 원인 | 설명 |
|---|---|
| [키 플러시](#key-flushing) | 키를 수동으로 제거 했습니다. |
| [잘못 된 데이터베이스 선택](#incorrect-database-selection) | Redis가 기본이 아닌 데이터베이스를 사용 하도록 설정 되어 있습니다. |
| [Redis 인스턴스 오류](#redis-instance-failure) | 명시적 delete 명령으로 키를 제거 합니다. |

### <a name="key-flushing"></a>키 플러시

클라이언트는 Redis cache의 **모든** 데이터베이스에서 모든 키를 제거 하도록 [flushdb](http://redis.io/commands/flushdb) 명령을 호출 하 여 **단일** 데이터베이스의 모든 키를 [제거 하거나 모든](http://redis.io/commands/flushall) 키를 제거할 수 있습니다. [INFO](http://redis.io/commands/info) 명령을 사용 하 여 키가 플러시 되었는지 확인할 수 있습니다. *Commandstats* 섹션에서 FLUSH 명령이 호출 되었는지 여부를 표시 합니다.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>잘못 된 데이터베이스 선택

Azure Cache for Redis는 기본적으로 **d b 0** 데이터베이스를 사용 합니다. 다른 데이터베이스 (예: db1)로 전환 하 고 해당 파일에서 키를 읽으려고 하는 경우 모든 데이터베이스가 논리적으로 별도의 단위이 고 다른 데이터 집합을 보유 하 고 있으므로 Redis에서 해당 데이터베이스를 찾지 못합니다. [SELECT](http://redis.io/commands/select) 명령을 사용 하 여 사용 가능한 다른 데이터베이스를 사용 하 고 각 데이터베이스에서 키를 찾습니다.

### <a name="redis-instance-failure"></a>Redis 인스턴스 오류

Redis는 메모리 내 데이터 저장소입니다. 데이터는 Redis를 호스트 하는 물리적 또는 가상 컴퓨터에 유지 됩니다. 기본 계층의 Redis 용 Azure Cache 인스턴스는 단일 VM (가상 컴퓨터) 에서만 실행 됩니다. 해당 VM이 다운 되 면 캐시에 저장 한 모든 데이터가 손실 됩니다. Standard 및 Premium 계층의 캐시는 복제 된 구성에서 두 개의 Vm을 사용 하 여 데이터 손실에 대해 훨씬 높은 복원 력을 제공 합니다. 이러한 캐시의 마스터 노드에 오류가 발생 하면 복제본 노드가 데이터를 자동으로 처리 하는 데 사용 됩니다. 이러한 Vm은 둘 다 동시에 사용할 수 없게 될 가능성을 최소화 하기 위해 별도의 장애 도메인 및 업데이트 도메인에 있습니다. 그러나 주요 데이터 센터 가동 중단이 발생할 경우 Vm은 계속 해 서 사용할 수 있습니다. 이러한 드문 경우에는 데이터가 손실 됩니다.

이러한 인프라 오류 로부터 데이터 보호를 향상 시키려면 [Redis 데이터 지 속성](http://redis.io/topics/persistence) 및 [지역에서 복제](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) 를 사용 하는 것을 고려해 야 합니다.

## <a name="additional-information"></a>추가 정보

- [Redis 서버 쪽 문제에 대 한 Azure 캐시 문제 해결](cache-troubleshoot-server.md)
- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)

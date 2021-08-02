---
title: Azure Cache for Redis의 모범 사례
description: 다음 모범 사례에 따라 Azure Cache for Redis를 효과적으로 사용하는 방법을 알아봅니다.
author: carldc
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: cadaco
ms.openlocfilehash: b7a3a723af2b23b657a21f1df772fae13f050ca7
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413828"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis의 모범 사례

다음 모범 사례를 따르면 Azure Cache for Redis 인스턴스 사용의 성능 및 비용 효과적 측면을 극대화하는 데 도움이 될 수 있습니다.

## <a name="configuration-and-concepts"></a>구성 및 개념

* **프로덕션 시스템에 대해 표준 또는 프리미엄 계층을 사용합니다.**  기본 계층은 데이터 복제 및 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다.  C0 캐시는 공유 CPU 코어를 사용하고 메모리가 매우 적으며 “방해가 되는 이웃” 문제가 발생하기 쉽기 때문에 간단한 개발/테스트 시나리오에만 사용하는 것이 좋습니다.

* **Redis는 메모리 내 데이터 저장소입니다.**  [이 문서](cache-troubleshoot-data-loss.md)에서는 데이터 손실이 발생할 수 있는 몇 가지 시나리오에 대해 간략하게 설명합니다.

* [패치 및 장애 조치(failover)](cache-failover.md)로 인한 **연결 문제를 처리할 수 있도록 시스템을 개발합니다.**

* 메모리 부족 상태에서 **시스템 응답성을 향상시키기 위해 [maxmemory-reserved 설정](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)을 구성** 합니다.  쓰기 작업이 많은 워크로드나, 100KB 이상을 Redis에 저장하는 경우에는 충분한 예약 설정이 특히 중요합니다. 캐시 크기를 10%로 시작하고 쓰기 작업이 많은 로드의 경우 이 비율을 높입니다.

* **Redis는 더 작은 값에서 가장 잘 작동하므로** 더 큰 데이터를 여러 개의 키로 분할하는 것을 고려합니다.  [이 Redis 토론](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)에는 신중하게 고려해야 하는 몇 가지 고려 사항이 있습니다.  큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](cache-troubleshoot-client.md#large-request-or-response-size) 를 읽어보세요.

* **캐시 인스턴스와 애플리케이션을 항상 동일한 지역에 배치합니다.**  다른 지역의 캐시에 연결하면 대기 시간이 크게 증가하고 안정성이 떨어질 수 있습니다.  Azure 외부에서 연결할 수 있지만 *특히 Redis를 캐시로 사용* 하지 않는 것이 좋습니다.  Redis를 키/값 저장소로만 사용하는 경우에는 대기 시간이 중요하지 않을 수 있습니다.

* **연결을 다시 사용합니다.**  새 연결을 만들면 비용이 많이 들고 대기 시간이 증가하므로 가능한 연결을 최대한 다시 사용합니다. 새 연결을 만들도록 선택하는 경우 릴리스에 앞서 기존 연결을 종료해야 합니다(.NET, Java와 같은 관리되는 메모리 언어에서도 해당).

* **파이프라인을 사용합니다.**  [Redis 파이프라인](https://redis.io/topics/pipelining)을 지원하는 Redis 클라이언트를 선택해 보세요. 파이프라인은 네트워크를 효율적으로 사용하고 가능한 최상의 처리량을 얻는 데 도움이 됩니다.

* **최소 15초 이상의 *연결 시간 제한* 을 사용하도록 클라이언트 라이브러리를 구성** 하여 더 높은 CPU 조건에서도 연결할 수 있는 시스템 시간을 제공합니다.  연결 시간 제한 값이 짧으면 해당 시간 프레임 안에 연결이 설정된다고 보장할 수 없습니다.  무언가 잘못된 상황에서(높은 클라이언트 CPU, 서버 CPU 등) 연결 시간 제한 값이 짧으면 연결 시도가 실패하게 됩니다. 이 동작으로 인해 상황이 더 나빠지기도 합니다.  시간 제한이 더 짧으면 도움이 되기보다 오히려 문제를 악화시킵니다. 시스템이 다시 연결을 시도하는 프로세스를 강제로 다시 시작하여 *연결 -> 실패 -> 다시 시도* 루프로 이어질 수 있습니다. 일반적으로 연결 시간 제한은 15초 이상으로 유지하는 것이 좋습니다. 빠르게 다시 시도하고 실패하는 것보다 15초 또는 20초 후에 연결 시도에 성공하도록 하는 것이 좋습니다. 이러한 다시 시도 루프로 인해 시스템이 초기에 시간이 약간 더 걸리는 것보다 작동 중단이 더 길어질 수 있습니다.  
   > [!NOTE]
   > 이 참고 자료는 *연결 시도* 와 관련된 것이며 GET 또는 SET와 같은 *작업* 이 완료될 때까지 기다리는 시간과는 무관합니다.

* **비용이 많이 드는 작업 지양** - [KEYS](https://redis.io/commands/keys) 명령 같은 일부 Redis 작업은 비용이 *매우* 많이 들기 때문에 피해야 합니다.  자세한 내용은 [장기 실행 명령](cache-troubleshoot-server.md#long-running-commands)에 대한 몇 가지 고려 사항을 참조하세요.

* **TLS 암호화 사용** - Azure Cache for Redis에는 기본적으로 TLS 암호화 통신이 필요합니다.  현재 TLS 버전 1.0, 1.1, 1.2가 지원됩니다.  그러나 TLS 1.0 및 1.1은 업계 전반에서 사용하지 않도록 하는 과정에 있으므로 가능하다면 1.2를 사용합니다.  클라이언트 라이브러리 또는 도구에서 TLS를 지원하지 않는 경우 [Azure Portal](cache-configure.md#access-ports) 또는 [관리 API](/rest/api/redis/redis/update)를 통해 암호화되지 않은 연결을 사용하도록 설정할 수 있습니다.  암호화된 연결을 사용할 수 없는 경우에는 캐시 및 클라이언트 애플리케이션을 가상 네트워크에 배치하는 것이 좋습니다.  가상 네트워크 캐시 시나리오에서 사용되는 포트에 대한 자세한 내용은 다음 [표](cache-how-to-premium-vnet.md#outbound-port-requirements)를 참조하세요.

* **유휴 시간 제한** - Azure Cache for Redis는 현재 연결에 대해 10분의 유휴 시간 제한을 적용하므로 10분 미만으로 설정해야 합니다. 가장 일반적인 클라이언트 라이브러리에는 Azure Redis를 자동으로 ping하는 연결 유지 구성이 있습니다. 그러나 연결 유지 설정이 없는 클라이언트에서 고객 애플리케이션은 연결 유지를 담당합니다.

## <a name="memory-management"></a>메모리 관리

Redis 서버 인스턴스 내에서 고려할 수 있는 메모리 사용량과 관련된 몇 가지 사항이 있습니다.  몇 가지 예를 들면 다음과 같습니다.

* **애플리케이션에 대해 작동하는 [제거 정책](https://redis.io/topics/lru-cache)을 선택합니다.**  Azure Redis에 대한 기본 정책은 *volatile-lru* 로, TTL 값 설정이 있는 키만 제거 대상이 될 수 있습니다.  키에 TTL 값이 없으면 시스템에서 키를 제거하지 않습니다.  메모리가 부족할 때 시스템에서 키를 제거할 수 있게 하려는 경우 *allkeys-lru* 정책을 고려할 수 있습니다.

* **키에 만료 값을 설정합니다.**  만료는 메모리가 부족할 때까지 기다리지 않고 사전에 키를 제거합니다.  메모리 부족으로 인해 제거가 시작되면 서버에 추가 부하가 발생할 수 있습니다.  자세한 내용은 [EXPIRE](https://redis.io/commands/expire) 및 [EXPIREAT](https://redis.io/commands/expireat) 명령에 대한 설명서를 참조하세요.

## <a name="client-library-specific-guidance"></a>클라이언트 라이브러리 관련 참고 자료

* [StackExchange.Redis(.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
* [Java - 어떤 클라이언트를 사용해야 하나요?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
* [Lettuce(Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
* [Jedis(Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
* [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
* [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
* [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
* [ASP.NET 세션 상태 제공자](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="when-is-it-safe-to-retry"></a>언제 다시 시도해도 되나요?

아쉽게도 쉬운 답은 없습니다.  애플리케이션마다 다시 시도할 수 있는 작업과 그럴 수 없는 작업을 결정해야 합니다.  작업마다 서로 다른 요구 사항과 키 간 종속성이 있습니다.  고려할 사항은 다음과 같습니다.

* Redis에서 실행하도록 요청한 명령을 성공적으로 실행한 경우에도 클라이언트 쪽 오류가 발생할 수 있습니다.  예를 들면 다음과 같습니다.
  * 시간 제한은 클라이언트 쪽 개념입니다.  작업이 서버에 도달하면 클라이언트가 대기를 포기하는 경우에도 서버에서 명령을 실행합니다.  
  * 소켓 연결에서 오류가 발생하면 서버에서 작업이 실제로 실행되었는지 알 수 없습니다.  예를 들어 서버에서 요청을 처리한 후 클라이언트에서 응답을 받기 전에 연결 오류가 발생할 수 있습니다.
* 실수로 동일한 작업을 두 번 실행하는 경우 내 애플리케이션이 어떻게 반응하나요?  예를 들어 정수를 한 번이 아닌 두 번 증가시키면 어떻게 되나요?  내 애플리케이션이 여러 위치에서 동일한 키로 작성되고 있나요?  내 다시 시도 논리가 내 앱의 다른 부분에서 설정한 값을 덮어쓰면 어떻게 되나요?

오류 조건에서 코드가 작동하는 방식을 테스트하려면 [다시 부팅 기능](cache-administration.md#reboot)을 사용하는 것이 좋습니다. 다시 부팅하면 연결 문제가 애플리케이션에 미치는 영향을 확인할 수 있습니다.

## <a name="performance-testing"></a>성능 테스트

* 자체 성능 테스트를 작성하기 전에 **먼저 `redis-benchmark.exe`를 사용하여** 가능한 처리량/대기 시간을 확인하세요.  Redis 벤치마크 설명서는 [여기에서 확인](https://redis.io/topics/benchmarks)할 수 있습니다. `redis-benchmark.exe`는 TLS를 지원하지 않습니다. 테스트를 실행하기 전에 [포털을 통해 비-TLS 포트를 사용하도록 설정](cache-configure.md#access-ports)해야 합니다.  redis-benchmark.exe의 Windows 호환 버전은 [여기](https://github.com/MSOpenTech/redis/releases)에서 찾을 수 있습니다.
* 테스트에 사용되는 클라이언트 VM은 Redis 캐시 인스턴스와 **동일한 지역** 에 있어야 합니다.
* 더 나은 하드웨어를 유지하고 최상의 결과를 제공하므로 클라이언트에 대해 **Dv2 VM 시리즈를 사용** 하는 것이 좋습니다.
* 사용하는 클라이언트 VM의 **컴퓨팅 및 대역폭 수준이 적어도* 테스트 중인 캐시만큼인지 확인합니다.
* 캐시에 대해 **장애 조치(failover) 조건에서** 테스트합니다. 안정적인 상태 조건에서만 캐시의 성능을 테스트하지 않도록 하는 것이 중요합니다. 장애 조치 조건에서도 테스트하고 해당 시간 동안 캐시에서 CPU/서버 부하를 측정합니다. [주 노드를 다시 부팅](cache-administration.md#reboot)하여 장애 조치를 시작할 수 있습니다. 장애 조치 조건에서 테스트하면 장애 조치 조건 중 처리량과 대기 시간 측면에서 애플리케이션이 어떻게 동작하는지 확인할 수 있습니다. 장애 조치는 업데이트 중 및 계획되지 않은 이벤트 중에 발생할 수 있습니다. 이상적으로는 성능에 영향을 줄 수 있는 장애 조치 중에도 CPU/서버 부하 최대치가 80%를 넘지 않는 것이 좋습니다.
* **일부 캐시 크기** 는 4개 이상의 코어가 있는 VM에서 호스트됩니다. TLS 암호화/암호 해독 및 TLS 연결/연결 끊기 워크로드를 여러 코어에 분산하여 캐시 VM의 전체 CPU 사용량을 줄입니다.  [VM 크기 및 코어에 대한 자세한 내용은 여기를 참조하세요.](cache-planning-faq.md#azure-cache-for-redis-performance)
* Windows에서 작업하는 경우 클라이언트 컴퓨터에서 **VRSS를 사용하도록 설정합니다**.  [자세한 내용을 보려면 여기를 참조하세요](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  PowerShell 스크립트 예제:
   >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (Get-NetAdapter).Name

* **프리미엄 계층 Redis 인스턴스를 사용하는 것이 좋습니다**.  이 캐시 크기는 CPU 및 네트워크 둘 다에 대해 더 나은 하드웨어에서 실행되므로 더 나은 네트워크 대기 시간 및 처리량을 제공합니다.

   > [!NOTE]
   > 관찰된 성능 결과가 참조를 위해 [여기에 게시](cache-planning-faq.md#azure-cache-for-redis-performance)됩니다.   또한 SSL/TLS에서는 약간의 오버헤드가 더해지므로, 전송 암호화를 사용할 경우 대기 시간 및/또는 처리량이 다를 수 있습니다.

### <a name="redis-benchmark-examples"></a>Redis 벤치마크 예제

**사전 테스트 설정**: 아래 나열된 대기 시간 및 처리량 테스트 명령에 필요한 데이터를 사용하여 캐시 인스턴스를 준비합니다.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024

**대기 시간 테스트 방법**: 1k 페이로드를 사용하여 GET 요청을 테스트합니다.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**처리량 테스트 방법:** 1k 페이로드를 사용하여 GET 요청을 파이프라인으로 연결합니다.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50

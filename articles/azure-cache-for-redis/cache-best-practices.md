---
title: Azure Cache for Redis의 모범 사례
description: 이러한 모범 사례를 따라 Azure 캐시를 Redis에 효과적으로 사용하는 방법을 알아봅니다.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478617"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis의 모범 사례 
이러한 모범 사례를 따르면 Redis 인스턴스에 대한 Azure 캐시의 성능과 비용 효율적인 사용을 최대화할 수 있습니다.

## <a name="configuration-and-concepts"></a>구성 및 개념
 * **프로덕션 시스템에 표준 또는 프리미엄 계층을 사용합니다.**  기본 계층은 데이터 복제가 없고 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다.  C0 캐시는 공유 CPU 코어, 메모리가 적고 "시끄러운 이웃" 문제가 발생하기 쉽기 때문에 간단한 개발/테스트 시나리오를 위한 것입니다.

 * **Redis는 인메모리 데이터 저장소임을 기억하십시오.**  [이 문서에서는](cache-troubleshoot-data-loss.md) 데이터 손실이 발생할 수 있는 몇 가지 시나리오를 간략하게 설명합니다.

 * [패치 및 장애 조치로 인해](cache-failover.md)연결 **블립을 처리할 수 있도록 시스템을 개발합니다.**

 * 메모리 압력 조건에서 **시스템 응답성을 개선하도록 [최대 메모리 예약 설정을](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 구성합니다.**  쓰기 가 중부하 작업 부하나 Redis에 더 큰 값(100KB 이상)을 저장하는 경우 충분한 예약 설정이 특히 중요합니다. 캐시 크기의 10%로 시작하여 쓰기가 많은 로드가 있는 경우 이 비율을 늘려야 합니다.

 * **Redis는 작은 값에서 가장 잘 작동하므로**더 큰 데이터를 여러 키로 잘라두는 것이 좋습니다.  [이 Redis 토론에서는](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)신중하게 고려해야 할 몇 가지 고려 사항이 나열되어 있습니다.  큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](cache-troubleshoot-client.md#large-request-or-response-size) 를 읽어보세요.

 * **동일한 리전에서 캐시 인스턴스와 응용 프로그램을 찾습니다.**  다른 지역의 캐시에 연결하면 대기 시간이 대폭 증가하고 안정성이 떨어질 수 있습니다.  Azure 외부에서 연결할 수 있지만 특히 *Redis를 캐시로 사용하는 경우는*사용하지 않는 것이 좋습니다.  Redis를 키/값 저장소로만 사용하는 경우 대기 시간이 주요 관심사가 아닐 수 있습니다. 

 * **연결을 다시 사용합니다.**  새 연결을 만드는 것은 비용이 많이 들고 대기 시간이 증가하므로 가능한 한 연결을 재사용합니다. 새 연결을 만들도록 선택한 경우 .NET 또는 Java와 같은 관리되는 메모리 언어에서도 연결을 해제하기 전에 이전 연결을 닫아야 합니다.

 * **클라이언트 라이브러리를 구성하여 최소 15초의 *연결 시간 설정을* 사용하여**더 높은 CPU 조건에서도 연결할 수 있는 시스템 시간을 제공합니다.  작은 연결 시간 시간 값은 해당 시간 프레임에서 연결이 설정되는 것을 보장하지 않습니다.  문제가 발생하면(높은 클라이언트 CPU, 높은 서버 CPU 등) 연결 시간 시간 시간 지정 값이 짧으면 연결 시도가 실패합니다. 이 동작은 종종 나쁜 상황을 악화시게 합니다.  시간 시간이 짧아지므로 시스템이 다시 연결하려고 하는 프로세스를 다시 시작하도록 하여 문제를 악화시켜 *연결 -> 실패 -> 재시도* 루프로 이어질 수 있습니다. 일반적으로 연결 시간 시간을 15초 이상으로 두는 것이 좋습니다. 다시 시도만 빠르게 실패하도록 하는 것보다 15초 또는 20초 후에 연결 시도가 성공하도록 하는 것이 좋습니다. 이러한 재시도 루프는 시스템이 처음에 더 오래 걸리게 하는 경우보다 중단이 더 오래 지속될 수 있습니다.  
     > [!NOTE]
     > 이 지침은 연결 *시도에* 따라 다르며 GET 또는 SET과 같은 *작업이* 완료될 때까지 기다릴 수 있는 시간과 는 관련이 없습니다.
 
 * **비용이 많이 드는 작업 피하기** - [KEYS](https://redis.io/commands/keys) 명령과 같은 일부 Redis 작업은 *매우* 비싸므로 피해야 합니다.  자세한 내용은 장기 실행 명령에 대한 몇 가지 고려 사항을 [참조하십시오.](cache-troubleshoot-server.md#long-running-commands)

 * **TLS 암호화 사용** - Redis의 Azure 캐시에는 기본적으로 TLS 암호화 된 통신이 필요합니다.  TLS 버전 1.0, 1.1 및 1.2는 현재 지원됩니다.  그러나 TLS 1.0과 1.1은 업계 전반에서 중단되는 경로에 있으므로 가능한 경우 TLS 1.2를 사용하십시오.  클라이언트 라이브러리 또는 도구가 TLS를 지원하지 않는 경우 Azure 포털 또는 [관리 API를](https://docs.microsoft.com/rest/api/redis/redis/update) [통해](cache-configure.md#access-ports) 암호화되지 않은 연결을 사용하도록 설정하면 됩니다.  암호화된 연결이 불가능한 경우 캐시 및 클라이언트 응용 프로그램을 가상 네트워크에 배치하는 것이 좋습니다.  가상 네트워크 캐시 시나리오에서 사용되는 포트에 대한 자세한 내용은 이 [표를](cache-how-to-premium-vnet.md#outbound-port-requirements)참조하십시오.
 
## <a name="memory-management"></a>메모리 관리
Redis 서버 인스턴스 내에서 메모리 사용량과 관련된 몇 가지 사항을 고려해야 합니다.  몇 가지 예를 들면 다음과 같습니다.

 * **응용 프로그램에 적합한 [제거 정책을](https://redis.io/topics/lru-cache) 선택합니다.**  Azure Redis의 기본 정책은 *휘발성-루(volatile-lru)이며,* 이는 TTL 값이 설정된 키만 제거할 수 있음을 의미합니다.  TTL 값이 없는 키가 있으면 시스템에서 키를 제거하지 않습니다.  메모리 압력을 받는 경우 시스템에서 키를 제거하도록 하려면 *allkeys-lru* 정책을 고려할 수 있습니다.

 * **키에 만료 값을 설정합니다.**  만료되면 메모리 압력이 있을 때까지 기다리지 않고 사전에 키를 제거합니다.  메모리 압력으로 인해 제거가 시작되면 서버에 추가 로드가 발생할 수 있습니다.  자세한 내용은 [만료](https://redis.io/commands/expire) 및 [만료 명령설명서를](https://redis.io/commands/expireat) 참조하십시오.
 
## <a name="client-library-specific-guidance"></a>클라이언트 라이브러리 특정 지침
 * [스택익스체인지.레디스(.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - 어떤 클라이언트를 사용해야 합니까?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [양상추 (자바)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [제디스 (자바)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 세션 상태 공급자](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>언제 다시 시도하는 것이 안전합니까?
불행히도 쉬운 대답은 없습니다.  각 응용 프로그램은 다시 시도할 수 있는 작업과 다시 시도할 수 없는 작업을 결정해야 합니다.  각 작업에는 서로 다른 요구 사항과 키 간 종속성이 있습니다.  다음은 고려할 수 있는 몇 가지 사항입니다.

 * Redis가 실행하도록 요청한 명령을 성공적으로 실행하더라도 클라이언트 측 오류를 얻을 수 있습니다.  다음은 그 예입니다.
     - 시간 정은 클라이언트 측 개념입니다.  작업이 서버에 도달하면 클라이언트가 대기를 포기하더라도 서버가 명령을 실행합니다.  
     - 소켓 연결에서 오류가 발생하면 작업이 실제로 서버에서 실행되었는지 알 수 없습니다.  예를 들어 서버가 요청을 처리한 후 클라이언트가 응답을 받기 전에 연결 오류가 발생할 수 있습니다.
 *  실수로 동일한 작업을 두 번 실행하면 응용 프로그램이 어떻게 반응합니까?  예를 들어 정수를 한 번이 아닌 두 번 증분하면 어떻게 됩니까?  응용 프로그램이 여러 위치에서 동일한 키로 작성합니까?  재시도 논리가 앱의 다른 부분에서 설정한 값을 덮어쓰는 경우 어떻게 해야 합니까?

오류 조건에서 코드의 작동 방식을 테스트하려면 [재부팅 기능을](cache-administration.md#reboot)사용하는 것이 좋습니다. 재부팅을 사용하면 연결 블립이 응용 프로그램에 미치는 영향을 확인할 수 있습니다.

## <a name="performance-testing"></a>성능 테스트
 * 먼저 자체 perf 테스트를 작성하기 전에 가능한 처리량/대기 시간을 확인할 수 있습니다. ** `redis-benchmark.exe` **  Redis-벤치마크 문서는 [여기에서 찾을](https://redis.io/topics/benchmarks)수 있습니다.  redis-benchmarkTTLS를 지원 하지 않습니다., 그래서 테스트를 실행 하기 전에 [포털을 통해 비 TLS 포트를 사용 하도록](cache-configure.md#access-ports) 해야 합니다.  [redis-benchmark.exe의 윈도우 호환 버전은 여기에서 찾을 수 있습니다](https://github.com/MSOpenTech/redis/releases)
 * 테스트에 사용되는 클라이언트 VM은 Redis 캐시 **인스턴스와 동일한 지역에** 있어야 합니다.
 * **Dv2 VM 시리즈는** 더 나은 하드웨어를 가지고 있으며 최상의 결과를 제공할 수 있으므로 클라이언트에 사용하는 것이 좋습니다.
 * 사용하는 클라이언트 VM에 테스트 중인*캐시만큼의 계산및 대역폭이* 있는지 확인합니다. 
 * Windows에 있는 경우 클라이언트 컴퓨터에서 **VRSS를 사용하도록 설정합니다.**  [자세한 내용을 보려면 여기를 참조하세요](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  예제 파워쉘 스크립트:
     >PowerShell -실행정책 무제한 인에이블 넷어댑터RSS -Name(Get-NetAdapter). 이름 
     
 * **프리미엄 계층 Redis 인스턴스를 사용하는 것이 좋습니다.**  이러한 캐시 크기는 CPU와 네트워크 모두에서 더 나은 하드웨어에서 실행되기 때문에 네트워크 대기 시간 및 처리량이 더 좋습니다.
 
     > [!NOTE]
     > 우리의 관찰 된 성능 결과는 귀하의 참조를 위해 [여기에 게시됩니다.](cache-faq.md#azure-cache-for-redis-performance)   또한 SSL/TLS는 일부 오버헤드를 추가하므로 전송 암호화를 사용하는 경우 다른 대기 시간 및/또는 처리량을 얻을 수 있습니다.
 
### <a name="redis-benchmark-examples"></a>레디스 벤치마크 예제
**사전 테스트 설정**: 아래에 나열된 대기 시간 및 처리량 테스트 명령에 필요한 데이터로 캐시 인스턴스를 준비합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -aAccesskey -t SET -n 10 -d 1024 

**대기 시간을 테스트하려면**: 1k 페이로드를 사용하여 GET 요청을 테스트합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -aAccesskey -t GET -d 1024 -P 50 -c 4

**처리량을 테스트하려면 다음을 수행하십시오.** 1k 페이로드를 사용하여 파이프라인 GET 요청.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -aAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50

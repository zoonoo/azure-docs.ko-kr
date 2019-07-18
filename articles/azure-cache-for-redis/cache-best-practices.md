---
title: Azure Redis 캐시에 대 한 모범 사례
description: 이러한 모범 사례를 수행 하 여 Redis에 대 한 Azure Cache 효과적으로 사용 하는 방법에 알아봅니다.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452463"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Redis 캐시에 대 한 모범 사례 
이러한 모범 사례를 따라 성능 및 Redis 인스턴스에 대 한 Azure 캐시의 비용 효율적인 사용을 최대화할 수 있습니다.

## <a name="configuration-and-concepts"></a>구성 및 개념
 * **프로덕션 시스템에 대 한 표준 또는 프리미엄 계층을 사용 합니다.**  기본 계층은 데이터 복제 및 SLA를 사용 하 여 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다.  C0 캐시는 공유 CPU 코어, 약간의 메모리가 있는 "시끄러운 이웃" 문제 가능성이 있으므로 간단한 개발/테스트 시나리오에 대 한 것입니다.

 * **Redis는 메모리 내 데이터 저장소에 유의 합니다.**  [이 문서에서는](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 데이터 손실이 발생할 수 있는 몇 가지 시나리오에 간략하게 설명 합니다.

 * **연결 문제를 처리할 수 있도록 시스템을 개발할** [패치 및 장애 조치 때문](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)합니다.

 * **구성에 [maxmemory 예약 설정을](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 시스템 응답성을 개선 하기 위해** 메모리 부족 합니다.  이 설정은 쓰기 작업이 많은 워크 로드 또는 Redis에서 큰 값 (100 KB 이상)를 저장 하는 경우 특히 중요 합니다.  캐시 크기의 10%를 시작 하는 권장 다음 쓰기 집약적 로드 해야 하는 경우 증가 I. 참조 [고려할](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) 값을 선택 하는 경우.

 * **Redis는 시작 가장 작은 값으로**, 여러 키로 큰 데이터를 잘 것이 좋습니다.  [이 Redis 토론](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)를 신중 하 게 고려해 야 할 몇 가지 고려 사항이 나열 됩니다.  큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](cache-how-to-troubleshoot.md#large-requestresponse-size) 를 읽어보세요.

 * **동일한 지역에 캐시 인스턴스 및 응용 프로그램을 찾습니다.**  다른 지역에 캐시를 연결할 수 있습니다 훨씬 대기 시간이 증가할 줄이고 안정성.  Azure 외부에서 연결 하 고 수 하지 것이 좋습니다 *Redis 캐시를 사용 하는 경우에 특히*합니다.  Redis는 키/값 저장소만을 사용 하는 경우 대기 시간이 가장 우려 아닐 수 있습니다. 

 * **연결 다시 사용** -새 연결을 만드는 비용이 많이 드는 되며 대기 시간이 증가, 따라서 최대한 연결 다시 사용 합니다. 새 연결을 만드는 선택 하면 (예:.NET 또는 Java 언어를 관리 되는 메모리)에 해제 하기 전에 이전 연결을 닫을 수 있는지 확인 합니다.

 * **클라이언트 라이브러리를 사용 하 여 구성을 *연결 제한 시간* 15 초 이상**, 더 높은 CPU 조건에 연결 하는 시스템 시간이 제공 합니다.  작은 연결 제한 시간 값을 해당 시간 내에 연결이 설정 되는 보장 되지 않습니다.  무언가 잘못 되 면 (클라이언트의 높은 CPU, 높은 서버 CPU 및 등)을 짧은 연결 시간 제한 값을 하면 연결 시도가 실패 합니다. 이 문제는 잘못 된 상황이 더 악화 되는 합니다.  수 있도록, 대신 짧은 시간 초과를 야기할 수 있는 시스템에 다시 연결 시도의 프로세스를 다시 시작을 강제 적용 하 여 문제를 계획 한 *연결 실패-> 다시 시도->* 루프입니다. 일반적으로 연결 제한 시간을 15 초 이상 유지 하는 것이 좋습니다. 연결 시도가 신속 하 게 실패 하 게 하는 것 보다 15 또는 20 초 후에 성공할 수 있도록 하는 것이 좋습니다 다시 시도에 합니다. 이러한 다시 시도 루프에 가동 중단 하는 경우 시스템만 더 이상 처음 수행 하는 보다 더 오래 지속 될 수 있습니다.  
     > [!NOTE]
     > 이 지침에 따라 다릅니다를 *연결 시도가* 될 때까지 대기 하는 시간에 관계 없는 *작업* 예: GET 또는 완료로 설정 합니다.
 

 * **부담이 큰 명령을 방지** -일부 작업을 원하는 redis 합니다 [키 명령을](https://redis.io/commands/keys)는 *매우* 비용이 많이 드는 않아야 합니다.  자세한 내용은 참조 하세요. [부담이 큰 명령 주위에 몇 가지 고려 사항](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>메모리 관리
고려해 야 할 수 있는 Redis 서버 인스턴스 내에서 메모리 사용과 관련 된 몇 가지 있습니다.  몇 가지는 다음과 같습니다.

 * **선택는 [제거 정책을](https://redis.io/topics/lru-cache) 응용 프로그램에 대해 작동 합니다.**  Azure Redis에 대 한 기본 정책은 *volatile lru*, 즉, TTL이 있는 키만 값이 설정 제거를 사용할 수 있습니다.  키 TTL 값이 있으면 시스템 키를 제거 하지 않습니다.  아무 키나 메모리가 부족할 경우 제거 될 수 있도록 시스템을 고려해 야 할 수 있습니다 합니다 *allkeys lru* 정책입니다.

 * **키에 만료 값을 설정 합니다.**  그러면 메모리가 부족할 때까지 기다리지 않고 사전에 키를 제거 됩니다.  메모리가 중으로 인해 제거 시작지 않습니다 때 서버에서 추가 로드를 발생할 수 있습니다.  자세한 내용은 설명서를 참조 합니다 [만료](https://redis.io/commands/expire) 및 [ExpireAt](https://redis.io/commands/expireat) 명령입니다.
 
## <a name="client-library-specific-guidance"></a>클라이언트 라이브러리에 대 한 특정 지침
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-클라이언트 사용 해야 합니까?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [겨자 (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 세션 상태 공급자](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>때 안전 하 게 다시 시도 하는?
그러나는 쉽게 대답할 수 없습니다.  각 응용 프로그램 작업을 다시 시도할 수 있습니다 및 없는 결정 해야 합니다.  각 작업에 서로 다른 요구 사항과 키 간 종속성.  해야 할 몇 가지는 다음과 같습니다.

 * Redis에 성공적으로 실행 되도록 묻는 명령을 실행 하는 경우에 클라이언트 쪽 오류를 가져올 수 있습니다.  예를 들면 다음과 같습니다.
     - 시간 제한은 클라이언트 쪽 개념입니다.  작업 서버에 도달 하면 서버 클라이언트 대기를 포기 하는 경우에 명령이 실행 됩니다.  
     - 소켓 연결에 오류가 발생 하는 경우 작업 서버에서 실제로 실행 하는 경우 알 수 없는 합니다.  예를 들어, 클라이언트에서 응답을 받기 전에 있지만 서버에서 요청 처리 된 후 연결 오류가 발생할 수 있습니다.
 *  실수로 동일한 작업을 두 번 실행 하는 경우 응용 프로그램에 react는 하는 방법  예를 들어, 경우에 어떻게 합니까 증가 한 번만 대신 두 번 정수?  내 응용 프로그램에 쓰는 동일한 키를 여러 위치에서?  경우에 어떻게 내 재시도 논리는 내 앱의 다른 부분에서 설정 된 값을 덮어씁니다?

코드 오류 조건에서 작동 하는 방법을 테스트 하려는 경우 사용을 고려 합니다 [다시 부팅 기능](cache-administration.md#reboot)합니다. 따라서 연결 블 립이 응용 프로그램에 미치는 영향을 확인할 수 있습니다.

## <a name="performance-testing"></a>성능 테스트
 * **사용 하 여 시작 `redis-benchmark.exe`**  테스트 가능한 처리량/대기 시간에 대 한 느낌을 사용자 고유의 성능을 쓰기 전에 가져오려고 합니다.  Redis 벤치 마크 설명서 수 [여기](http://redis.io/topics/benchmarks)합니다.  해당 redis 벤치 마크 해야 하므로 SSL을 지원 하지 않습니다 [포털을 통해 비 SSL 포트를 사용 하도록 설정](cache-configure.md#access-ports) 테스트를 실행 하기 전에 합니다.  [Benchmark.exe의 호환 되는 windows 버전을 여기서 찾을 수 있습니다.](https://github.com/MSOpenTech/redis/releases)
 * 클라이언트 VM 테스트에 사용 해야 **동일한 지역에** Redis cache 인스턴스와 합니다.
 * **Dv2 VM 시리즈를 사용 하는 것이 좋습니다** 클라이언트에 대 한 하드웨어 성능이 더 우수 하 고 최상의 결과 제공 합니다.
 * 클라이언트가 사용 하는 VM에 있는지 확인 **만큼 계산 및 대역폭* 테스트할 캐시 합니다. 
 * **VRSS를 사용 하도록 설정** Windows 하는 경우 클라이언트 컴퓨터입니다.  [자세한 내용을 보려면 여기를 참조하세요](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Powershell 스크립트 예제:
     >PowerShell-ExecutionPolicy Unrestricted 사용 NetAdapterRSS-이름 (Get-netadapter). 이름 
     
 * **프리미엄 계층 Redis 인스턴스를 사용 하 여**입니다.  이러한 캐시 크기 CPU 및 네트워크 모두에 대 한 더 나은 하드웨어에서 실행 중인 때문에 더 나은 네트워크 대기 시간 및 처리량 갖습니다.
 
     > [!NOTE]
     > 이 관찰 되는 성능 결과가 [여기에서 게시 된](cache-faq.md#azure-cache-for-redis-performance) 참조용입니다.   또한 전송 암호화를 사용 하는 경우 다른 대기 시간 및/또는 처리량을 얻을 수 있도록 SSL/TLS 약간의 오버 헤드를 추가 함을 유의 합니다.
 
### <a name="redis-benchmark-examples"></a>Redis 벤치 마크 예제
**미리 설정을 테스트할**: 대기 시간 및 처리량 명령 아래에 나열 된 테스트에 필요한 데이터를 사용 하 여 캐시 인스턴스를 준비 합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**대기 시간을 테스트 하려면**: 1k 페이로드를 사용 하 여 GET 요청 테스트 합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**처리량 테스트:** 1k 페이로드를 사용 하 여 파이프라인 GET 요청을 사용합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50

---
title: Azure Cache for Redis 서버 쪽 문제 해결
description: 메모리 압력, 높은 CPU, 장기 실행 명령 또는 대역폭 제한과 같은 Redis용 Azure 캐시에서 일반적인 서버 측 문제를 해결하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277935"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Azure Cache for Redis 서버 쪽 문제 해결

이 섹션에서는 Redis용 Azure 캐시 또는 해당 시스템을 호스팅하는 가상 컴퓨터의 조건으로 인해 발생하는 문제 해결에 대해 설명합니다.

- [Redis 서버의 메모리 압력](#memory-pressure-on-redis-server)
- [높은 CPU 사용량 또는 서버 로드](#high-cpu-usage-or-server-load)
- [장기 실행 명령](#long-running-commands)
- [서버 쪽 대역폭 제한](#server-side-bandwidth-limitation)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="memory-pressure-on-redis-server"></a>Redis 서버의 메모리 압력

서버 쪽의 메모리 부족은 요청 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리 압력이 발생하면 시스템이 데이터를 디스크로 페이징할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다. 이 메모리 부족의 몇 가지 가능한 원인은 다음과 같습니다.

- 캐시는 최대 용량에 가까운 데이터로 채워져 있습니다.
- Redis는 높은 메모리 조각화를 보고 있습니다. 이 조각화는 Redis가 작은 개체에 최적화되어 있기 때문에 큰 개체를 저장하여 가장 자주 발생합니다.

Redis는 이 문제를 식별하는 데 도움이 되는 [INFO](https://redis.io/commands/info) 명령을 통해 "used_memory"와 "used_memory_rss"이라는 두 가지 통계를 노출합니다. 포털을 사용하여 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 수 있습니다.

메모리 사용량을 건강하게 유지하기 위해 몇 가지 가능한 변경 사항이 있습니다.

- [메모리 정책을 구성](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 하고 키에 만료 시간을 설정합니다. 조각화가 있는 경우 이 정책으로는 충분하지 않을 수 있습니다.
- [maxmemory-reserved 값을 구성합니다](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) .
- 크게 캐시된 개체를 더 작은 관련 개체로 분할합니다.
- 사용된 메모리와 같은 메트릭에 [대한 경고를 생성하여](cache-how-to-monitor.md#alerts) 잠재적인 영향에 대해 조기에 알릴 수 있습니다.
- 메모리 용량이 더 많은 더 큰 캐시 크기로 [확장합니다.](cache-how-to-scale.md)

## <a name="high-cpu-usage-or-server-load"></a>높은 CPU 사용량 또는 서버 로드

서버 로드또는 CPU 사용량이 높으면 서버가 적시에 요청을 처리할 수 없습니다. 서버가 응답속도가 느리고 요청 속도를 따라잡을 수 없습니다.

CPU 또는 서버 로드와 같은 [메트릭을 모니터링합니다.](cache-how-to-monitor.md#view-metrics-with-azure-monitor) CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다.

높은 서버 부하를 완화하기 위해 몇 가지 변경 사항을 수행할 수 있습니다.

- 아래에 언급된 장기 실행 [명령이나](#long-running-commands) 높은 메모리 압력으로 인해 페이지 오류가 발생하는 등 CPU 스파이크의 원인을 조사합니다.
- CPU 또는 서버 로드와 같은 메트릭에 대한 [경고를 생성하여](cache-how-to-monitor.md#alerts) 잠재적인 영향에 대해 조기에 알릴 수 있습니다.
- CPU 용량이 더 많은 더 큰 캐시 크기로 [확장합니다.](cache-how-to-scale.md)

## <a name="long-running-commands"></a>장기 실행 명령

일부 Redis 명령은 다른 명령보다 실행 비용이 더 비쌉습니다. [Redis 명령 설명서에는](https://redis.io/commands) 각 명령의 시간 복잡성이 표시됩니다. Redis 명령 처리는 단일 스레드이므로 실행하는 데 시간이 걸리는 명령은 그 다음에 오는 다른 모든 명령을 차단합니다. Redis 서버에 발급하는 명령을 검토하여 성능에 미치는 영향을 이해해야 합니다. 예를 들어 [KEYS](https://redis.io/commands/keys) 명령은 O(N) 작업이라는 것을 알지 못하고 자주 사용됩니다. [SCAN을](https://redis.io/commands/scan) 사용하여 CPU 스파이크를 줄임으로써 키를 방지할 수 있습니다.

[SLOWLOG](https://redis.io/commands/slowlog) 명령을 사용하여 서버에 대해 실행되는 비용이 많이 드는 명령을 측정할 수 있습니다.

## <a name="server-side-bandwidth-limitation"></a>서버 쪽 대역폭 제한

캐시 크기에 따라 네트워크 대역폭 용량이 다릅니다. 서버가 사용 가능한 대역폭을 초과하면 데이터가 클라이언트로 빠르게 전송되지 않습니다. 클라이언트 요청은 서버가 데이터를 클라이언트에 충분히 빠르게 푸시할 수 없기 때문에 시간 시간이 길어질 수 있습니다.

"캐시 읽기" 및 "캐시 쓰기" 메트릭을 사용하여 사용 중인 서버 측 대역폭의 양을 확인할 수 있습니다. 포털에서 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 수 있습니다.

네트워크 대역폭 사용량이 최대 용량에 가까운 상황을 완화하려면 다음을 수행하십시오.

- 클라이언트 호출 동작을 변경하여 네트워크 수요를 줄입니다.
- 캐시 읽기 또는 캐시 쓰기와 같은 메트릭에 [대한 경고를 생성하여](cache-how-to-monitor.md#alerts) 잠재적인 영향에 대해 조기에 알릴 수 있습니다.
- 더 많은 네트워크 대역폭 용량으로 더 큰 캐시 크기로 [확장합니다.](cache-how-to-scale.md)

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 클라이언트 쪽 문제 해결](cache-troubleshoot-client.md)
- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)

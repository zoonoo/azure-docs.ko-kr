---
title: Azure Cache for Redis 클라이언트 쪽 문제 해결
description: Redis 클라이언트 메모리 압력, 트래픽 버스트, 높은 CPU, 제한된 대역폭, 큰 요청 또는 큰 응답 크기와 같은 Redis용 Azure Cache를 사용하여 일반적인 클라이언트 측 문제를 해결하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277948"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Azure Cache for Redis 클라이언트 쪽 문제 해결

이 섹션에서는 응용 프로그램에서 사용하는 Redis 클라이언트의 조건으로 인해 발생하는 문제 해결에 대해 설명합니다.

- [Redis 클라이언트에 대한 메모리 압력](#memory-pressure-on-redis-client)
- [트래픽 버스트](#traffic-burst)
- [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
- [클라이언트 측 대역폭 제한](#client-side-bandwidth-limitation)
- [큰 요청 또는 응답 크기](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 클라이언트에 대한 메모리 압력

클라이언트 시스템의 메모리 압력은 캐시에서 응답 처리를 지연시킬 수 있는 모든 종류의 성능 문제로 이어집니다. 메모리 압력이 발생하면 시스템이 데이터를 디스크로 페이징할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다.

클라이언트의 메모리 압력을 감지하려면 다음을 수행합니다.

- 컴퓨터에서 메모리 사용량을 모니터링하여 사용 가능한 메모리를 초과하지 않는지 확인합니다.
- 클라이언트의 `Page Faults/Sec` 성능 카운터를 모니터링합니다. 정상적인 작동 중에 대부분의 시스템에 일부 페이지 오류가 있습니다. 요청 시간 시간에 해당하는 페이지 오류의 스파이크는 메모리 압력을 나타낼 수 있습니다.

클라이언트의 높은 메모리 압력은 다음과 같은 여러 가지 방법으로 완화될 수 있습니다.

- 클라이언트에서 메모리 사용량을 줄이기 위해 메모리 사용 패턴을 자세히 조사합니다.
- 더 많은 메모리를 사용하여 클라이언트 VM을 더 큰 크기로 업그레이드합니다.

## <a name="traffic-burst"></a>트래픽 버스트

나쁜 `ThreadPool` 설정과 결합된 트래픽 폭주는 Redis 서버에서 이미 보냈으나 클라이언트 쪽에서 아직 소비되지 않은 데이타의 처리 지연이 발생할 수 있습니다.

[예제를 `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) `ThreadPool` 사용하여 시간이 지남에 따라 통계가 어떻게 변하는지 모니터링합니다. 다음과 같이 `TimeoutException` StackExchange.Redis의 메시지를 사용하여 자세히 조사할 수 있습니다.

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

앞의 예외에는 다음과 같은 몇 가지 흥미로운 문제가 있습니다.

- `IOCP` 섹션과 `WORKER` 섹션에 `Min` 값보다 큰 `Busy` 값이 있습니다. 이 차이는 `ThreadPool` 설정을 조정해야 한다는 것을 의미합니다.
- 또한 `in: 64221`도 볼 수 있습니다: 이 값은 클라이언트의 커널 소켓 계층에서 64,211바이트가 수신되었지만 응용 프로그램에서 읽지 않았음을 나타냅니다. 이러한 차이는 일반적으로 응용 프로그램(예: StackExchange.Redis)이 서버가 보내는 것처럼 네트워크에서 데이터를 빠르게 읽지 않는다는 것을 의미합니다.

버스트 시나리오에서 스레드 풀이 빠르게 확장되도록 [ `ThreadPool` 설정을 구성할](cache-faq.md#important-details-about-threadpool-growth) 수 있습니다.

## <a name="high-client-cpu-usage"></a>클라이언트의 높은 CPU 사용량

클라이언트 CPU 사용량이 높음은 시스템이 요청받은 작업을 따라갈 수 없다는 것을 나타냅니다. 캐시가 응답을 신속하게 보냈더라도 클라이언트는 적시에 응답을 처리하지 못할 수 있습니다.

Azure 포털또는 컴퓨터의 성능 카운터를 통해 사용할 수 있는 메트릭을 사용하여 클라이언트의 시스템 전체 CPU 사용량을 모니터링합니다. 단일 *프로세스는* CPU 사용량이 낮을 수 있지만 시스템 전체 CPU는 높을 수 있으므로 프로세스 CPU를 모니터링하지 않도록 주의하십시오. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다. CPU가 높으면 `in: XXX` 트래픽 `TimeoutException` [버스트](#traffic-burst) 섹션에 설명된 대로 오류 메시지에 높은 값이 발생할 수도 있습니다.

> [!NOTE]
> StackExchange.Redis 1.1.603 이상은 `TimeoutException` 오류 메시지에 `local-cpu` 매트릭을 포함합니다. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
>

클라이언트의 높은 CPU 사용량을 완화하려면 다음을 수행하십시오.

- CPU 스파이크의 원인을 조사합니다.
- 더 많은 CPU 용량으로 클라이언트를 더 큰 VM 크기로 업그레이드합니다.

## <a name="client-side-bandwidth-limitation"></a>클라이언트 측 대역폭 제한

클라이언트 컴퓨터의 아키텍처에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 클라이언트가 네트워크 용량을 오버로드하여 사용 가능한 대역폭을 초과하면 서버가 보내는 시간만큼 빠르게 클라이언트 측에서 데이터가 처리되지 않습니다. 이 경우 시간이 초과될 수 있습니다.

[예제를 `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)사용하여 시간이 지남에 따라 대역폭 사용량이 어떻게 변하는지 모니터링합니다. 이 코드는 (Azure 웹 사이트와 같이) 권한이 제한된 일부 환경에서 성공적으로 실행되지 않을 수도 있습니다.

네트워크 대역폭 사용량을 줄이거나 네트워크 용량이 더 많은 클라이언트 VM 크기를 하나로 늘립니다.

## <a name="large-request-or-response-size"></a>큰 요청 또는 응답 크기

큰 요청/응답으로 시간 초과가 발생할 수 있습니다. 예를 들어 클라이언트에 구성된 시간 시간 값이 1초라고 가정합니다. 애플리케이션이 동시에 2개의 키(예: 'A' 및 'B')를 요청합니다(동일한 실제 네트워크 연결 사용). 대부분의 클라이언트는 요청 "파이프 라이닝"을 지원, 여기서 두 요청 'A'와 'B'는 응답을 기다리지 않고 차례로 전송됩니다. 서버는 응답을 동일한 순서로 전송합니다. 응답 'A'가 큰 경우 나중에 요청에 대한 대부분의 시간 시간을 먹을 수 있습니다.

다음 예제에서는 요청 'A' 및 'B'가 서버로 빠르게 전송됩니다. 서버는 응답을 'A'와 'B'로 빠르게 보내기 시작합니다. 데이터 전송 시간 때문에 응답 'B'는 서버가 신속하게 응답하더라도 응답 'A'가 시간 뒤에 대기해야 합니다.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

이 요청/응답은 측정하기 어렵습니다. 클라이언트 코드를 계측하여 큰 요청과 응답을 추적할 수 있습니다.

큰 응답 크기에 대한 해결 은 다양하지만 다음과 같습니다.

1. 몇 개의 큰 값이 아니라 많은 수의 작은 값에 맞게 응용 프로그램을 최적화합니다.
    - 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다.
    - 참조 게시물 [redis에 대 한 이상적인 값 크기 범위는 무엇입니까? 100KB가 너무 큽니까?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 작은 값을 권장하는 이유에 대한 자세한 내용을 참조하십시오.
1. VM 의 크기를 늘려 더 높은 대역폭 기능을 얻을 수 있습니다.
    - 클라이언트 또는 서버 VM에서 대역폭이 많을수록 더 큰 응답의 데이터 전송 시간이 줄어들 수 있습니다.
    - 두 컴퓨터의 현재 네트워크 사용량을 현재 VM 크기의 한계와 비교합니다. 서버에서만 또는 클라이언트에서만 더 많은 대역폭이 충분하지 않을 수 있습니다.
1. 응용 프로그램에서 사용하는 연결 개체 수를 늘립니다.
    - 라운드 로빈 접근 방식을 사용하여 다른 연결 개체에 대해 요청을 합니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)

---
title: 'Azure 백 엔드 연결 기능의 상호 운용성: 데이터 평면 분석 | Microsoft Docs'
description: 이 문서에서는 ExpressRoute, 사이트 간 VPN 및 Azure의 가상 네트워크 피어링 간의 상호 운용성을 분석하는 데 사용할 수 있는 테스트 설정의 데이터 평면 분석에 대해 설명합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: f4d94536a8c1b509e0ce435a764e69984b5d415e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425541"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Azure 백 엔드 연결 기능의 상호 운용성: 데이터 평면 분석

이 문서에서는 [테스트 설정][Setup]의 데이터 평면 분석에 대해 설명합니다. 테스트 설정의 [테스트 설정 구성][Configuration] 및 [제어 평면 분석][Control-Analysis]을 검토할 수도 있습니다.

데이터 평면 분석은 어떤 토폴로지 내의 한 로컬 네트워크(LAN 또는 가상 네트워크)에서 다른 로컬 네트워크로 트래버스하는 패킷에서 선택된 경로를 검토합니다. 두 로컬 네트워크 간의 데이터 경로가 반드시 대칭인 것은 아닙니다. 그러므로 이 문서에서는 로컬 네트워크에서 역방향 경로와 별도인 다른 네트워크까지 전달 경로를 분석해 보겠습니다.

## <a name="data-path-from-the-hub-vnet"></a>허브 VNet에서 오는 데이터 경로

### <a name="path-to-the-spoke-vnet"></a>스포크 VNet 경로

VNet(가상 네트워크) 피어링은 피어링된 두 VNet 간의 네트워크 브리지 기능을 에뮬레이트합니다. 허브 VNet에서 스포크 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

다음 그림은 Azure Network Watcher의 관점에서 본 허브 VNet과 스포크 VNet의 그래픽 연결 보기입니다.


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>분기 VNet 경로

허브 VNet에서 분기 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 허브 VNet의 Azure VPN Gateway에 있는 VPN 게이트웨이입니다. 두 번째 홉은 분기 VNet의 VPN 게이트웨이입니다. 분기 VNet의 VPN 게이트웨이 IP 주소는 허브 VNet에서 보급되지 않습니다. 세 번째 홉은 분기 VNet 상의 VM입니다.

다음 그림은 Network Watcher의 관점에서 본 분기 VNet과 스포크 VNet의 그래픽 연결 보기입니다.

[![2]][2]

같은 연결에 대해 다음 그림은 Network Watcher의 그리드 보기를 보여줍니다.

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>온-프레미스 위치 1 경로

허브 VNet에서 온-프레미스 위치 1의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 MSEE(Microsoft Enterprise Edge Router)로 가는 Azure ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 및 세 번째 홉은 CE(고객 에지) 라우터와 온-프레미스 위치 1 LAN IP입니다. 이러한 IP 주소는 허브 VNet에서 보급되지 않습니다. 네 번째 홉은 온-프레미스 위치 1의 VM입니다.


### <a name="path-to-on-premises-location-2"></a>온-프레미스 위치 2 경로

허브 VNet에서 온-프레미스 위치 2의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

이 경로 추적에서 첫 번째 홉은 MSEE에 대한 ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 및 세 번째 홉은 CE 라우터와 온-프레미스 위치 2 LAN IP입니다. 이러한 IP 주소는 허브 VNet에서 보급되지 않습니다. 네 번째 홉은 온-프레미스 위치 2의 VM입니다.

### <a name="path-to-the-remote-vnet"></a>원격 VNet 경로

허브 VNet에서 원격 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

이 경로 추적에서 첫 번째 홉은 MSEE에 대한 ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 홉은 원격 VNet의 게이트웨이 IP입니다. 두 번째 홉 IP 범위는 허브 VNet 내에서 보급되지 않습니다. 세 번째 홉은 원격 VNet의 VM입니다.

## <a name="data-path-from-the-spoke-vnet"></a>스포크 VNet에서 오는 데이터 경로

스포크 VNet은 허브 VNet의 네트워크 보기를 공유합니다. VNet 피어링을 통해 스포크 VNet은 허브 VNet의 원격 게이트웨이 연결을 마치 스포크 VNet에 직접 연결된 것처럼 사용합니다.

### <a name="path-to-the-hub-vnet"></a>허브 VNet 경로

스포크 VNet에서 허브 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>분기 VNet 경로

스포크 VNet에서 분기 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 허브 VNet의 VPN 게이트웨이입니다. 두 번째 홉은 분기 VNet의 VPN 게이트웨이입니다. 분기 VNet의 VPN 게이트웨이 IP 주소는 허브/스포크 VNet에서 보급되지 않습니다. 세 번째 홉은 분기 VNet 상의 VM입니다.

### <a name="path-to-on-premises-location-1"></a>온-프레미스 위치 1 경로

스포크 VNet에서 온-프레미스 위치 1의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

이 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 및 세 번째 홉은 CE 라우터와 온-프레미스 위치 1 LAN IP입니다. 이러한 IP 주소는 허브/스포크 VNet에서 보급되지 않습니다. 네 번째 홉은 온-프레미스 위치 1의 VM입니다.

### <a name="path-to-on-premises-location-2"></a>온-프레미스 위치 2 경로

스포크 VNet에서 온-프레미스 위치 2의 VM으로 가는 경로 추적 출력은 다음과 같습니다.


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

이 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 및 세 번째 홉은 CE 라우터와 온-프레미스 위치 2 LAN IP입니다. 이러한 IP 주소는 허브/스포크 VNet에서 보급되지 않습니다. 네 번째 홉은 온-프레미스 위치 2의 VM입니다.

### <a name="path-to-the-remote-vnet"></a>원격 VNet 경로

스포크 VNet에서 원격 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

이 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute 게이트웨이 터널 엔드포인트입니다. 두 번째 홉은 원격 VNet의 게이트웨이 IP입니다. 두 번째 홉 IP 범위는 허브/스포크 VNet 내에서 보급되지 않습니다. 세 번째 홉은 원격 VNet의 VM입니다.

## <a name="data-path-from-the-branch-vnet"></a>분기 VNet에서 오는 데이터 경로

### <a name="path-to-the-hub-vnet"></a>허브 VNet 경로

분기 VNet에서 허브 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 분기 VNet의 VPN 게이트웨이입니다. 두 번째 홉은 허브 VNet의 VPN 게이트웨이입니다. 허브 VNet의 VPN 게이트웨이 IP 주소는 원격 VNet에서 보급되지 않습니다. 세 번째 홉은 허브 VNet 상의 VM입니다.

### <a name="path-to-the-spoke-vnet"></a>스포크 VNet 경로

분기 VNet에서 스포크 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 분기 VNet의 VPN 게이트웨이입니다. 두 번째 홉은 허브 VNet의 VPN 게이트웨이입니다. 허브 VNet의 VPN 게이트웨이 IP 주소는 원격 VNet에서 보급되지 않습니다. 세 번째 홉은 스포크 VNet 상의 VM입니다.

### <a name="path-to-on-premises-location-1"></a>온-프레미스 위치 1 경로

분기 VNet에서 온-프레미스 위치 1의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

이 경로 추적에서, 첫 번째 홉은 분기 VNet의 VPN 게이트웨이입니다. 두 번째 홉은 허브 VNet의 VPN 게이트웨이입니다. 허브 VNet의 VPN 게이트웨이 IP 주소는 원격 VNet에서 보급되지 않습니다. 세 번째 홉은 기본 CE 라우터 상의 VPN 터널 종료 지점입니다. 네 번째 홉은 온-프레미스 위치 1의 내부 IP 주소입니다. 이 LAN IP 주소는 CE 라우터 외부에서 보급되지 않습니다. 다섯 번째 홉은 온-프레미스 위치 1의 대상 VM입니다.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>온-프레미스 위치 2 및 원격 VNet 경로

제어 평면 분석에서 설명했듯이, 분기 VNet은 네트워크 구성에 따라 온-프레미스 위치 2 또는 원격 VNet에 대한 가시성이 없습니다. 다음 ping 결과는 다음 정보를 확인합니다. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>온-프레미스 위치 1에서 오는 데이터 경로

### <a name="path-to-the-hub-vnet"></a>허브 VNet 경로

온-프레미스 위치 1에서 허브 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

아 경로 추적에서, 처음 두 홉은 온-프레미스 네트워크의 일부입니다. 세 번째 홉은 CE 라우터를 지향하는 기본 MSEE 인터페이스입니다. 네 번째 홉은 허브 VNet의 ExpressRoute 게이트웨이입니다. 허브 VNet의 ExpressRoute 게이트웨이 IP 범위는 온-프레미스 네트워크에 보급되지 않습니다. 다섯 번째 홉은 대상 VM입니다.

Network Watcher는 Azure 중심 보기만 제공합니다. 온-프레미스 관점에서 보자면, Azure 네트워크 성능 모니터를 사용합니다. 네트워크 성능 모니터는 Azure 외부 네트워크의 서버에 데이터 경로 분석용으로 설치할 수 있는 에이전트를 제공합니다.

다음 그림은 ExpressRoute를 통해 허브 VNet 상의 VM에 대해 실행한 온-프레미스 위치 1 VM 연결의 토폴로지 보기입니다.

[![4]][4]

앞서 설명했듯이, 테스트 설정은 온-프레미스 위치 1과 허브 VNet 간의 ExpressRoute에 대한 백업 연결로 사이트 간 VPN을 사용합니다. 백업 데이터 경로를 테스트하기 위해 온-프레미스 위치 1 기본 CE 라우터와 해당 MSEE 간의 ExpressRoute 연결 장애를 유도해 보겠습니다. ExpressRoute 연결 장애를 유도하기 위해 MSEE를 지향하는 CE 인터페이스를 종료합니다.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

다음 그림은 ExpressRoute 연결이 다운되었을 때 사이트 간 VPN 연결을 통해 허브 VNet 상의 VM에 대해 실행한 온-프레미스 위치 1 VM 연결의 토폴로지 보기입니다.

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>스포크 VNet 경로

온-프레미스 위치 1에서 스포크 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

ExpressRoute 기본 연결로 다시 돌아가서, 스포크 VNet을 지향하는 데이터 경로 분석을 수행해 보겠습니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

나머지 데이터 경로 분석을 위해 기본 ExpressRoute 1 연결을 불러옵니다.

### <a name="path-to-the-branch-vnet"></a>분기 VNet 경로

온-프레미스 위치 1에서 분기 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>온-프레미스 위치 2 경로

[제어 평면 분석][Control-Analysis]에서 설명했듯이, 온-프레미스 위치 1은 네트워크 구성에 따라 온-프레미스 위치 2에 대한 가시성이 없습니다. 다음 ping 결과는 다음 정보를 확인합니다. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>원격 VNet 경로

온-프레미스 위치 1에서 원격 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>온-프레미스 위치 2에서 오는 데이터 경로

### <a name="path-to-the-hub-vnet"></a>허브 VNet 경로

온-프레미스 위치 2에서 허브 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>스포크 VNet 경로

온-프레미스 위치 2에서 스포크 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>분기 VNet, 온-프레미스 위치 1 및 원격 VNet 경로

[제어 평면 분석][Control-Analysis]에서 설명했듯이, 온-프레미스 위치 1은 네트워크 구성에 따라 분기 VNet, 온-프레미스 위치 1 및 원격 VNet에 대한 가시성이 없습니다. 

## <a name="data-path-from-the-remote-vnet"></a>원격 VNet에서 오는 데이터 경로

### <a name="path-to-the-hub-vnet"></a>허브 VNet 경로

원격 VNet에서 허브 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>스포크 VNet 경로

원격 VNet에서 스포크 VNet의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>분기 VNet 및 온-프레미스 위치 2 경로

[제어 평면 분석][Control-Analysis]에서 설명했듯이, 원격 VNet은 네트워크 구성에 따라 분기 VNet 또는 온-프레미스 위치 2에 대한 가시성이 없습니다. 

### <a name="path-to-on-premises-location-1"></a>온-프레미스 위치 1 경로

원격 VNet에서 온-프레미스 위치 1의 VM으로 가는 경로 추적 출력은 다음과 같습니다.

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

ExpressRoute Microsoft 피어링을 사용하여 온-프레미스 네트워크와 Azure VNet 간에 개인적으로 데이터를 교환하도록 사이트 간 VPN을 구성할 수 있습니다. 이 구성을 사용하면 비밀성, 신뢰성 및 무결성을 유지하며 데이터를 교환할 수 있습니다. 데이터 교환은 재생 방지이기도 합니다. ExpressRoute Microsoft 피어링을 사용하여 터널 모드에서 사이트 간 IPsec VPN을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 사용하는 사이트 간 VPN 구성에 대한 기본 제한은 처리량입니다. IPsec 터널을 통한 처리량은 VPN Gateway 용량에 의해 제한됩니다. VPN Gateway 처리량은 ExpressRoute 처리량보다 낮습니다. 이 시나리오에서 매우 안전한 트래픽의 경우 IPsec 터널 및 모든 다른 트래픽의 경우 프라이빗 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN

ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 테스트 설정에서 설명한 것처럼, Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 만드는 데 사이트 간 VPN을 사용할 수 있습니다. ExpressRoute 및 사이트 간 VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 ExpressRoute를 우선 순위로 지정합니다. ExpressRoute와 사이트 간 VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 사이트 간 VPN 연결을 사용하기 전에 ExpressRoute 연결을 사용하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN의 공존 연결을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [Azure에서 허브-스포크 네트워크 토폴로지 구현][Hub-n-Spoke]을 참조하세요.

지역 내의 VNet 피어링에서 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

서로 다른 지역에 있는 분기 VNet 및 온-프레미스 네트워크를 허브 VNet을 통해 서로 통신하도록 할 수 있습니다. 이 구성에 대한 네이티브 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안은 허브의 라우팅에 대해 NVA(네트워크 가상 어플라이언스)를 사용하는 것입니다.

자세한 내용은 [VPN Gateway란?][VPN] 및 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.


## <a name="next-steps"></a>다음 단계

다음에 대해 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.
-   ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 개수를 알아봅니다.
-   ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 개수를 알아봅니다.
-   ExpressRoute의 다른 크기 조정 제한 사항에 대해 알아봅니다.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "허브 VNet에서 스포크 VNet으로 연결의 Network Watcher 보기"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "허브 VNet에서 분기 VNet으로 연결의 Network Watcher 보기"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "허브 VNet에서 분기 VNet으로 연결의 Network Watcher 그리드 보기"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "ExpressRoute 1을 통한 위치 1 VM에서 허브 VNet으로 연결의 네트워크 성능 모니터 보기"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "사이트 간 VPN을 통한 위치 1 VM에서 허브 VNet으로 연결의 네트워크 성능 모니터 보기"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering



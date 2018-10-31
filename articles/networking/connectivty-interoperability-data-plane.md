---
title: 'ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 데이터 평면 분석: Azure 백 엔드 연결 기능 상호 운용성 | Microsoft Docs'
description: 이 페이지는 ExpressRoute, 사이트 간 VPN 및 VNet 피어링 기능의 상호 운용성을 분석하기 위해 만들어진 테스트 설정에 대한 데이터 평면 분석을 제공합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947101"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 데이터 평면 분석

이 문서에서는 테스트 설정의 데이터 평면 분석을 탐색해 보겠습니다. 테스트 설정을 검토하려면 [테스트 설정][Setup]을 참조하세요. 테스트 설정 구성 세부 정보를 검토하려면 [테스트 설정 구성][Configuration]을 참조하세요. 테스트 설정의 제어 평면 분석을 검토하려면 [제어 평면 분석][Control-Analysis]을 참조하세요.

데이터 평면 분석은 어떤 토폴로지 내의 한 로컬 네트워크(LAN/VNet)에서 다른 로컬 네트워크로 전송하는 패킷에 의해 선택된 경로를 검토합니다. 두 로컬 네트워크 간의 데이터 경로가 반드시 대칭인 것은 아닙니다. 그러므로 이 문서에서는 역방향 경로와 별도로 한 로컬 네트워크에서 다른 네트워크까지 전달 경로를 분석해 보겠습니다.

##<a name="data-path-from-hub-vnet"></a>허브 VNet에서 오는 데이터 경로

###<a name="path-to-spoke-vnet"></a>스포크 VNet으로 가는 경로

VNet 피어링은 피어링된 두 VNet 간의 네트워크 브리지 기능을 에뮬레이트합니다. 허브 VNet에서 스포크 VNet의 VM으로 가는 경로 추적 출력을 아래에 나열합니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

다음 화면 클립은 Azure Network Watcher가 표시한 허브 VNet과 스포크 VNet의 그래픽 연결 뷰입니다.


[![1]][1]

###<a name="path-to-branch-vnet"></a>분기 VNet에 대한 경로

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 허브 VNet의 VPN GW입니다. 두 번째 홉은 해당 IP 주소가 허브 VNet 내에서 보급되지 않은 분기 VNet의 VPN GW입니다. 세 번째 홉은 분기 VNet 상의 VM입니다.

다음 화면 클립은 Azure Network Watcher가 표시한 허브 VNet과 분기 VNet의 그래픽 연결 뷰입니다.

[![2]][2]

동일한 연결에 대해 다음 화면 클립은 Azure Network Watcher가 표시하는 눈금 보기입니다.

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>온-프레미스 위치-1에 대한 경로

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 ExpressRoute GW 엔드포인트입니다. 두 번째 및 세 번째 홉은 각각 CE 라우터와 온-프레미스 위치 1 LAN IP이며, 이 IP 주소들은 허브 VNet 내에서 보급되지 않았습니다. 네 번째 홉은 온-프레미스 위치-1 상의 VM입니다.


###<a name="path-to-on-premises-location-2"></a>온-프레미스 위치-2에 대한 경로

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 ExpressRoute GW 엔드포인트입니다. 두 번째 및 세 번째 홉은 각각 CE 라우터와 온-프레미스 위치 2 LAN IP이며, 이 IP 주소들은 허브 VNet 내에서 보급되지 않았습니다. 네 번째 홉은 온-프레미스 위치-2 상의 VM입니다.

###<a name="path-to-remote-vnet"></a>원격 VNet에 대한 경로

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 ExpressRoute GW 엔드포인트입니다. 두 번째 홉은 원격 VNet의 GW IP입니다. 두 번째 홉 IP 범위는 허브 VNet 내에서 보급되지 않았습니다. 세 번째 홉은 원격 VNet 상의 VM입니다.

##<a name="data-path-from-spoke-vnet"></a>스포크 VNet에서 오는 데이터 경로

스포크 VNet은 허브 VNet의 네트워크 뷰를 공유한다는 점을 상기하십시오. VNet 피어링을 통해 스포크 VNet은 허브 VNet의 원격 게이트웨이 연결을 마치 스포크 VNet에 직접 연결된 것처럼 사용합니다.

###<a name="path-to-hub-vnet"></a>허브 VNet에 대한 경로

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>분기 VNet에 대한 경로

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 허브 VNet의 VPN GW입니다. 두 번째 홉은 해당 IP 주소가 허브/스포크 VNet 내에서 보급되지 않은 분기 VNet의 VPN GW입니다. 세 번째 홉은 분기 VNet 상의 VM입니다.

###<a name="path-to-on-premises-location-1"></a>온-프레미스 위치-1에 대한 경로

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute GW 엔드포인트입니다. 두 번째 및 세 번째 홉은 각각 CE 라우터와 온-프레미스 위치 1 LAN IP이며, 이 IP 주소들은 허브/스포크 VNet 내에서 보급되지 않았습니다. 네 번째 홉은 온-프레미스 위치-1 상의 VM입니다.

###<a name="path-to-on-premises-location-2"></a>온-프레미스 위치-2에 대한 경로

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute GW 엔드포인트입니다. 두 번째 및 세 번째 홉은 각각 CE 라우터와 온-프레미스 위치 2 LAN IP이며, 이 IP 주소들은 허브/스포크 VNet 내에서 보급되지 않았습니다. 네 번째 홉은 온-프레미스 위치-2 상의 VM입니다.

###<a name="path-to-remote-vnet"></a>원격 VNet에 대한 경로

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 MSEE에 대한 허브 VNet의 ExpressRoute GW 엔드포인트입니다. 두 번째 홉은 원격 VNet의 GW IP입니다. 두 번째 홉 IP 범위는 허브/스포크 VNet 내에서 보급되지 않았습니다. 세 번째 홉은 원격 VNet 상의 VM입니다.

##<a name="data-path-from-branch-vnet"></a>분기 VNet에서 오는 데이터 경로

###<a name="path-to-hub-vnet"></a>허브 VNet에 대한 경로

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 분기 VNet의 VPN GW입니다. 두 번째 홉은 해당 IP 주소가 원격 VNet 내에서 보급되지 않은 허브 VNet의 VPN GW입니다. 세 번째 홉은 허브 VNet 상의 VM입니다.

###<a name="path-to-spoke-vnet"></a>스포크 VNet으로 가는 경로

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 분기 VNet의 VPN GW입니다. 두 번째 홉은 해당 IP 주소가 원격 VNet 내에서 보급되지 않고 세 번째 홉이 스포크 VNet 상의 VM인 허브 VNet의 VPN GW입니다.

###<a name="path-to-on-premises-location-1"></a>온-프레미스 위치-1에 대한 경로

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

위의 경로 추적에서 첫 번째 홉은 분기 VNet의 VPN GW입니다. 두 번째 홉은 해당 IP 주소가 원격 VNet 내에서 보급되지 않은 허브 VNet의 VPN GW입니다. 세 번째 홉은 기본 CE 라우터 상의 VPN 터널 종료 지점입니다. 네 번째 홉은 CE 라우터 외부에 보급되지 않은 온-프레미스 위치-1 LAN IP 주소의 내부 IP 주소입니다. 다섯 번째 홉은 온-프레미스 위치-1 상의 대상 VM입니다.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>온-프레미스 위치-2 및 원격 VNet에 대한 경로

앞서 제어 평면 분석에서 설명한 것처럼 분기 VNet은 네트워크 구성에 따라 온-프레미스 위치-2 또는 원격 VNet에 대한 가시성이 없습니다. 다음 ping 결과는 이 사실을 확인합니다. 

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

##<a name="data-path-from-on-premises-location-1"></a>온-프레미스 위치-1에서 오는 데이터 경로

###<a name="path-to-hub-vnet"></a>허브 VNet에 대한 경로

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

위의 추적 경로에서 처음 두 홉은 온-프레미스 네트워크의 일부입니다. 세 번째 홉은 CE 라우터를 지향하는 기본 MSEE 인터페이스입니다. 네 번째 홉은 해당 IP 범위가 온-프레미스 네트워크에 보급되지 않은 허브 VNet의 ExpressRoute G/W입니다. 다섯 번째 홉은 대상 VM입니다.

Azure Network Watcher는 Azure 중심 뷰만 제공합니다. 그러므로 온-프레미스 중심 뷰에 대해서는 Azure NPM(네트워크 성능 모니터)을 사용했습니다. NPM은 Azure 외부의 네트워크에 설치된 서버일 수 있는 에이전트를 제공하며 데이터 경로 분석을 수행합니다.

다음 화면 클립은 ExpressRoute를 통해 허브 VNet 상의 VM에 대해 실행한 온-프레미스 위치-1 VM 연결의 토폴로지 뷰입니다.

[![4]][4]

테스트 설정은 온-프레미스 위치-1과 허브 VNet 간의 ExpressRoute에 대한 백업 연결로 사이트 간 VPN을 사용합니다. 백업 데이터 경로를 테스트하기 위해 MSEE를 지향하는 CE 인터페이스를 종료하여 온-프레미스 위치-1 기본 CE 라우터와 해당 MSEE 간의 ExpressRoute 링크 고장을 유도해 보겠습니다.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

다음 화면 클립은 
ExpressRoute 연결이 다운되었을 때 사이트 간 VPN 연결을 통해 허브 VNet 상의 VM에 대해 실행한 온-프레미스 위치-1 VM 연결의 토폴로지 뷰입니다.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>스포크 VNet으로 가는 경로

ExpressRoute 기본 연결로 다시 돌아가서 스포크 VNet을 지향하는 데이터 경로 분석을 수행해 보겠습니다.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

나머지 데이터 분석을 위해 기본 ExpressRoute-1 연결을 불러옵니다.

###<a name="path-to-branch-vnet"></a>분기 VNet에 대한 경로

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>온-프레미스 위치-2에 대한 경로

앞서 제어 평면 분석에서 설명한 것처럼 온-프레미스 위치-1은 네트워크 구성에 따라 온-프레미스 위치-2에 대한 가시성이 없습니다. 다음 ping 결과는 이 사실을 확인합니다. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>원격 VNet에 대한 경로

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>온-프레미스 위치-2에서 오는 데이터 경로

###<a name="path-to-hub-vnet"></a>허브 VNet에 대한 경로

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>스포크 VNet으로 가는 경로

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>분기 VNet, 온-프레미스 위치-1 및 원격 VNet에 대한 경로

앞서 제어 평면 분석에서 설명한 것처럼 온-프레미스 위치-1은 네트워크 구성에 따라 분기 VNet, 온-프레미스 위치-1 및 원격 VNet에 대한 가시성이 없습니다. 

##<a name="data-path-from-remote-vnet"></a>원격 VNet에서 오는 데이터 경로

###<a name="path-to-hub-vnet"></a>허브 VNet에 대한 경로

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>스포크 VNet으로 가는 경로

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>분기 VNet 및 온-프레미스 위치-2에 대한 경로

앞서 제어 평면 분석에서 설명한 것처럼 원격 VNet은 네트워크 구성에 따라 분기 VNet 및 온-프레미스 위치-2에 대한 가시성이 없습니다. 


### <a name="path-to-on-premises-location-1"></a>온-프레미스 위치-1에 대한 경로

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>추가 참고 자료

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결 사용

####<a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

사이트 간 VPN은 ExpressRoute Microsoft 피어링을 통해 온-프레미스 네트워크와 Azure VNet 간에 비밀성, 재생 방지, 신뢰성 및 무결성을 가지고 개인적으로 데이터를 교환하도록 구성할 수 있습니다. ExpressRoute Microsoft 피어링을 통해 터널 모드에서 사이트 간 IPSec VPN을 구성하는 방법에 관한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 통해 S2S VPN을 구성할 때 주요 제한은 처리량입니다. IPSec 터널을 통한 처리량은 VPN GW 용량에 의해 제한됩니다. VPN GW 처리량은 ExpressRoute 처리량에 비해 작습니다. 그러한 시나리오에서 매우 안전한 트래픽의 경우 IPSec 터널 및 모든 다른 트래픽의 경우 개인 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN
ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 이 테스트 설정에서 수행한 것처럼 지정된 Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 원하는 경우 사이트 간 VPN을 사용하여 그렇게 할 수 있습니다. ExpressRoute 및 S2S VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 S2S VPN보다 ExpressRoute를 선호합니다. ExpressRoute와 S2S VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 S2S VPN 연결보다 ExpressRoute를 선호하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN이 공존하는 연결에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet(가상 네트워크)입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [허브 및 스포크 아키텍처][Hub-n-Spoke]를 참조하세요.

하위 지역 내에서 VNet 피어링을 통해 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

분기 VNet(다른 하위 지역의)과 온-프레미스 네트워크가 허브 VNet을 통해 서로 통신하도록 하려는 경우 원시 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안 옵션은 허브의 라우팅을 위해 NVA를 사용하는 것입니다.

VPN Gateway 구성에 대해서는 [VPN Gateway 구성][VPN]을 참조하세요. 고가용성 NVA 배포에 대해서는 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 수, ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 수 또는 ExpressRoute의 다른 규모 제한에 대해 알아보려면 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "허브 VNet에서 스포크 VNet으로 연결의 Network Watcher 뷰"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "허브 VNet에서 분기 VNet으로 연결의 Network Watcher 뷰"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "허브 VNet에서 분기 VNet으로 연결의 Network Watcher 눈금 보기"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "ExpressRoute 1을 통한 위치-1 VM에서 허브 VNet으로 연결의 네트워크 성능 모니터 뷰"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "S2S VPN을 통한 위치-1 VM에서 허브 VNet으로 연결의 네트워크 성능 모니터 뷰"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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





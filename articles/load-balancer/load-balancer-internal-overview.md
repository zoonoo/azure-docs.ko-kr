---
title: Azure Load Balancer 개요 | Microsoft Docs
description: Azure에서 내부 부하 분산 장치가 작동하는 방식과 내부 끝점을 구성하는 시나리오.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Azure Internal Load Balancer 개요



Azure ILB(Internal Load Balancer)는 클라우드 서비스 내부에 있는 리소스 또는 VPN을 사용하여 Azure 인프라에 액세스하는 리소스로만 트래픽을 보냅니다. 이런 점에서 ILB는 인터넷 연결 부하 분산 장치와 다릅니다. Azure 인프라는 클라우드 서비스의 부하가 분산된 VIP(가상 IP) 주소 또는 가상 네트워크에 대한 액세스를 제한합니다. VIP 주소와 가상 네트워크는 인터넷 끝점에 직접 노출되지 않습니다. 내부 LOB(기간 업무) 응용 프로그램은 Azure에서 실행되며 Azure 내에서 또는 온-프레미스 리소스에서 액세스 할 수 있습니다.

## <a name="why-you-might-need-an-internal-load-balancer"></a>내부 부하 분산 장치가 필요한 이유

내부 Load Balancer는 클라우드 서비스 또는 지역 범위의 가상 네트워크 내부에 있는 VM(가상 머신) 간의 부하 분산을 제공합니다. 지역 범위의 가상 네트워크에 대한 자세한 내용은 Azure 블로그의 [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)를 참조하세요. 선호도 그룹에 대해 구성된 기존 가상 네트워크는 ILB를 사용할 수 없습니다.

ILB를 통해 다음과 같은 유형의 부하 분산을 사용할 수 있습니다.

* 클라우드 서비스 내에서: VM에서 동일한 클라우드 서비스 내에 있는 VM 집합에 대한 부하 분산. <a href="#figure1">예제</a>를 참조하세요.
* 가상 네트워크 내에서: 가상 네트워크의 VM에서 가상 네트워크의 동일한 클라우드 서비스 내에 있는 VM 집합으로 부하 분산. <a href="#figure2">예제</a>를 참조하세요.
* 크로스-프레미스 가상 네트워크의 경우: 온-프레미스 컴퓨터에서 가상 네트워크의 동일한 클라우드 서비스 내에 있는 VM 집합으로 부하 분산. <a href="#figure3">예제</a>를 참조하세요.
* 다중 계층 응용 프로그램의 경우: 백 엔드 계층이 인터넷에 연결되어 있지 않은 인터넷 연결 다중 계층 응용 프로그램의 부하 분산. 백 엔드 계층에는 인터넷 연결 계층의 트래픽 부하 분산이 필요합니다.
* LOB(기간 업무) 응용 프로그램의 경우: 추가적인 부하 분산 장치 하드웨어 또는 소프트웨어 없이 Azure에서 호스트되는 LOB(기간 업무) 응용 프로그램의 부하 분산. 이 시나리오는 트래픽 부하가 분산되는 컴퓨터 집합에 있는 온-프레미스 서버를 포함합니다.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>인터넷 연결 다중 계층 응용 프로그램에 대한 부하 분산

웹 계층은 인터넷 클라이언트에 대한 인터넷 연결 끝점을 포함하며 부하 분산 집합의 일부입니다. ILB는 TCP 포트 443(HTTPS)에 대해 웹 클라이언트에서 들어오는 트래픽을 웹 서버로 배포합니다.

데이터베이스 서버는 웹 서버가 저장소에 사용하는 ILB 끝점 뒤에 있습니다. ILB 끝점은 데이터베이스 서비스 부하가 분산된 끝점입니다. ILB 집합의 데이터베이스 서버에 트래픽 부하가 분산됩니다.

다음 이미지는 동일한 클라우드 서비스 내의 인터넷 연결 다중 계층 응용 프로그램에 대한 내부 부하 분산을 설명합니다.

<a name="figure1"></a>
![인터넷 연결 다중 계층 응용 프로그램](./media/load-balancer-internal-overview/IC736321.png)

다른 시나리오는 다중 계층 응용 프로그램에 사용할 수 있습니다. 부하 분산 장치는 ILB용 서비스를 사용하는 클라우드 서비스와 다른 클라우드 서비스에 배포됩니다.

동일한 가상 네트워크를 사용하는 클라우드 서비스는 ILB 끝점에 액세스할 수 있습니다. 다음 이미지는 데이터베이스 백 엔드와 다른 클라우드 서비스에 있는 프런트 엔드 웹 서버를 보여줍니다. 프런트 엔드 서버는 백 엔드와 동일한 가상 네트워크 내에서 ILB 끝점을 사용합니다.

<a name="figure2"></a>
![다른 클라우드 서비스의 프런트 엔드 서버](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>인트라넷 LOB(기간 업무) 응용 프로그램에 대한 부하 분산

온-프레미스 네트워크의 클라이언트 트래픽은 Azure 네트워크에 VPN 연결을 사용하는 LOB(기간 업무) 서버 집합 전체에 부하가 분산됩니다.

클라이언트 컴퓨터는 지점 및 사이트 간 VPN을 사용하여 Azure VPN 서비스에서 IP 주소에 액세스할 수 있습니다. LOB(기간 업무) 응용 프로그램은 ILB 끝점 뒤에 호스팅될 수 있습니다.

<a name="figure3"></a>
![ILB 끝점 뒤에 호스트된 LOB(기간 업무) 응용 프로그램](./media/load-balancer-internal-overview/IC744148.png)

LOB(기간 업무) 응용 프로그램에 대한 다른 시나리오는 ILB 끝점이 구성된 가상 네트워크에 대한 사이트 간 VPN입니다. 온-프레미스 네트워크 트래픽은 ILB 끝점으로 라우팅됩니다.

<a name="figure4"></a>
![ILB 끝점으로 라우팅된 온-프레미스 네트워크 트래픽](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>제한 사항

내부 Load Balancer 구성에서 SNAT가 지원되지 않습니다. 이 문서에서 SNAT는 원본 네트워크 주소 변환을 가장하는 포트와 관련된 시나리오를 나타냅니다. 부하 분산 장치 풀의 VM은 각각의 내부 부하 분산 장치의 프런트 엔드 IP 주소에 도달해야 합니다. 흐름이 처음 시작된 VM으로 부하가 분산되면 연결 오류가 발생합니다. 이러한 시나리오는 ILB에서 지원되지 않습니다. 대신 프록시 스타일 부하 분산 장치를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Load Balancer에 대한 Azure Resource Manager 지원](load-balancer-arm.md)
* [인터넷 연결 부하 분산 장치 구성 시작](load-balancer-get-started-internet-arm-ps.md)
* [내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)
* [부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

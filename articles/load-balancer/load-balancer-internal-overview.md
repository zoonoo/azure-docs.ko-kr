---
title: "내부 부하 분산 장치 개요 | Microsoft Docs"
description: "내부 부하 분산 장치 및 해당 기능에 대한 개요입니다. Azure에 대한 부하 분산 장치의 작동 방식 및 내부 끝점을 구성하는 가능한 시나리오입니다."
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
ms.openlocfilehash: ec07c77119c2da408da21fbdc7877d0b43d16556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="internal-load-balancer-overview"></a>내부 부하 분산 장치 개요

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

인터넷 연결 부하 분산 장치와는 달리, ILB(내부 부하 분산 장치)는 클라우드 서비스 내의 리소스에만 트래픽을 전달하거나 VPN을 사용하여 Azure 인프라에 액세스합니다. 이러한 인프라에서는 가상 네트워크 또는 클라우드 서비스의 부하 분산된 VIP(가상 IP) 주소가 인터넷 끝점에 직접 표시되지 않도록 이러한 주소에 대한 액세스를 제한합니다. 이렇게 하면 내부 기간 업무(LOB) 응용 프로그램을 Azure에서 실행하고 온-프레미스의 리소스나 클라우드 내에서 해당 응용 프로그램에 액세스할 수 있습니다.

## <a name="why-you-may-need-an-internal-load-balancer"></a>내부 부하 분산 장치가 필요한 이유

Azure ILB(내부 부하 분산)는 클라우드 서비스 또는 지역 범위의 가상 네트워크 내부에 있는 가상 컴퓨터 간의 부하 분산을 제공합니다. 지역 범위의 가상 네트워크 사용 및 구성에 대한 자세한 내용은 Azure 블로그의 [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) 를 참조하세요. 선호도 그룹에 대해 구성된 기존 가상 네트워크는 ILB를 사용할 수 없습니다.

ILB를 통해 다음과 같은 유형의 부하 분산을 사용할 수 있습니다.

* 클라우드 서비스 내의 가상 컴퓨터에서 동일한 클라우드 서비스 내에 있는 가상 컴퓨터 집합으로(그림 1 참조)
* 가상 네트워크 내의 가상 컴퓨터에서 가상 네트워크의 동일한 클라우드 서비스 내에 있는 가상 컴퓨터 집합으로(그림 2 참조)
* 크로스-프레미스 가상 네트워크의 경우 온-프레미스 컴퓨터에서 가상 네트워크의 동일한 클라우드 서비스 내에 있는 가상 컴퓨터 집합으로(그림 3 참조)
* 백 엔드 계층이 인터넷에 연결되어 있지 않지만 인터넷 연결 계층에서 전송된 트래픽에 대한 부하 분산을 요구하는 인터넷 연결 다중 계층 응용 프로그램
* 추가적인 부하 분산 장치 하드웨어 또는 소프트웨어 없이도 Azure에서 호스트되는 LOB 응용 프로그램의 부하를 분산합니다. 트래픽 부하가 분산되는 컴퓨터 집합에 온-프레미스 서버 포함

## <a name="internet-facing-multi-tier-applications"></a>인터넷 연결 다중 계층 응용 프로그램

웹 계층은 인터넷 클라이언트에 대한 인터넷 연결 끝점을 포함하며 부하 분산 집합의 일부입니다. 부하 분산 장치는 TCP 포트 443(HTTPS)에 대해 웹 클라이언트에서 들어오는 트래픽을 웹 서버로 배포합니다.

데이터베이스 서버는 웹 서버가 저장소에 사용하는 ILB 끝점 뒤에 있습니다. 이 데이터베이스 서비스 부하 분산 끝점의 트래픽은 ILB 집합의 데이터베이스 서버 간에 부하 분산됩니다.

다음 이미지는 동일한 클라우드 서비스 내의 인터넷 연결 다중 계층 응용 프로그램을 설명합니다.

![내부 부하 분산 단일 클라우드 서비스](./media/load-balancer-internal-overview/IC736321.png)

그림 1 - 인터넷 연결 다중 계층 응용 프로그램

다중 계층 응용 프로그램에 대한 다른 가능한 사용은 ILB에 대한 서비스를 사용하는 클라우드 서비스가 아닌 다른 클라우드 서비스에 ILB가 배포된 경우입니다.

동일한 가상 네트워크를 사용하는 클라우드 서비스는 ILB 끝점에 액세스할 수 있습니다. 다음 이미지에서는 프런트 엔드 웹 서버가 데이터베이스 백 엔드와 다른 클라우드 서비스에 있고 동일한 가상 네트워크 내의 ILB 끝점을 사용하는 것을 보여 줍니다.

![클라우드 서비스 간의 내부 부하 분산](./media/load-balancer-internal-overview/IC744147.png)

그림 2 - 다른 클라우드 서비스의 프런트 엔드 서버

## <a name="intranet-line-of-business-applications"></a>인트라넷 기간 업무 응용 프로그램

온-프레미스 네트워크의 클라이언트에서 전송된 트래픽은 Azure 네트워크에 대한 VPN 연결을 통해 LOB 서버 집합에 부하 분산됩니다.

클라이언트 컴퓨터는 지점과 사이트 간 VPN을 사용하여 Azure VPN 서비스의 IP 주소에 액세스할 수 있습니다. 이 경우 ILB 끝점 뒤에 호스트된 LOB 응용 프로그램을 사용할 수 있습니다.

![지점과 사이트 간 VPN을 사용한 내부 부하 분산](./media/load-balancer-internal-overview/IC744148.png)

그림 3 - LB 끝점 뒤에 호스트된 LOB 응용 프로그램

LOB에 대한 다른 시나리오는 ILB 끝점이 구성된 가상 네트워크에 대해 사이트 간 VPN을 사용하는 것입니다. 이 경우 온-프레미스 네트워크 트래픽을 ILB 끝점으로 라우팅할 수 있습니다.

![사이트 간 VPN을 사용한 내부 부하 분산](./media/load-balancer-internal-overview/IC744150.png)

그림 4 - 온-프레미스 네트워크 트래픽을 ILB 끝점으로 라우팅

## <a name="limitations"></a>제한 사항

내부 부하 분산 장치 구성에서 SNAT가 지원되지 않습니다. 이 문서에 나오는 SNAT는 원본 네트워크 주소 변환을 가장하는 포트를 가리킵니다.  이는 부하 분산 장치 풀의 VM이 각 내부 부하 분산 장치의 프런트 엔드 IP 주소에 도달해야 하는 시나리오에 적용됩니다. 이 시나리오는 내부 부하 분산 장치에는 지원되지 않습니다. 흐름이 처음 시작된 VM으로 부하 분산되면 연결 오류가 발생합니다. 이러한 시나리오에는 프록시 스타일의 부하 분산 장치를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Load Balancer에 대한 Azure Resource Manager 지원](load-balancer-arm.md)

[인터넷 연결 부하 분산 장치 구성 시작](load-balancer-get-started-internet-arm-ps.md)

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

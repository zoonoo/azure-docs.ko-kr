---
title: "Azure Load Balancer 개요 | Microsoft Docs"
description: "Azure Load Balancer 기능, 아키텍처 및 구현에 대한 개요입니다. 부하 분산 장치의 작동 방식과 클라우드에서의 활용에 대해 알아봅니다."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Azure Load Balancer 개요

Azure Load Balancer는 응용 프로그램에 고가용성 및 뛰어난 네트워크 성능을 제공합니다. 이 장치는 부하 분산 장치 집합에 정의된 서비스의 정상 인스턴스 간에 들어오는 트래픽을 분산하는 계층 4(TCP, UDP) 부하 분산 장치입니다.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer는 다음과 같이 구성할 수 있습니다.

* 가상 컴퓨터에 들어오는 인터넷 트래픽을 부하 분산합니다. 이 구성을 [인터넷 연결 부하 분산](load-balancer-internet-overview.md)이라고 합니다.
* 가상 네트워크의 가상 컴퓨터 간, 클라우드 서비스의 가상 컴퓨터 간, 또는 크로스-프레미스 가상 네트워크의 온-프레미스 컴퓨터와 가상 컴퓨터 간에 트래픽을 부하 분산합니다. 이 구성을 [내부 부하 분산](load-balancer-internal-overview.md)이라고 합니다.
* 외부 트래픽을 특정 가상 컴퓨터에 전달합니다.

클라우드의 모든 리소스에는 인터넷에서 연결할 수 있는 공용 IP 주소가 필요합니다. Azure의 클라우드 인프라는 리소스에 대해 라우팅할 수 없는 IP 주소를 사용합니다. Azure는 공용 IP 주소로 NAT(Network Address Translation)를 사용하여 인터넷과 통신합니다.

## <a name="load-balancer-features"></a>부하 분산 장치 기능

* 해시 기반 배포

    Azure Load Balancer는 해시 기반 배포 알고리즘을 사용합니다. 기본적으로 5개 튜플 해시(원본 IP, 원본 포트, 대상 IP, 대상 포트 및 프로토콜 형식으로 구성됨)를 사용하여 트래픽을 사용 가능한 서버에 매핑합니다. 이 알고리즘은 전송 세션 내에서만 지속성이 유지됩니다. 동일한 TCP 또는 UDP 세션의 패킷은 부하 분산된 끝점을 통해 동일한 인스턴스로 전송됩니다. 클라이언트가 연결을 닫았다가 다시 열거나, 동일한 원본 IP에서 새 세션을 시작하는 경우, 원본 포트가 변경되어 트래픽이 다른 데이터 센터의 다른 끝점으로 전송될 수 있습니다.

    자세한 내용은 [부하 분산 장치 배포 모드](load-balancer-distribution-mode.md)를 참조하세요. 다음 그림은 해시 기반 배포를 보여 줍니다.

    ![해시 기반 배포](./media/load-balancer-overview/load-balancer-distribution.png)

    그림 - 해시 기반 배포

* 포트 전달

    Azure Load Balancer는 인바운드 통신을 관리하는 방법을 제어할 수 있습니다. 이 통신은 인터넷 호스트, 다른 클라우드 서비스의 가상 컴퓨터 또는 가상 네트워크에서 시작된 트래픽을 포함합니다. 이 제어는 끝점(입력 끝점이라고도 함)으로 표시됩니다.

    입력 끝점은 공용 포트를 수신 대기하고 트래픽을 내부 포트로 전달합니다. 내부 또는 외부 끝점에 대해 동일한 포트를 매핑하거나 다른 포트를 사용할 수 있습니다. 예를 들어 공용 끝점 매핑은 포트 80이지만 포트 81에서 수신 대기하도록 웹 서버를 구성할 수 있습니다. 공용 끝점을 만들면 부하 분산 장치 인스턴스가 만들어지도록 트리거됩니다.

    Azure Portal을 사용하여 만든 경우, 포털에서 RDP(원격 데스크톱 프로토콜) 및 원격 Windows PowerShell 세션 트래픽에 대해 가상 컴퓨터의 끝점이 자동으로 생성됩니다. 이 끝점을 사용하여 인터넷으로 가상 컴퓨터를 원격 관리할 수 있습니다.

* 자동 재구성

    Azure Load Balancer는 인스턴스를 확장 또는 축소하는 경우 즉시 재구성됩니다. 예를 들어, 클라우드 서비스에서 웹/작업자 역할에 대한 인스턴스 수를 늘리는 경우 또는 동일한 부하 분산된 집합에 가상 컴퓨터를 더 추가하는 경우에 이러한 재구성이 실행됩니다.

* 서비스 모니터링

    Azure Load Balancer는 다양한 서버 인스턴스의 상태를 프로브할 수 있습니다. 프로브가 응답하지 않으면 부하 분산 장치는 비정상 인스턴스에 새 연결의 전송을 중지합니다. 기존 연결은 영향을 받지 않습니다.

    지원되는 세 가지 형식의 프로브는 다음과 같습니다.

    + **게스트 에이전트 프로브(PaaS(Platform as a Service) 가상 컴퓨터에서만)**: 부하 분산 장치는 가상 컴퓨터 내의 게스트 에이전트를 사용합니다. 게스트 에이전트는 인스턴스가 준비 상태에 있는 경우에만(인스턴스가 사용 중, 재순환 중, 중지 중 등의 상태가 아닌 경우에만) 수신 대기하며 HTTP 200 OK 응답을 반환합니다. 에이전트가 HTTP 200 OK로 응답하지 않으면, 부하 분산 장치에서 인스턴스를 응답하지 않는 것으로 표시하고 해당 인스턴스로의 트래픽 전송을 중지합니다. 이때 부하 분산 장치는 계속해서 인스턴스에 ping을 실행합니다. 게스트 에이전트가 HTTP 200 응답을 반환하면, 부하 분산 장치는 해당 인스턴스로 트래픽 전송을 다시 시작합니다. 웹 역할을 사용하는 경우, 웹 사이트 코드는 일반적으로 w3wp.exe(Azure 패브릭 또는 게스트 에이전트에서 모니터링되지 않음)에서 실행됩니다. 즉, w3wp.exe의 오류(예: HTTP 500 응답)는 게스트 에이전트에 보고되지 않으며, 부하 분산 장치는 순환에서 제거해야 할 해당 인스턴스를 인식할 수 없습니다.
    + **HTTP 사용자 지정 프로브:** 이 프로브는 기본(게스트 에이전트) 프로브를 재정의합니다. 사용자는 이 프로브를 사용하여 사용자 지정 논리를 만들고 역할 인스턴스의 상태를 확인할 수 있습니다. 부하 분산 장치는 정기적으로 끝점을 검색합니다(기본적으로 15초 마다). 시간 제한 기간(기본값 31초) 내에 TCP ACK 또는 HTTP 200으로 응답하는 인스턴스는 순환 중인 것으로 간주됩니다. 부하 분산 장치의 순환에서 인스턴스를 제거하는 자체 논리를 구현하려는 경우 이 정보가 유용합니다. 예를 들어, 인스턴스가 90% CPU를 초과하는 경우 200 이외의 상태를 반환하도록 인스턴스를 구성할 수 있습니다. w3wp.exe를 사용하는 웹 역할의 경우도 웹 사이트 코드에 오류가 있으면 프로브에 200 이외의 상태가 반환되므로 웹 사이트의 자동 모니터링이 이루어집니다.
    + **TCP 사용자 지정 프로브:** 이 프로브는 정의된 프로브 포트에 대한 성공적인 TCP 세션 설정에 따라 달라집니다.

    자세한 내용은 [LoadBalancerProbe 스키마](https://msdn.microsoft.com/library/azure/jj151530.aspx)를 참조하세요.

* SNAT(원본 NAT)

    서비스에서 인터넷으로 전송되는 모든 아웃바운드 트래픽은 들어오는 트래픽과 동일한 VIP 주소를 사용하여 SNAT(원본 NAT)를 통과합니다. SNAT에는 다음과 같은 중요한 이점이 있습니다.

    + VIP를 서비스의 다른 인스턴스에 동적으로 매핑할 수 있으므로 서비스의 업그레이드 및 재해 복구가 용이합니다.
    + ACL(액세스 제어 목록)을 보다 쉽게 관리할 수 있습니다. VIP로 표현되는 ACL은 서비스를 확장, 축소하거나 다시 배포해도 변경되지 않습니다.

    부하 분산 장치 구성은 UDP에 대해 완전한 Full Cone NAT를 지원합니다. Full Cone NAT는 포트에서 아웃바운드 요청에 대한 응답으로 모든 외부 호스트에서 오는 인바운드 연결을 허용하는 NAT의 한 유형입니다.

    가상 컴퓨터가 시작하는 각 새로운 아웃바운드 연결의 경우도 아웃바운드 포트는 부하 분산 장치에 의해 할당됩니다. 외부 호스트는 VIP(가상 IP)가 할당된 포트를 통해 트래픽을 확인합니다. 아웃바운드 연결이 많이 필요한 시나리오에서는 VM이 SNAT 전용 아웃바운드 IP 주소를 사용할 수 있도록 [인스턴스 수준 공용 IP](../virtual-network/virtual-networks-instance-level-public-ip.md) 주소를 사용하는 것이 좋습니다. 이렇게 하면 포트 소모 위험이 줄어듭니다.

    이 항목에 대한 자세한 내용은 [아웃바운드 연결](load-balancer-outbound-connections.md) 문서를 참조하세요.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>가상 컴퓨터에 대해 여러 개의 부하 분산된 IP 주소 지원
가상 컴퓨터 집합에 두 개 이상의 부하 분산된 공용 IP 주소를 할당할 수 있습니다. 이 기능을 사용하면 동일한 가상 컴퓨터 집합에서 여러 개의 SSL 웹 사이트 및/또는 여러 개의 SQL Server AlwaysOn 가용성 그룹 수신기를 호스트할 수 있습니다. 자세한 내용은 [클라우드 서비스당 여러 VIP](load-balancer-multivip.md)를 참조하세요.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>제한 사항

Load Balancer 백 엔드 풀에는 기본 계층을 제외한 모든 VM SKU가 포함될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [인터넷 연결 Load Balancer](load-balancer-internet-overview.md)에 대해 알아보기

- [내부 Load Balancer 개요](load-balancer-internal-overview.md)에 대해 알아보기

- [인터넷 연결 Load Balancer](load-balancer-get-started-internet-portal.md) 만들기

- Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보기


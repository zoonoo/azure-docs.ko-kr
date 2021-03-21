---
title: Azure 경로 서버 문제 해결
description: Azure Route Server에 대 한 문제를 해결 하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 83f1e83653c5674988cadcb5b54d3c675ae0b8b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489443"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Azure 경로 서버 문제 해결

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="connectivity-issues"></a>연결 문제

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Azure Route Server에 기본 경로 (0.0.0.0/0)를 알린 후 NVA가 인터넷 연결을 분실 하는 이유는 무엇 인가요?
NVA에서 기본 경로를 보급 하는 경우 Azure 경로 서버는 NVA 자체를 포함 하 여 가상 네트워크의 모든 Vm에 대해 해당 경로를 프로그램 합니다. 이 기본 경로는 NVA를 모든 인터넷 바인딩된 트래픽에 대 한 다음 홉으로 설정 합니다. NVA에 인터넷 연결이 필요한 경우 NVA에서이 기본 경로를 재정의 하 고 UDR을 NVA가 호스트 되는 서브넷에 연결 하도록 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md) 를 구성 해야 합니다 (아래 예제 참조). 그렇지 않으면 nva 호스트 컴퓨터는 NVA에서 보낸 것을 포함 하 여 인터넷에 바인딩된 트래픽을 NVA 자체에 다시 전송 합니다.

| 경로 | 다음 홉 |
|-------|----------|
| 0.0.0.0/0 | 인터넷 |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>내 NVA에서 Azure Route Server의 BGP 피어 IP로 ping 할 수 있는 이유는 무엇 인가요? 둘 사이에서 BGP 피어 링을 설정한 후에는 동일한 IP를 더 이상 ping 할 수 없습니까? BGP 피어 링이 중단 되는 이유는 무엇 인가요?

일부 NVA에서는 Azure 경로 서버 서브넷에 대 한 고정 경로를 추가 해야 합니다. 예를 들어 Azure 경로 서버가 10.0.255.0/27에 있고 NVA가 10.0.1.0/24 이면 NVA의 라우팅 테이블에 다음 경로를 추가 해야 합니다.

| 경로 | 다음 홉 |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1는 NVA (또는 더 정확 하 게 Nic 중 하나가 호스트 됨)가 호스트 되는 서브넷의 기본 게이트웨이 IP입니다.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>이미 Express 경로 게이트웨이 및/또는 Azure VPN gateway가 있는 가상 네트워크에 Azure 경로 서버를 배포할 때 Express 경로 및/또는 Azure VPN을 통해 온-프레미스 네트워크에 연결 되지 않는 이유는 무엇 인가요?
Azure 경로 서버를 가상 네트워크에 배포 하는 경우 게이트웨이와 가상 네트워크 간에 제어 평면을 업데이트 해야 합니다. 이 업데이트를 수행 하는 동안 가상 네트워크의 Vm이 온-프레미스 네트워크에 대 한 연결을 손실 하는 기간이 있습니다. 프로덕션 환경에서 Azure 경로 서버를 배포 하기 위해 유지 관리를 예약 하는 것이 좋습니다.  

## <a name="control-plane-issues"></a>제어 평면 문제

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>NVA와 Azure 경로 서버 간에 BGP 피어 링이 작동 하는 이유 ("플 래핑")는 무엇 인가요?

BGP 타이머 설정 때문에 플 래핑의 원인이 될 수 있습니다. 기본적으로 Azure 경로 서버의 연결 유지 타이머는 60 초로 설정 되 고 대기 타이머는 180 초입니다.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>BGP 피어 링이 작동 중인 경우에도 NVA가 Azure Route 서버에서 경로를 수신 하지 않는 이유는 무엇 인가요?

Azure Route Server에서 사용 하는 ASN은 65515입니다. NVA에 대해 다른 ASN을 구성 하 여 경로 전파가 자동으로 발생할 수 있도록 NVA와 Azure 경로 서버 간에 "eBGP" 세션을 설정할 수 있도록 해야 합니다. NVA와 Azure 경로 서버가 가상 네트워크의 다른 서브넷에 있으므로 BGP 구성에서 "멀티 홉"을 사용 하도록 설정 해야 합니다.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>NVA와 Azure Route Server 간의 BGP 피어 링이 작동 중입니다. 두 항목 간에 올바르게 교환 되는 경로를 볼 수 있습니다. 내 VM의 유효 라우팅 테이블에 NVA 경로가 없는 이유는 무엇 인가요? 

* VM이 NVA 및 Azure Route Server와 동일한 VNet에 있는 경우:

     Azure 경로 서버는 가상 네트워크에서 실행 되는 다른 모든 Vm에 경로를 전송 하는 책임을 공유 하는 두 개의 Vm에서 호스트 되는 두 개의 BGP 피어 Ip를 노출 합니다. 각 NVA는 두 개의 동일한 BGP 세션을 설정 해야 합니다. 예를 들어, 가상 네트워크의 Vm이 Azure 경로 서버 로부터 일관 된 라우팅 정보를 얻을 수 있도록 두 Vm에 동일한 BGP 세션을 설정 하 고 (경로와 동일 하 게 동일한 경로를 사용 하 여 동일한 경로 집합을 사용 합니다.) 아래 다이어그램을 참조 하세요.

    ![경로 서버를 사용 하는 네트워크 가상 어플라이언스를 보여 주는 다이어그램](./media/faq/network-virtual-appliances.png)

    NVA 인스턴스가 두 개 이상 있는 경우 NVA 인스턴스 하나를 활성으로 지정 하 고 다른 수동을 지정 하려는 경우 다른 NVA 인스턴스로부터 동일한 경로에 대 한 경로를 다른 것으로 보급할 *수* 있습니다.

* VM이 NVA 및 Azure Route Server를 호스트 하는 가상 네트워크와 다른 가상 네트워크에 있는 경우 두 Vnet 사이에서 VNet 피어 링이 사용 하도록 설정 되어 있는지 확인 *하 고,* VM의 Vnet에서 원격 경로 서버 사용이 사용 하도록 설정 되어 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 경로 서버를 구성](quickstart-configure-route-server-powershell.md) 하는 방법 알아보기

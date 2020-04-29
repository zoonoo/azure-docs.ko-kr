---
title: Azure Event Hubs에 대 한 네트워크 보안
description: 이 문서에서는 개인 끝점에서 액세스를 구성 하는 방법을 설명 합니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422778"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 네트워크 보안 
이 문서에서는 Azure Event Hubs에서 다음 보안 기능을 사용 하는 방법을 설명 합니다. 

- 서비스 태그
- IP 방화벽 규칙
- 네트워크 서비스 끝점
- 전용 끝점 (미리 보기)


## <a name="service-tags"></a>서비스 태그
서비스 태그는 지정 된 Azure 서비스에서 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그가 있는 주소 접두사를 관리 하 고, 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 하 여 네트워크 보안 규칙에 대 한 빈번한 업데이트의 복잡성을 최소화 합니다. 서비스 태그에 대 한 자세한 내용은 [서비스 태그 개요](../virtual-network/service-tags-overview.md)를 참조 하세요.

서비스 태그를 사용 하 여 [네트워크 보안 그룹](../virtual-network/security-overview.md#security-rules) 또는 [Azure 방화벽](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 합니다. 규칙의 적절 한 *원본* 또는 *대상* 필드에서 서비스 태그 이름 (예: **EventHub**)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다.

| 서비스 태그 | 목적 | 인바운드 또는 아웃 바운드를 사용할 수 있나요? | 지역별 일 수 있나요? | Azure 방화벽과 함께 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | 아웃바운드 | 예 | 예 |


## <a name="ip-firewall"></a>IP 방화벽 
기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한 인터넷에서 Event Hubs 네임 스페이스에 액세스할 수 있습니다. IP 방화벽을 사용 하면 [CIDR (클래스 없는 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법에서 ipv4 주소 또는 ipv4 주소 범위 집합 으로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트 에서만 Azure Event Hubs에 액세스할 수 있어야 하는 경우에 유용 합니다. 방화벽 규칙을 사용 하면 특정 IPv4 주소에서 시작 되는 트래픽을 허용 하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express 경로](/azure/expressroute/expressroute-faqs#supported-services)에 Event Hubs를 사용 하는 경우 온-프레미스 인프라 IP 주소의 트래픽만 허용 하는 **방화벽 규칙** 을 만들 수 있습니다. 

IP 방화벽 규칙은 Event Hubs 네임 스페이스 수준에서 적용 됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Hubs 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [이벤트 허브에 대 한 IP 방화벽을 구성 하는 방법](event-hubs-ip-filtering.md) 을 참조 하세요.

## <a name="network-service-endpoints"></a>네트워크 서비스 끝점
[VNet(Virtual Network) 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)와 Event Hubs를 통합하면 양쪽 엔드에서 네트워크 트래픽 경로를 보호하여 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드의 메시징 기능에 대한 액세스를 보호할 수 있습니다.

하나 이상의 가상 네트워크 서브넷 서비스 끝점에 바인딩하기 위해 구성 된 경우 해당 Event Hubs 네임 스페이스는 더 이상 가상 네트워크에서 권한이 있는 모든 서브넷의 트래픽을 허용 하지 않습니다. 가상 네트워크 큐브 뷰에서 Event Hubs 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다. 

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Event Hubs 네임스페이스 간에 격리된 프라이빗 관계가 생성됩니다. 이 동작에 대 한 예외는 없습니다. 기본적으로 서비스 끝점을 사용 하도록 설정 하면 가상 `denyall` 네트워크와 연결 된 [IP 방화벽](event-hubs-ip-filtering.md) 에서 규칙을 사용할 수 있습니다. IP 방화벽의 특정 IP 주소를 추가 하 여 이벤트 허브 공용 끝점에 대 한 액세스를 사용할 수 있습니다. 

> [!IMPORTANT]
> 가상 네트워크는 Event Hubs의 **표준** 및 **전용** 계층에서 지원되며 **기본** 계층에서는 지원 되지 않습니다.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

긴밀 하 고 compartmentalized 보안을 요구 하 고, 가상 네트워크 서브넷이 compartmentalized 서비스를 구분 하는 데 필요한 솔루션은 해당 구획에 있는 서비스 간에 통신 경로가 여전히 필요 합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 메시지를 파티 간에 전환할 때 디스크에도 기록 되는 절연 된 통신 경로를 제공 합니다. 동일한 Event Hubs 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

### <a name="bind-event-hubs-to-virtual-networks"></a>가상 네트워크에 event hubs 바인딩

**가상 네트워크 규칙**은 Azure Event Hubs 네임스페이스가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Event Hubs를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 가상 네트워크의 서브넷에 **가상 네트워크 서비스 끝점** 을 만들고 [서비스 끝점 개요](../virtual-network/virtual-network-service-endpoints-overview.md) 문서에 설명 된 대로 **Microsoft EventHub** 에 대해 사용 하도록 설정 해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 **가상 네트워크 규칙**을 사용하여 Event Hubs 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Event Hubs 네임스페이스의 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Event Hubs 네임스페이스에 대한 액세스 권한이 부여됩니다. Event Hubs 자체는 아웃 바운드 연결을 설정 하지 않으며, 액세스를 얻을 필요가 없으며, 따라서이 규칙을 사용 하도록 설정 하 여 서브넷에 대 한 액세스 권한을 부여 받지 않습니다.

자세한 내용은 [이벤트 허브에 대 한 가상 네트워크 서비스 끝점을 구성 하는 방법](event-hubs-service-endpoints.md) 을 참조 하세요.

## <a name="private-endpoints"></a>전용 끝점

Azure [개인 링크 서비스](../private-link/private-link-overview.md) 를 사용 하면 가상 네트워크의 **개인 끝점** 을 통해 azure 서비스 (예: azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다.

개인 끝점은 Azure 개인 링크를 통해 제공 되는 서비스에 비공개로 안전 하 게 연결 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

> [!NOTE]
> 이 기능은 **전용** 계층 에서만 지원 됩니다. 전용 계층에 대 한 자세한 내용은 [Event Hubs Dedicated 개요](event-hubs-dedicated-overview.md)를 참조 하세요. 
>
> 이 기능은 현재 **미리 보기**상태입니다. 


자세한 내용은 [이벤트 허브에 대 한 개인 끝점을 구성 하는 방법](private-link-service.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [이벤트 허브에 대 한 IP 방화벽을 구성 하는 방법](event-hubs-ip-filtering.md)
- [이벤트 허브에 대 한 가상 네트워크 서비스 끝점을 구성 하는 방법](event-hubs-service-endpoints.md)
- [이벤트 허브에 대 한 개인 끝점을 구성 하는 방법](private-link-service.md)
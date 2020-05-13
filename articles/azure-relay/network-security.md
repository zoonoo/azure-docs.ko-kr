---
title: Azure Relay에 대 한 네트워크 보안
description: 이 문서에서는 개인 끝점에서 액세스를 구성 하는 방법을 설명 합니다.
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211015"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay에 대 한 네트워크 보안 
이 문서에서는 Azure Relay에서 다음 보안 기능을 사용 하는 방법을 설명 합니다. 

- IP 방화벽 규칙 (미리 보기)
- 전용 끝점 (미리 보기)

> [!NOTE]
> Azure Relay는 네트워크 서비스 끝점을 지원 하지 않습니다. 


## <a name="ip-firewall"></a>IP 방화벽 
기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한, 릴레이 네임 스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용 하면 [CIDR (클래스 없는 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법에서 ipv4 주소 또는 ipv4 주소 범위 집합 으로만 제한할 수 있습니다.

이 기능은 Azure Relay 잘 알려진 특정 사이트 에서만 액세스할 수 있어야 하는 경우에 유용 합니다. 방화벽 규칙을 사용 하면 특정 IPv4 주소에서 시작 되는 트래픽을 허용 하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express 경로](/azure/expressroute/expressroute-faqs#supported-services)와 릴레이를 사용 하는 경우 온-프레미스 인프라 IP 주소의 트래픽만 허용 하는 **방화벽 규칙** 을 만들 수 있습니다. 

IP 방화벽 규칙은 릴레이 네임 스페이스 수준에서 적용 됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. 릴레이 네임 스페이스의 허용 되는 IP 규칙과 일치 하지 않는 IP 주소에서의 모든 연결 시도는 권한 없는 것으로 거부 됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [Relay 네임 스페이스에 대 한 IP 방화벽을 구성 하는 방법](ip-firewall-virtual-networks.md) 을 참조 하세요.

## <a name="private-endpoints"></a>전용 끝점

Azure **개인 링크 서비스** 를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure 서비스 (예: Azure Relay, Azure Service Bus, azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다. 자세한 내용은 [Azure Private Link(미리 보기)란?](../private-link/private-link-overview.md)을 참조하세요.

**개인 끝점** 은 가상 네트워크에서 실행 되는 워크 로드를 **개인 링크 리소스가** 있는 서비스 (예: 릴레이 네임 스페이스)에 전용 및 안전 하 게 연결할 수 있도록 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대 한 모든 트래픽은 개인 끝점을 통해 라우팅할 수 있으므로 게이트웨이, NAT 장치, Express 경로, VPN 연결 또는 공용 IP 주소가 필요 하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 공용 인터넷에서 노출 되는 것을 제거 하는 Microsoft 백본 네트워크를 통해 트래버스 됩니다. 특정 Azure Relay 네임 스페이스에 대 한 연결을 허용 하 여 액세스 제어에서 세분성 수준을 제공할 수 있습니다.

> [!NOTE]
> 이 기능은 현재 **미리 보기**상태입니다. 

자세한 내용은 [개인 끝점을 구성 하는 방법](private-link-service.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [IP 방화벽을 구성 하는 방법](ip-firewall-virtual-networks.md)
- [개인 끝점을 구성 하는 방법](private-link-service.md)
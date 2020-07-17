---
title: Azure Relay에 대한 네트워크 보안
description: 이 문서에서는 프라이빗 엔드포인트에서 액세스를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a1ade21df39890b7f1c31a81fca1fffafe2acaa0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322157"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay에 대한 네트워크 보안 
이 문서에서는 Azure Relay에서 다음 보안 기능을 사용하는 방법을 설명합니다. 

- IP 방화벽 규칙(미리 보기)
- 프라이빗 엔드포인트(미리 보기)

> [!NOTE]
> Azure Relay는 네트워크 서비스 엔드포인트를 지원하지 않습니다. 


## <a name="ip-firewall"></a>IP 방화벽 
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 릴레이 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Relay에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services)와 함께 릴레이를 사용하는 경우 온-프레미스 인프라 IP 주소에서 오는 트래픽만 허용하도록 **방화벽 규칙**을 만들 수 있습니다. 

IP 방화벽 규칙은 릴레이 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. 릴레이 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 모든 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [릴레이 네임스페이스에 대한 IP 방화벽을 구성하는 방법](ip-firewall-virtual-networks.md)을 참조하세요.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

Azure **Private Link Service**를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스(예: Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다. 자세한 내용은 [Azure Private Link(미리 보기)란?](../private-link/private-link-overview.md)을 참조하세요.

**프라이빗 엔드포인트**는 가상 네트워크에서 실행 중인 워크로드가 **프라이빗 링크 리소스**가 있는 서비스(예: 릴레이 네임스페이스)에 비공개로 안전하게 연결할 수 있도록 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute, VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 특정 Azure Relay 네임스페이스에 대한 연결을 허용하여 액세스 제어 수준을 세분화할 수 있습니다.

> [!NOTE]
> 이 기능은 현재 **미리 보기**로 제공됩니다. 

자세한 내용은 [프라이빗 엔드포인트를 구성하는 방법](private-link-service.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [IP 방화벽을 구성하는 방법](ip-firewall-virtual-networks.md)
- [프라이빗 엔드포인트를 구성하는 방법](private-link-service.md)
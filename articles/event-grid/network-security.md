---
title: Azure 이벤트 그리드 리소스에 대한 네트워크 보안
description: 이 문서에서는 개인 끝점에서 액세스를 구성하는 방법을 설명합니다.
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300155"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure 이벤트 그리드 리소스에 대한 네트워크 보안
이 문서에서는 Azure 이벤트 그리드에서 다음 보안 기능을 사용하는 방법에 대해 설명합니다. 

- 송신에 대한 서비스 태그(미리 보기)
- 입문에 대한 IP 방화벽 규칙(미리 보기)
- 시작에 대한 비공개 끝점(미리 보기)


## <a name="service-tags"></a>서비스 태그
서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙에 대한 자주 업데이트하는 복잡성을 최소화합니다. 서비스 태그에 대한 자세한 내용은 [서비스 태그 개요를](../virtual-network/service-tags-overview.md)참조하십시오.

서비스 태그를 사용하여 [네트워크 보안 그룹](../virtual-network/security-overview.md#security-rules) 또는 Azure [방화벽](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 규칙의 적절한 *소스* 또는 *대상* 필드에 서비스 태그 이름(예: **AzureEventGrid)을**지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다.

| 서비스 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있습니까? | 지역일 수 있습니까? | Azure 방화벽에서 사용할 수 있습니까? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEvent그리드 | Azure Event Grid. <br/><br/>*참고:* 이 태그는 미국 중남부, 미국 동부, 미국 동부 2, US 서부 2 및 미국 중부에만 있는 Azure 이벤트 그리드 끝점을 다룹니다. | 모두 | 예 | 예 |


## <a name="ip-firewall"></a>IP 방화벽 
Azure Event Grid는 토픽 및 도메인에 게시하기 위한 IP 기반 액세스 제어를 지원합니다. IP 기반 컨트롤을 사용하면 게시자를 토픽 또는 도메인으로 제한할 수 있습니다. 이 기능은 이벤트 그리드에서 지원하는 [인증 메커니즘을 보완합니다.](security-authentication.md)

기본적으로 요청이 유효한 인증 및 권한 부여와 함께 제공되는 경우 인터넷에서 주제 및 도메인에 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(클래스리스 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법의 IP 주소 또는 IP 주소 범위 집합으로만 제한할 수 있습니다. 다른 IP 주소에서 보낸 게시자는 거부되며 403(금지됨) 응답을 받게 됩니다.


## <a name="private-endpoints"></a>프라이빗 엔드포인트
[비공개 끝점을](../private-link/private-endpoint-overview.md) 사용하여 공용 인터넷을 [거치지](../private-link/private-link-overview.md) 않고도 가상 네트워크에서 주제 및 도메인으로 직접 이벤트를 안전하게 진행할 수 있습니다. 개인 끝점은 VNet의 Azure 서비스에 대한 특수 네트워크 인터페이스입니다. 토픽 또는 도메인에 대한 개인 엔드포인트를 만들면 VNet의 클라이언트와 Event Grid 리소스 간에 안전한 연결을 제공합니다. 개인 끝점은 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다. 개인 끝점과 이벤트 그리드 서비스 간의 연결은 보안 개인 링크를 사용합니다.

![아키텍처 다이어그램](./media/network-security/architecture-diagram.png)

이벤트 그리드 리소스에 개인 끝점을 사용하면 다음을 수행할 수 있습니다.

- 공용 인터넷이 아닌 Microsoft 백본 네트워크를 통해 VNet에서 주제 또는 도메인에 대한 보안 액세스
- 프라이빗 피어링을 사용하여 VPN 또는 ExpressRoutes를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 안전하게 연결합니다.

VNet에서 주제 또는 도메인에 대한 개인 끝점을 만들면 리소스 소유자에게 승인을 위해 동의 요청이 전송됩니다. 개인 끝점 만들기를 요청하는 사용자가 리소스의 소유자이기도 한 경우 이 동의 요청이 자동으로 승인됩니다. 그렇지 않으면 연결이 승인될 때까지 **보류 중입니다.** VNet의 응용 프로그램은 달리 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여 개인 끝점을 통해 Event Grid 서비스에 원활하게 연결할 수 있습니다. 리소스 소유자는 Azure 포털의 리소스에 대한 **개인 끝점** 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

### <a name="connect-to-private-endpoints"></a>프라이빗 엔드포인트에 연결
개인 끝점을 사용하는 VNet의 게시자는 공개 끝점에 연결하는 클라이언트와 동일한 연결 문자열을 토픽 또는 도메인에 사용해야 합니다. DNS 확인은 개인 링크를 통해 VNet에서 토픽 또는 도메인으로의 연결을 자동으로 라우팅합니다. Event Grid는 기본적으로 개인 끝점에 필요한 업데이트와 함께 VNet에 연결된 개인 [DNS 영역을](../dns/private-dns-overview.md) 만듭니다. 그러나 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다.

### <a name="dns-changes-for-private-endpoints"></a>개인 끝점에 대한 DNS 변경
개인 끝점을 만들면 리소스에 대한 DNS CNAME 레코드가 접두사가 `privatelink`있는 하위 도메인의 별칭으로 업데이트됩니다. 기본적으로 개인 링크의 하위 도메인에 해당하는 개인 DNS 영역이 만들어집니다. 

VNet 외부에서 개인 끝점을 사용하여 주제 또는 도메인 끝점 URL을 확인하면 서비스의 공용 끝점으로 확인됩니다. 'topicA'에 대한 DNS 리소스 레코드는 개인 엔드포인트를 **호스팅하는 VNet 외부에서** 해결될 때 다음과 같은 것입니다.

| 이름                                          | Type      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure 트래픽 관리자 프로필\>

[IP 방화벽을](#ip-firewall)사용하여 공용 끝점을 통해 VNet 외부의 클라이언트에 대한 액세스를 거부하거나 제어할 수 있습니다. 

개인 끝점을 호스팅하는 VNet에서 해결되면 토픽 또는 도메인 끝점 URL이 개인 끝점의 IP 주소로 확인됩니다. 'topicA' 주제에 대한 DNS 리소스 레코드는 개인 끝점을 **호스팅하는 VNet 내부에서** 해결될 때 다음과 같은 것입니다.

| 이름                                          | Type      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

이 방법을 사용하면 개인 끝점을 호스팅하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 대해 동일한 연결 문자열을 사용하여 토픽 또는 도메인에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 토픽 또는 도메인 끝점에 대한 FQDN을 개인 엔드포인트 IP 주소로 확인할 수 있습니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임하도록 DNS 서버를 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` 구성하거나 개인 엔드포인트 IP 주소로 A 레코드를 구성합니다.

권장되는 DNS 영역 `privatelink.eventgrid.azure.net`이름은 .

### <a name="private-endpoints-and-publishing"></a>비공개 끝점 및 게시

다음 표는 개인 끝점 연결의 다양한 상태와 게시에 미치는 영향을 설명합니다.

| 연결 상태   |  성공적으로 게시(예/아니요) |
| ------------------ | -------------------------------|
| 승인됨           | yes                            |
| 거부됨           | 예                             |
| Pending            | 예                             |
| 연결 끊김       | 예                             |

게시가 성공하려면 개인 끝점 연결 상태가 **승인되어야**합니다. 연결이 거부되면 Azure 포털을 사용하여 연결을 승인할 수 없습니다. 유일한 가능성은 연결을 삭제하고 대신 새 연결을 만드는 것입니다.

## <a name="pricing-and-quotas"></a>가격 책정 및 할당량
**비공개 엔드포인트는** 프리미엄 계층 토픽 및 도메인에서만 사용할 수 있습니다. Event Grid를 사용하면 토픽 또는 도메인당 최대 64개의 개인 엔드포인트 연결을 만들 수 있습니다. 기본 계층에서 프리미엄 계층으로 업그레이드하려면 [가격 책정 계층 업데이트](update-tier.md) 문서를 참조하세요.

**IP 방화벽** 기능은 이벤트 그리드의 기본 계층과 프리미엄 계층 모두에서 사용할 수 있습니다. 주제 또는 도메인당 최대 16개의 IP 방화벽 규칙을 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계
Event Grid 리소스에 대한 IP 방화벽을 구성하여 일부 IP 주소 또는 IP 주소 범위 집합에서만 공용 인터넷을 통해 액세스를 제한할 수 있습니다. 단계별 지침은 IP 방화벽 [구성을](configure-firewall.md)참조하십시오.

개인 끝점을 구성하여 선택한 가상 네트워크에서만 액세스를 제한할 수 있습니다. 단계별 지침은 [개인 끝점 구성을](configure-private-endpoints.md)참조하십시오.

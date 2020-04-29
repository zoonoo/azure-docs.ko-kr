---
title: Azure Event Grid 리소스에 대 한 네트워크 보안
description: 이 문서에서는 개인 끝점에서 액세스를 구성 하는 방법을 설명 합니다.
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79300155"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure Event Grid 리소스에 대 한 네트워크 보안
이 문서에서는 Azure Event Grid에서 다음 보안 기능을 사용 하는 방법을 설명 합니다. 

- 송신에 대 한 서비스 태그 (미리 보기)
- 수신에 대 한 IP 방화벽 규칙 (미리 보기)
- 수신에 대 한 개인 끝점 (미리 보기)


## <a name="service-tags"></a>서비스 태그
서비스 태그는 지정 된 Azure 서비스에서 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그가 있는 주소 접두사를 관리 하 고, 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 하 여 네트워크 보안 규칙에 대 한 빈번한 업데이트의 복잡성을 최소화 합니다. 서비스 태그에 대 한 자세한 내용은 [서비스 태그 개요](../virtual-network/service-tags-overview.md)를 참조 하세요.

서비스 태그를 사용 하 여 [네트워크 보안 그룹](../virtual-network/security-overview.md#security-rules) 또는 [Azure 방화벽](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 합니다. 규칙의 적절 한 *원본* 또는 *대상* 필드에서 서비스 태그 이름 (예: **azureeventgrid**)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다.

| 서비스 태그 | 목적 | 인바운드 또는 아웃 바운드를 사용할 수 있나요? | 지역별 일 수 있나요? | Azure 방화벽과 함께 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. <br/><br/>*참고:* 이 태그는 미국 남부 중부, 미국 동부, 미국 동부 2, 미국 서 부 2 및 미국 중부 전용의 Azure Event Grid 끝점을 포함 합니다. | 모두 | 아니요 | 아니요 |


## <a name="ip-firewall"></a>IP 방화벽 
Azure Event Grid는 항목과 도메인에 게시 하기 위한 IP 기반 액세스 제어를 지원 합니다. IP 기반 컨트롤을 사용 하면 게시자를 토픽 또는 도메인으로 승인 된 컴퓨터 및 클라우드 서비스 집합 으로만 제한할 수 있습니다. 이 기능은 Event Grid에서 지 원하는 [인증 메커니즘](security-authentication.md) 을 보완 합니다.

기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한, 항목 및 도메인은 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용 하면 [CIDR (클래스 없는 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 ip 주소 또는 ip 주소 범위 집합 으로만 제한할 수 있습니다. 다른 IP 주소에서 시작 된 게시자는 거부 되며 403 (사용할 수 없음) 응답을 받게 됩니다.


## <a name="private-endpoints"></a>전용 끝점
[개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 공용 인터넷을 통하지 않고 [개인 링크](../private-link/private-link-overview.md) 를 통해 안전 하 게 항목 및 도메인에 대 한 가상 네트워크에서 직접 이벤트를 수신 하도록 허용할 수 있습니다. 개인 끝점은 VNet의 Azure 서비스에 대 한 특별 한 네트워크 인터페이스입니다. 토픽 또는 도메인에 대 한 개인 끝점을 만드는 경우 VNet의 클라이언트와 Event Grid 리소스 간에 보안 연결을 제공 합니다. 개인 끝점에는 VNet의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 Event Grid 서비스 간의 연결은 보안 개인 링크를 사용 합니다.

![아키텍처 다이어그램](./media/network-security/architecture-diagram.png)

Event Grid 리소스에 대해 개인 끝점을 사용 하면 다음 작업을 수행할 수 있습니다.

- 공용 인터넷이 아닌 Microsoft 백본 네트워크를 통해 VNet에서 토픽 또는 도메인에 안전 하 게 액세스할 수 있습니다.
- 개인 피어 링을 사용 하 여 VPN 또는 연결할 expressroutes를 사용 하 여 VNet에 연결 하는 온-프레미스 네트워크에서 안전 하 게 연결 합니다.

VNet에서 토픽 또는 도메인에 대 한 개인 끝점을 만드는 경우 승인 요청이 리소스 소유자에 게 전송 됩니다. 개인 끝점의 생성을 요청 하는 사용자가 리소스의 소유자 이기도 한 경우이 동의 요청은 자동으로 승인 됩니다. 그렇지 않으면 승인 될 때까지 연결이 **보류 중** 상태입니다. VNet의 응용 프로그램은 다른 방법으로 사용 하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용 하 여 개인 끝점을 통해 Event Grid 서비스에 원활 하 게 연결할 수 있습니다. 리소스 소유자는 Azure Portal 리소스의 **전용 끝점** 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

### <a name="connect-to-private-endpoints"></a>전용 끝점에 연결
개인 끝점을 사용 하는 VNet의 게시자는 토픽 또는 도메인에 대해 공용 끝점에 연결 하는 클라이언트와 동일한 연결 문자열을 사용 해야 합니다. DNS 확인은 개인 링크를 통해 VNet에서 토픽 또는 도메인으로의 연결을 자동으로 라우팅합니다. Event Grid는 기본적으로 개인 끝점에 대 한 필수 업데이트를 사용 하 여 VNet에 연결 된 [개인 DNS 영역](../dns/private-dns-overview.md) 을 만듭니다. 그러나 사용자 고유의 DNS 서버를 사용 하는 경우 DNS 구성을 추가로 변경 해야 할 수 있습니다.

### <a name="dns-changes-for-private-endpoints"></a>전용 끝점에 대 한 DNS 변경
개인 끝점을 만들 때 리소스에 대 한 DNS CNAME 레코드는 접두사가 `privatelink`있는 하위 도메인의 별칭으로 업데이트 됩니다. 기본적으로 개인 링크의 하위 도메인에 해당 하는 개인 DNS 영역이 생성 됩니다. 

개인 끝점을 사용 하 여 VNet 외부에서 토픽 또는 도메인 끝점 URL을 확인 하면 서비스의 공용 끝점으로 확인 됩니다. ' TopicA '의 DNS 리소스 레코드는 개인 끝점을 호스트 하는 **VNet 외부** 에서 확인 되는 경우 다음과 같습니다.

| 속성                                          | Type      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure traffic manager 프로필\>

[IP 방화벽](#ip-firewall)을 사용 하 여 공용 끝점을 통해 VNet 외부의 클라이언트에 대 한 액세스를 거부 하거나 제어할 수 있습니다. 

개인 끝점을 호스트 하는 VNet에서 확인 되 면 토픽 또는 도메인 끝점 URL은 개인 끝점의 IP 주소로 확인 됩니다. ' TopicA ' 항목에 대 한 DNS 리소스 레코드는 개인 끝점을 호스트 하는 **VNet 내부** 에서 확인 되는 경우 다음과 같습니다.

| 속성                                          | Type      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

이 접근 방식을 사용 하면 개인 끝점을 호스트 하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 동일한 연결 문자열을 사용 하 여 토픽 또는 도메인에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 토픽 또는 도메인 끝점의 FQDN을 확인할 수 있습니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나 개인 끝점 IP 주소를 사용 하 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` 여에 대 한 A 레코드를 구성 합니다.

권장 DNS 영역 이름은 `privatelink.eventgrid.azure.net`입니다.

### <a name="private-endpoints-and-publishing"></a>개인 끝점 및 게시

다음 표에서는 개인 끝점 연결의 다양 한 상태와 게시에 대 한 영향을 설명 합니다.

| 연결 상태   |  게시 함 (예/아니요) |
| ------------------ | -------------------------------|
| 승인됨           | 예                            |
| 거부됨           | 아니요                             |
| Pending            | 아니요                             |
| 연결 끊김       | 아니요                             |

게시에 성공 하려면 개인 끝점 연결 상태를 **승인**해야 합니다. 연결이 거부 되 면 Azure Portal를 사용 하 여 승인할 수 없습니다. 유일한 방법은 연결을 삭제 하 고 새 연결을 만드는 것입니다.

## <a name="pricing-and-quotas"></a>가격 책정 및 할당량
**개인 끝점** 은 프리미엄 계층 토픽 및 도메인 에서만 사용할 수 있습니다. Event Grid는 토픽 또는 도메인 당 최대 64 개의 개인 끝점 연결을 만들 수 있습니다. 기본 계층에서 프리미엄 계층으로 업그레이드 하려면 [가격 책정 계층 업데이트](update-tier.md) 문서를 참조 하세요.

**IP 방화벽** 기능은 Event Grid의 기본 및 프리미엄 계층에서 모두 사용할 수 있습니다. 토픽 또는 도메인 당 최대 16 개의 IP 방화벽 규칙을 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계
Event Grid 리소스에 대 한 IP 방화벽을 구성 하 여 특정 IP 주소 또는 IP 주소 범위의 선택 집합 에서만 공용 인터넷을 통해 액세스를 제한할 수 있습니다. 단계별 지침은 [IP 방화벽 구성](configure-firewall.md)을 참조 하세요.

선택한 가상 네트워크 에서만 액세스를 제한 하도록 개인 끝점을 구성할 수 있습니다. 단계별 지침은 [개인 끝점 구성](configure-private-endpoints.md)을 참조 하세요.

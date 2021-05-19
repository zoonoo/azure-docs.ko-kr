---
title: 네트워킹 개요 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL의 유연한 서버 배포 옵션의 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/22/2021
ms.openlocfilehash: 5b832ca7f1b5fb8a6b0044ca299c75f01a2d0f32
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987052"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>네트워킹 개요 - Azure Database for PostgreSQL - 유연한 서버

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 Azure Database for PostgreSQL - 유연한 서버에 대한 연결 및 네트워킹 개념을 설명합니다. 

## <a name="choosing-a-networking-option"></a>네트워킹 옵션 선택
Azure Database for PostgreSQL - 유연한 서버에 연결하는 두 가지 네트워킹 옵션이 있습니다. 하나는 **프라이빗 액세스(VNet 통합)** 이고 다른 하나는 **퍼블릭 액세스(허용된 IP 주소)** 입니다. 서버를 만들 때 한 가지 옵션을 선택해야 합니다. 

> [!NOTE]
> 서버를 만든 후에는 네트워킹 옵션을 변경할 수 없습니다. 

* **프라이빗 액세스(VNet 통합)** – [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.

   다음과 같은 기능을 원하는 경우 VNet 통합 옵션을 선택합니다.
   * 개인 IP 주소를 사용하여 동일한 가상 네트워크의 Azure 리소스에서 유연한 서버에 연결
   * VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
   * 유연한 서버에 퍼블릭 엔드포인트가 없음

* **퍼블릭 액세스(허용된 IP 주소)** – 퍼블릭 엔드포인트를 통해 유연한 서버에 액세스합니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙** 이라고 합니다. 

   다음 기능을 사용하려면 퍼블릭 액세스 방법을 선택합니다.
   * 가상 네트워크를 지원하지 않는 Azure 리소스에서 연결
   * VPN 또는 ExpressRoute로 연결되지 않은 Azure 외부의 리소스에서 연결 
   * 유연한 서버에 퍼블릭 엔드포인트가 있음

다음 특징은 프라이빗 액세스 또는 퍼블릭 액세스 옵션을 사용하도록 선택하는 경우에 적용됩니다.
* 허용되는 IP 주소에서의 연결은 유효한 자격 증명을 사용하여 PostgreSQL 서버에 인증해야 합니다.
* 네트워크 트래픽에 [연결 암호화](#tls-and-ssl)를 사용할 수 있습니다.
* 서버에 FQDN(정규화된 도메인 이름)이 있습니다. 연결 문자열의 호스트 이름 속성에는 IP 주소 대신 FQDN을 사용하는 것이 좋습니다.
* 두 옵션 모두 데이터베이스 수준 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어합니다. PostgreSQL의 역할 속성을 사용하여 데이터베이스, 테이블 및 기타 개체 액세스를 제어합니다.


## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)
Vnet(가상 네트워크) 통합을 통한 프라이빗 액세스는 PostgreSQL 유연한 서버에 대한 프라이빗 보안 통신을 제공합니다.

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="유연한 서버 Postgres VNET":::

위의 다이어그램에서,
1. 유연한 서버는 VNET **VNet-1** 의 위임된 서브넷 - 10.0.1.0/24에 삽입됩니다.
2. 동일한 vnet 내의 다른 서브넷에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 있습니다.
3. 다른 VNET **VNet-2** 에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 없습니다. 유연한 서버에 액세스하려면 먼저 [프라이빗 DNS 영역 VNET 피어링](#private-dns-zone-and-vnet-peering)을 수행해야 합니다.
   
### <a name="virtual-network-concepts"></a>가상 네트워크 개념
PostgreSQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념은 다음과 같습니다.

* **가상 네트워크** - Azure VNet(Virtual Network)은 사용하도록 구성된 개인 IP 주소 공간을 포함합니다. Azure 가상 네트워킹에 대한 자세한 내용은 [Azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)를 참조하세요.

    가상 네트워크는 유연한 서버와 같은 Azure 지역에 있어야 합니다.

* **위임된 서브넷** - 가상 네트워크에 서브넷(하위 네트워크)이 포함됩니다. 서브넷을 사용하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포됩니다. 

   PostgreSQL 유연한 서버는 PostgreSQL 유연한 서버 전용으로 **위임된** 서브넷에 있어야 합니다. 이렇게 위임하면 Azure Database for PostgreSQL 유연한 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 위임 속성을 Microsoft.DBforPostgreSQL/flexibleServers로 할당하여 서브넷을 위임합니다.

* **NSG(네트워크 보안 그룹)** - 네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md)문서를 참조하세요.

* **프라이빗 DNS 통합** - Azure 프라이빗 DNS 영역 통합을 통해 프라이빗 DNS 영역이 연결된 모든 지역 내 피어링된 VNET 또는 현재 VNET 내의 프라이빗 DNS를 확인할 수 있습니다. 자세한 내용은 [프라이빗 DNS 영역 설명서](https://docs.microsoft.com/azure/dns/private-dns-overview)를 참조하세요.

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 프라이빗 액세스(VNet 통합)를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.

> [!NOTE]
> 사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for PostgreSQL - 유연한 서버의 FQDN을 확인해야 합니다. 자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

### <a name="private-dns-zone-and-vnet-peering"></a>프라이빗 DNS 영역 및 VNET 피어링

프라이빗 DNS 영역 설정과 VNET 피어링은 서로 독립적입니다.

* 기본적으로 새 프라이빗 DNS 영역은 제공된 서버 이름을 사용하여 서버당 자동 프로비저닝됩니다. 그러나 유연한 서버에서 사용할 사용자 고유의 프라이빗 DNS 영역을 설정하려는 경우에는 [프라이빗 DNS 개요](https://docs.microsoft.com/azure/dns/private-dns-overview) 설명서를 참조하세요.
* 다른 VNET에 프로비저닝된 클라이언트에서 유연한 서버에 연결하려는 경우에는 VNET과 프라이빗 DNS 영역을 연결해야 합니다. [가상 네트워크 연결 방법](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 설명서를 참조하세요.


### <a name="unsupported-virtual-network-scenarios"></a>지원되지 않는 가상 네트워크 시나리오
* 퍼블릭 엔드포인트(또는 퍼블릭 IP 또는 DNS) - 가상 네트워크에 배포된 유연한 서버는 퍼블릭 엔드포인트를 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기(주소 공간)를 늘릴 수 없습니다.
* 지역 간 VNet 피어링은 지원되지 않습니다.


## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)
퍼블릭 액세스 메서드의 특성은 다음과 같습니다.
* 허용하는 IP 주소만 PostgreSQL 유연한 서버에 액세스할 권한이 있습니다. 기본적으로 IP 주소는 허용되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* PostgreSQL 서버는 공개적으로 확인할 수 있는 DNS 이름이 있습니다.
* 유연한 서버는 Azure 가상 네트워크 중 하나에 있지 않습니다.
* 서버에서 들어오고 나가는 네트워크 트래픽이 프라이빗 네트워크를 통해 이동하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용합니다.

### <a name="firewall-rules"></a>방화벽 규칙
IP 주소에 대한 권한을 부여하는 것을 방화벽 규칙이라고 합니다. 허용되지 않는 IP 주소에서 연결을 시도하면 원래 클라이언트에 오류가 표시됩니다.

[Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)에서 퍼블릭 액세스(허용된 IP 주소)를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.


### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용
고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 설정하는 것이 좋습니다.

> [!IMPORTANT]
> **Azure 서비스 및 Azure 내의 리소스에서 퍼블릭 액세스 허용** 옵션은 다른 고객 구독의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

### <a name="troubleshooting-public-access-issues"></a>퍼블릭 액세스 문제 해결
PostgreSQL용 Microsoft Azure 데이터베이스 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록에 아직 적용되지 않은 변경 사항:** Azure Database for PostgreSQL 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패:** 사용자에게 Azure Database for PostgreSQL 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 Azure Database for PostgreSQL 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다.

* **동적 클라이언트 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

   * ISP(인터넷 서비스 공급자)는 PostgreSQL용 Azure 데이터베이스 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.
   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

* **IPv6 형식에 방화벽 규칙을 사용할 수 없음:** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하는 경우 유효성 검사 오류가 표시됩니다.

## <a name="hostname"></a>호스트 이름
선택한 네트워킹 옵션에 관계없이 유연한 서버에 연결할 때 항상 FQDN(정규화된 도메인 이름)을 호스트 이름으로 사용하는 것이 좋습니다. 서버의 IP 주소는 고정적으로 유지된다는 보장이 없습니다. FQDN을 사용하면 연결 문자열을 변경하지 않아도 됩니다. 

예제
* `hostname = servername.postgres.database.azure.com` 권장
* 가능하면 `hostname = 10.0.0.4`(프라이빗 주소) 또는 `hostname = 40.2.45.67`(퍼블릭 주소)은 사용하지 않습니다.


## <a name="tls-and-ssl"></a>TLS 및 SSL
Azure Database for PostgreSQL - 유연한 서버는 TLS(전송 계층 보안)를 사용한 PostgreSQL 서비스에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하는 업계 표준 프로토콜입니다. TLS는 SSL(Secure Sockets Layer)의 업데이트된 프로토콜입니다.

Azure Database for PostgreSQL - 유연한 서버는 전송 계층 보안을 사용하여 암호화된 연결만 지원합니다. TLS 1.0 및 TLS 1.1을 사용하여 들어오는 모든 연결은 거부됩니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 **프라이빗 액세스(VNet 통합)** 를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)에서 **퍼블릭 액세스(허용된 IP 주소)** 를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.

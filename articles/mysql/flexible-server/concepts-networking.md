---
title: 네트워킹 개요 - Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL의 유연한 서버 배포 옵션의 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: c83f36216e7488df94c372234d0541a4ee9f99b5
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492225"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 연결 및 네트워킹 개념 - 유연한 서버(미리 보기)

이 문서에서는 서버에 대한 퍼블릭 및 프라이빗 연결 옵션에 대해 설명합니다. Azure에서 서버를 안전하게 만들기 위해 Azure Database for MySQL 유연한 서버에 대한 네트워킹 개념을 상세히 살펴봅니다.

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 미리 보기로 제공됩니다.

## <a name="choosing-a-networking-option"></a>네트워킹 옵션 선택
Azure Database for MySQL 유연한 서버에 연결하는 두 가지 네트워킹 옵션이 있습니다. 하나는 **프라이빗 액세스(VNet 통합)** 이고 다른 하나는 **퍼블릭 액세스(허용된 IP 주소)** 입니다. 서버를 만들 때 한 가지 옵션을 선택해야 합니다. 

> [!NOTE]
> 서버를 만든 후에는 네트워킹 옵션을 변경할 수 없습니다. 

* **프라이빗 액세스(VNet 통합)** – [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.

   다음과 같은 기능을 원하는 경우 VNet 통합 옵션을 선택합니다.
   * 같은 가상 네트워크 또는 [피어 가상 네트워크](../../virtual-network/virtual-network-peering-overview.md)의 Azure 리소스에서 유연한 서버에 연결
   * VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
   * 비 퍼블릭 엔드포인트

* **퍼블릭 액세스(허용된 IP 주소)** – 퍼블릭 엔드포인트를 통해 유연한 서버에 액세스합니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙** 이라고 합니다. 

   다음 기능을 사용하려면 퍼블릭 액세스 방법을 선택합니다.
   * 가상 네트워크를 지원하지 않는 Azure 리소스에서 연결
   * VPN 또는 ExpressRoute로 연결되지 않은 Azure 외부의 리소스에서 연결 
   * 유연한 서버에 퍼블릭 엔드포인트가 있음

다음 특징은 프라이빗 액세스 또는 퍼블릭 액세스 옵션을 사용하도록 선택하는 경우에 적용됩니다.
* 허용되는 IP 주소에서의 연결은 유효한 자격 증명을 사용하여 MySQL 서버에 인증해야 합니다.
* 네트워크 트래픽에 [연결 암호화](#tls-and-ssl)를 사용할 수 있습니다.
* 서버에 FQDN(정규화된 도메인 이름)이 있습니다. 연결 문자열의 호스트 이름 속성에는 IP 주소 대신 FQDN을 사용하는 것이 좋습니다.
* 두 옵션 모두 데이터베이스 수준 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어합니다. MySQL의 역할 속성을 사용하여 데이터베이스, 테이블 및 기타 개체 액세스를 제어합니다.


## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)
Vnet(가상 네트워크) 통합을 통한 프라이빗 액세스는 MySQL 유연한 서버에 대한 프라이빗 보안 통신을 제공합니다.

### <a name="virtual-network-concepts"></a>가상 네트워크 개념
MySQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념은 다음과 같습니다.

* **가상 네트워크** - Azure VNet(Virtual Network)은 사용하도록 구성된 개인 IP 주소 공간을 포함합니다. Azure 가상 네트워킹에 대한 자세한 내용은 [Azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)를 참조하세요.

    가상 네트워크는 유연한 서버와 같은 Azure 지역에 있어야 합니다.

* **위임된 서브넷** - 가상 네트워크에 서브넷(하위 네트워크)이 포함됩니다. 서브넷을 사용하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포됩니다. 

   MySQL 유연한 서버는 MySQL 유연한 서버 전용으로 **위임된** 서브넷에 있어야 합니다. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 위임 속성을 Microsoft.DBforMySQL/flexibleServers로 할당하여 서브넷을 위임합니다.

* **NSG(네트워크 보안 그룹)** 네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 자세한 정보는 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md)를 검토하세요.

* **가상 네트워크 피어링** 가상 네트워크 피어링을 사용하면 Azure에서 두 개 이상의 가상 네트워크를 원활하게 연결할 수 있습니다. 피어링된 가상 네트워크는 연결하기 위해 하나로 표시됩니다. 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 사용합니다. 피어링된 VNet의 클라이언트 애플리케이션과 유연한 서버 간 트래픽은 Microsoft 개인 네트워크를 통해서만 라우팅되며 해당 네트워크에만 격리됩니다.

유연한 서버는 동일한 Azure 지역 내에서 가상 네트워크 피어링을 지원합니다. 지역 간 VNet 피어링은 **지원되지 않습니다**. 자세한 정보는 [가상 네트워크 피어링 개념](../../virtual-network/virtual-network-peering-overview.md)을 검토하세요.

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>동일한 Azure 지역에서 피어링된 VNet으로부터 연결
유연한 서버에 연결하려 하는 클라이언트 애플리케이션이 피어링된 가상 네트워크에 있는 경우, 피어링된 VNet에서 유연한 서버에 대한 DNS 이름을 확인할 수 없기 때문에 유연한 서버의 서버 이름을 사용하여 연결하지 못할 수도 있습니다. 이 경우 두 가지 방법으로 해결할 수 있습니다.
* 개인 IP 주소 사용(개발/테스트 시나리오에 권장) - 이 옵션은 개발 또는 테스트 목적으로 사용할 수 있습니다. nslookup을 사용하여 유연한 서버 이름(정규화된 도메인 이름)에 대해 개인 IP 주소를 역방향으로 조회하고 개인 IP 주소를 사용하여 클라이언트 애플리케이션으로부터 연결할 수 있습니다. 개인 IP 주소는 계획되거나 계획되지 않은 이벤트 중에 변경될 수 있기 때문에 프로덕션 용도로 유연한 서버 연결에 사용하는 것은 권장되지 않습니다.
* 프라이빗 DNS 영역 사용(프로덕션에 권장) - 이 옵션은 프로덕션 용도로 적합합니다. [프라이빗 DNS 영역](../../dns/private-dns-getstarted-portal.md)을 프로비전하고 클라이언트 가상 네트워크에 연결합니다. 프라이빗 DNS 영역에서 개인 IP 주소를 사용하여 유연한 서버에 대한 [A 레코드](../../dns/dns-zones-records.md#record-types)를 추가합니다. 그런 다음, A 레코드를 사용하여 피어링된 가상 네트워크의 클라이언트 애플리케이션에서 유연한 서버에 연결할 수 있습니다.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>ExpressRoute 또는 VPN을 사용하여 온-프레미스에서 Virtual Network의 유연한 서버에 연결
온-프레미스에서 가상 네트워크의 유연한 서버에 액세스해야 하는 워크로드의 경우 [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) 또는 [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/)과, [온-프레미스에 연결된](/azure/architecture/reference-architectures/hybrid-networking/) 가상 네트워크가 필요합니다. 이 설정을 적용한 후, 온-프레미스 가상 네트워크에서 실행 중인 클라이언트 애플리케이션(예: MySQL Workbench)에서 연결하려는 경우 유연한 서버 이름 확인을 위해 DNS 전달자가 필요합니다. 이 DNS 전달자는 서버 수준 전달자를 통해 Azure 제공 DNS 서비스 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)에 대해 DNS 쿼리를 확인해야 합니다.

올바르게 구성하려면 다음 리소스가 필요합니다.

- 온-프레미스 네트워크
- 프라이빗 액세스로 프로비전된 MySQL 유연한 서버(VNet 통합)
- [온-프레미스에 연결된](/azure/architecture/reference-architectures/hybrid-networking/) 가상 네트워크
- Azure에 배포된 DNS 전달자 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) 사용

그런 다음, 유연한 서버 이름(FQDN)을 사용하여 피어링된 가상 네트워크 또는 온-프레미스 네트워크의 클라이언트 애플리케이션에서 유연한 서버에 연결할 수 있습니다.

### <a name="unsupported-virtual-network-scenarios"></a>지원되지 않는 가상 네트워크 시나리오
* 퍼블릭 엔드포인트(또는 공용 IP 또는 DNS) - 가상 네트워크에 배포된 유연한 서버는 퍼블릭 엔드포인트를 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기(주소 공간)를 늘릴 수 없습니다.
* 지역 간 VNet 피어링은 지원되지 않음

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(VNet 통합)를 사용하도록 설정하는 방법 알아보기

> [!NOTE]
> 사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for MySQL - 유연한 서버의 FQDN을 확인해야 합니다. 자세한 정보는 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)
퍼블릭 액세스 메서드의 특성은 다음과 같습니다.
* 허용하는 IP 주소만 MySQL 유연한 서버에 액세스할 권한이 있습니다. 기본적으로 IP 주소는 허용되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* MySQL 서버에는 공개적으로 확인할 수 있는 DNS 이름이 있습니다.
* 유연한 서버는 Azure 가상 네트워크 중 하나에 있지 않습니다.
* 서버에서 들어오고 나가는 네트워크 트래픽은 개인 네트워크를 통해 이동하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용합니다.

### <a name="firewall-rules"></a>방화벽 규칙
IP 주소에 대한 권한을 부여하는 것을 방화벽 규칙이라고 합니다. 허용되지 않는 IP 주소에서 연결을 시도하면 원래 클라이언트에 오류가 표시됩니다.


### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용
고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 설정하는 것이 좋습니다.

> [!IMPORTANT]
> **Azure 서비스 및 Azure 내의 리소스에서 퍼블릭 액세스 허용** 옵션은 다른 고객 구독의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

[Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하고 관리하는 방법을 알아봅니다.


### <a name="troubleshooting-public-access-issues"></a>퍼블릭 액세스 문제 해결
Microsoft Azure Database for MySQL 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록에 대한 변경이 아직 적용되지 않음:** Azure Database for MySQL 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패:** 사용자에게 Azure Database for MySQL 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 Azure Database for MySQL 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다.

* **동적 클라이언트 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

   * ISP(인터넷 서비스 공급자)는 Azure Database for MySQL 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.
   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.
  
* **IPv6 형식에 방화벽 규칙을 사용할 수 없음:** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하는 경우 유효성 검사 오류가 표시됩니다.


## <a name="hostname"></a>호스트 이름
선택한 네트워킹 옵션에 관계없이 유연한 서버에 연결할 때 항상 FQDN(정규화된 도메인 이름)을 호스트 이름으로 사용하는 것이 좋습니다. 서버의 IP 주소는 고정적으로 유지된다는 보장이 없습니다. FQDN을 사용하면 연결 문자열을 변경하지 않아도 됩니다. 

예제
* 권장 `hostname = servername.mysql.database.azure.com`
* 가능하면 `hostname = 10.0.0.4`(프라이빗 주소) 또는 `hostname = 40.2.45.67`(공용 IP)은 사용하지 않습니다.


## <a name="tls-and-ssl"></a>TLS 및 SSL
Azure Database for MySQL 유연한 서버는 TLS(전송 계층 보안) 암호와 함께 SSL(Secure Sockets Layer)을 사용하여 MySQL 서버에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있도록 하는 업계 표준 프로토콜입니다.

Azure Database for MySQL 유연한 서버는 기본적으로 전송 계층 보안(TLS 1.2)을 사용하는 암호화된 연결을 지원하며 TLS 1.0 및 TLS 1.1을 사용하여 들어오는 연결은 기본적으로 모두 거부됩니다. 유연한 서버에서 암호화된 연결 적용 또는 TLS 버전 구성을 구성하거나 변경할 수 있습니다. 

다음은 유연한 서버에 사용할 수 있는 SSL 및 TLS 설정의 다양한 구성입니다.

| 시나리오   | 서버 매개 변수 설정      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL 사용 안 함(암호화된 연결) | require_secure_transport = OFF |레거시 애플리케이션이 MySQL 서버에 대한 암호화된 연결을 지원하지 않는 경우 require_secure_transport=OFF를 설정하여 유연한 서버에 암호화된 연결 적용을 사용하지 않도록 설정할 수 있습니다.|
|TLS 버전 1.2 미만으로 SSL 적용 | require_secure_transport = ON 및 tls_version = TLSV1 또는 TLSV1.1| 레거시 애플리케이션에서 암호화된 연결을 지원하지만 TLS 버전 1.2 미만이 필요한 경우 암호화된 연결을 사용하도록 설정할 수 있지만 애플리케이션에서 지원하는 tls 버전(v1.0 또는 v1.1)과의 연결을 허용하도록 유연한 서버를 구성할 수 있습니다.|
|TLS 버전 = 1.2를 사용하여 SSL 적용(기본 구성)|require_secure_transport = ON 및 tls_version = TLSV1.2| 이는 유연한 서버에 권장되는 기본 구성입니다.|
|TLS 버전 = 1.3으로 SSL 적용(MySQL v8.0 이상에서 지원됨)| require_secure_transport = ON 및 tls_version = TLSV1.3| 이는 새 애플리케이션 개발에 유용하며 권장됩니다.|

> [!Note]
> 유연한 서버에서 SSL 암호화를 변경하는 것은 지원되지 않습니다. tls_version이 TLS 버전 1.2로 설정된 경우 FIPS 암호화 그룹은 기본적으로 적용됩니다. 버전 1.2가 아닌 TLS 버전의 경우 SSL 암호화는 MySQL 커뮤니티 설치와 함께 제공되는 기본 설정으로 설정됩니다.

자세한 정보는 [SSL/TLS를 사용하여 연결](how-to-connect-tls-ssl.md)하는 방법을 검토하세요. 


## <a name="next-steps"></a>다음 단계
* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(vnet 통합)를 사용하도록 설정하는 방법 알아보기
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하는 방법 알아보기
* [TLS 사용](how-to-connect-tls-ssl.md) 방법 알아보기

---
title: 네트워킹 개요-유연한 서버 Azure Database for MySQL
description: Azure Database for MySQL에 대 한 유연한 서버 배포 옵션의 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: ec835073a1fe447490f6965fe41478319a47f503
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106839"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 유연한 서버 (미리 보기)에 대 한 연결 및 네트워킹 개념

이 문서에서는 서버에 대 한 공용 및 개인 연결 옵션을 설명 합니다. Azure에서 서버를 안전 하 게 만들기 위해 유연한 서버 Azure Database for MySQL에 대 한 네트워킹 개념에 대해 자세히 알아봅니다.

> [!IMPORTANT]
> Azure Database for MySQL-유연한 서버는 미리 보기 상태입니다.

## <a name="choosing-a-networking-option"></a>네트워킹 옵션 선택
Azure Database for MySQL 유연한 서버에 대 한 두 가지 네트워킹 옵션이 있습니다. 하나는 **프라이빗 액세스(VNet 통합)** 이고 다른 하나는 **퍼블릭 액세스(허용된 IP 주소)** 입니다. 서버를 만들 때 하나 이상의 옵션을 선택 해야 합니다. 

> [!NOTE]
> 서버를 만든 후에는 네트워킹 옵션을 변경할 수 없습니다. 

* **프라이빗 액세스(VNet 통합)** – [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.

   다음과 같은 기능을 원하는 경우 VNet 통합 옵션을 선택합니다.
   * 동일한 가상 네트워크 또는 [피어 링 가상 네트워크](../../virtual-network/virtual-network-peering-overview.md) 의 Azure 리소스에서 유연한 서버에 연결
   * VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
   * 비 퍼블릭 엔드포인트

* **공용 액세스 (허용 된 IP 주소)** – 유연한 서버는 공용 끝점을 통해 액세스 됩니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙** 이라고 합니다. 

   다음 기능을 사용 하려면 공용 액세스 방법을 선택 합니다.
   * 가상 네트워크를 지원 하지 않는 Azure 리소스에서 연결
   * VPN 또는 Express 경로를 통해 연결 되지 않은 Azure 외부의 리소스에서 연결 
   * 유연한 서버에는 공용 끝점이 있습니다.

다음 특징은 개인 액세스 또는 공용 액세스 옵션을 사용 하도록 선택 하는 경우에 적용 됩니다.
* 허용 되는 IP 주소에서의 연결은 유효한 자격 증명을 사용 하 여 MySQL 서버에 인증 해야 합니다.
* 네트워크 트래픽에 대 한 [연결 암호화](#tls-and-ssl) 를 사용할 수 있습니다.
* 서버에 fqdn (정규화 된 도메인 이름)이 있습니다. 연결 문자열의 hostname 속성에는 IP 주소 대신 fqdn을 사용 하는 것이 좋습니다.
* 두 옵션은 데이터베이스 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어 합니다. MySQL의 역할 속성을 사용 하 여 데이터베이스, 테이블 및 기타 개체 액세스를 제어 합니다.


## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)
Vnet (virtual network) 통합을 통한 개인 액세스는 MySQL 유연한 서버에 대 한 개인 및 보안 통신을 제공 합니다.

### <a name="virtual-network-concepts"></a>가상 네트워크 개념
MySQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념은 다음과 같습니다.

* **Virtual network** -VNet (Azure Virtual Network)은 사용 하도록 구성 된 개인 IP 주소 공간을 포함 합니다. Azure 가상 네트워킹에 대해 자세히 알아보려면 [azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md) 를 참조 하세요.

    가상 네트워크는 유연한 서버와 동일한 Azure 지역에 있어야 합니다.

* **위임 된 서브넷** -가상 네트워크에 서브넷 (하위 네트워크)이 포함 됩니다. 서브넷을 사용 하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포 됩니다. 

   Mysql 유연한 서버는 MySQL 유연한 서버 사용을 위해 **위임** 된 서브넷에 있어야 합니다. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 위임 속성을 flexibleServers으로 할당 하 여 서브넷을 위임 합니다.

* **NSG (네트워크 보안 그룹)** 네트워크 보안 그룹의 보안 규칙을 사용 하면 가상 네트워크 서브넷 및 네트워크 인터페이스에서 들어오고 나가는 네트워크 트래픽 유형을 필터링 할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md) 를 참조 하세요.

* **가상 네트워크 피어 링** 가상 네트워크 피어 링을 사용 하면 Azure에서 두 개 이상의 가상 네트워크를 원활 하 게 연결할 수 있습니다. 피어 링 가상 네트워크는 연결 목적으로 하나로 표시 됩니다. 피어 링 가상 네트워크의 가상 머신 간 트래픽은 Microsoft 백본 인프라를 사용 합니다. 클라이언트 응용 프로그램과 피어 링 Vnet의 유연한 서버 간 트래픽은 Microsoft 개인 네트워크를 통해만 라우팅되고 해당 네트워크로만 격리 됩니다.

유연한 서버는 동일한 Azure 지역 내에서 가상 네트워크 피어 링을 지원 합니다. 지역 간 피어 링 Vnet **지원 되지 않습니다**. 자세한 내용은 [가상 네트워크 피어 링 개념](../../virtual-network/virtual-network-peering-overview.md) 을 참조 하십시오.

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>동일한 Azure 지역의 피어 링 Vnet에서 연결
유연한 서버에 연결 하려는 클라이언트 응용 프로그램이 피어 링 가상 네트워크에 있는 경우 유연한 서버 servername을 사용 하 여 연결 하지 못할 수 있습니다 .이는 피어 링 VNet에서 유연한 서버에 대 한 DNS 이름을 확인할 수 없기 때문입니다. 이 문제를 해결 하는 두 가지 옵션이 있습니다.
* 개인 IP 주소 사용 (개발/테스트 시나리오에 권장)-이 옵션은 개발 또는 테스트 목적으로 사용할 수 있습니다. Nslookup을 사용 하 여 유연한 servername (정규화 된 도메인 이름)에 대 한 개인 IP 주소를 역방향으로 조회 하 고 개인 IP 주소를 사용 하 여 클라이언트 응용 프로그램에서 연결할 수 있습니다. 계획 되거나 계획 되지 않은 이벤트에서 변경 될 수 있으므로 유연한 서버에 대 한 연결에 개인 IP 주소를 사용 하는 것은 프로덕션에서 사용 하지 않는 것이 좋습니다.
* 사설 DNS 영역 사용 (프로덕션에 권장)-이 옵션은 프로덕션 용도로 적합 합니다. [개인 DNS 영역](../../dns/private-dns-getstarted-portal.md) 을 프로 비전 하 고 클라이언트 가상 네트워크에 연결 합니다. 개인 DNS 영역에서 개인 IP 주소를 사용 하 여 유연한 서버에 대 한 [a 레코드](../../dns/dns-zones-records.md#record-types) 를 추가 합니다. 그런 다음 A 레코드를 사용 하 여 피어 링 가상 네트워크의 클라이언트 응용 프로그램에서 유연한 서버에 연결할 수 있습니다.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Express 경로 또는 VPN을 사용 하 여 Virtual Network 온-프레미스에서 유연한 서버에 연결
온-프레미스 네트워크에서 가상 네트워크의 유연한 서버에 액세스 해야 하는 워크 로드의 경우 [express](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) 경로 또는 [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) 과 [온-프레미스에 연결 된](/azure/architecture/reference-architectures/hybrid-networking/)가상 네트워크가 필요 합니다. 이 설정을 적용 한 후에는 온-프레미스 가상 네트워크에서 실행 되는 클라이언트 응용 프로그램 (예: MySQL 워크 벤치)에서 연결 하려는 경우 유연한 서버 이름 확인을 위해 DNS 전달 자가 필요 합니다. 이 DNS 전달자는 Azure에서 제공 하는 DNS 서비스 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)서버 수준 전달자를 통해 모든 dns 쿼리를 확인 합니다.

제대로 구성 하려면 다음 리소스가 필요 합니다.

- 온-프레미스 네트워크
- 개인 액세스로 프로 비전 된 MySQL 유연한 서버 (VNet 통합)
- [온-프레미스에 연결 된](/azure/architecture/reference-architectures/hybrid-networking/) 가상 네트워크
- Azure에 배포 된 DNS 전달자 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) 사용

그런 다음 유연한 servername (FQDN)을 사용 하 여 피어 링 가상 네트워크 또는 온-프레미스 네트워크의 클라이언트 응용 프로그램에서 유연한 서버에 연결할 수 있습니다.

### <a name="unsupported-virtual-network-scenarios"></a>지원 되지 않는 가상 네트워크 시나리오
* 공용 끝점 (또는 공용 IP 또는 DNS)-가상 네트워크에 배포 된 유연한 서버는 공용 끝점을 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포 된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기 (주소 공간)를 늘릴 수 없습니다.
* 지역 간 피어 링 Vnet 지원 되지 않습니다.

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용 하 여 개인 액세스 (vnet 통합)를 사용 하도록 설정 하는 방법에 대해 알아봅니다.

> [!NOTE]
> 사용자 지정 DNS 서버를 사용 하는 경우에는 DNS 전달자를 사용 하 여 Azure Database for MySQL 유연한 서버의 FQDN을 확인 해야 합니다. 자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 을 참조 하세요.

## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)
공용 액세스 방법의 특성은 다음과 같습니다.
* 허용 하는 IP 주소에만 MySQL 유연한 서버에 액세스할 수 있는 권한이 있습니다. 기본적으로 IP 주소는 허용 되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* MySQL 서버에 공개적으로 확인할 DNS 이름이 있습니다.
* 유연한 서버가 Azure virtual network 중 하나가 아닙니다.
* 서버에서 들어오고 나가는 네트워크 트래픽이 개인 네트워크를 통해 이동 하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용 합니다.

### <a name="firewall-rules"></a>방화벽 규칙
IP 주소에 대 한 권한을 부여 하는 것을 방화벽 규칙 이라고 합니다. 허용 되지 않는 IP 주소에서 연결을 시도 하면 원래 클라이언트에 오류가 표시 됩니다.


### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용
Azure 서비스에 대해 고정 된 나가는 IP 주소를 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> Azure **내에서 azure 서비스 및 리소스에서 공용 액세스 허용** 옵션은 다른 고객의 구독에서 연결을 포함 하 여 azure의 모든 연결을 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

[Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용 하 여 공용 액세스 (허용 된 IP 주소)를 사용 하도록 설정 하 고 관리 하는 방법을 알아봅니다.


### <a name="troubleshooting-public-access-issues"></a>공용 액세스 문제 해결
MySQL Server 서비스의 Microsoft Azure Database에 대 한 액세스가 예상과 다르게 작동 하는 경우 다음 사항을 고려 하세요.

* **허용 목록의 변경사항이 아직 적용되지 않았습니다.** MySQL용 Azure 데이터베이스 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패:** 사용자에 게 Azure Database for MySQL 서버에 대 한 권한이 없거나 사용 된 암호가 잘못 된 경우 Azure Database for MySQL 서버에 대 한 연결이 거부 됩니다. 방화벽 설정을 만들면 클라이언트에 서버에 대 한 연결을 시도할 수 있는 기회가 제공 됩니다. 각 클라이언트는 여전히 필요한 보안 자격 증명을 제공 해야 합니다.

* **동적 클라이언트 IP 주소:** 동적 IP 주소 지정을 사용 하 여 인터넷에 연결 되어 있고 방화벽을 통과 하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도해 볼 수 있습니다.

   * ISP (인터넷 서비스 공급자)에 Azure Database for MySQL 서버에 액세스 하는 클라이언트 컴퓨터에 할당 된 IP 주소 범위를 문의 한 다음 IP 주소 범위를 방화벽 규칙으로 추가 합니다.
   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.
  
* **방화벽 규칙을 IPv6 형식에 사용할 수 없습니다.** 방화벽 규칙은 IPv4 형식 이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정 하는 경우 유효성 검사 오류가 표시 됩니다.


## <a name="hostname"></a>호스트 이름
선택한 네트워킹 옵션에 관계 없이 유연한 서버에 연결할 때 항상 FQDN (정규화 된 도메인 이름)을 호스트 이름으로 사용 하는 것이 좋습니다. 서버의 IP 주소는 정적으로 유지 되지 않을 수 있습니다. FQDN을 사용 하면 연결 문자열을 변경 하지 않아도 됩니다. 

예제
* 바람직하지 `hostname = servername.mysql.database.azure.com`
* 가능 하면 `hostname = 10.0.0.4` (개인 주소) 또는 `hostname = 40.2.45.67` (공용 IP)를 사용 하지 마십시오.


## <a name="tls-and-ssl"></a>TLS 및 SSL
Azure Database for MySQL 유연한 서버는 TLS (Transport Layer Security)를 사용 하 여 클라이언트 응용 프로그램을 MySQL 서비스에 연결 하는 것을 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 암호화 된 네트워크 연결을 보장 하는 업계 표준 프로토콜입니다. TLS는 SSL(Secure Sockets Layer) (SSL)의 업데이트 된 프로토콜입니다.

Azure Database for MySQL 유연한 서버는 TLS 1.2 (Transport Layer Security)를 사용 하 여 암호화 된 연결만 지원 합니다. TLS 1.0 및 TLS 1.1을 사용하여 들어오는 모든 연결은 거부됩니다. Azure Database for MySQL 유연한 서버에 연결 하기 위해 TLS 버전을 사용 하지 않도록 설정 하거나 변경할 수 없습니다. 자세한 내용은 [SSL/TLS를 사용 하 여 연결](how-to-connect-tls-ssl.md) 하는 방법을 검토 하세요. 


## <a name="next-steps"></a>다음 단계
* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(vnet 통합)를 사용하도록 설정하는 방법 알아보기
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)를 사용하여 퍼블릭 액세스(허용된 IP 주소)를 사용하도록 설정하는 방법 알아보기
* [TLS를 사용](how-to-connect-tls-ssl.md) 하는 방법 알아보기

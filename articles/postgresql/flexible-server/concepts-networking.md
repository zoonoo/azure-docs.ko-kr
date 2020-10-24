---
title: 네트워킹 개요-Azure Database for PostgreSQL 유연한 서버
description: Azure Database for PostgreSQL에 대 한 유연한 서버 배포 옵션의 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 4280932787cfb2220dab1da84dca41ca0c40e302
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485259"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>네트워킹 개요-Azure Database for PostgreSQL 유연한 서버

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 Azure Database for PostgreSQL 유연한 서버에 대 한 연결 및 네트워킹 개념을 설명 합니다. 

## <a name="choosing-a-networking-option"></a>네트워킹 옵션 선택
Azure Database for PostgreSQL 유연한 서버에 대 한 두 가지 네트워킹 옵션이 있습니다. 하나는 **프라이빗 액세스(VNet 통합)** 이고 다른 하나는 **퍼블릭 액세스(허용된 IP 주소)** 입니다. 서버를 만들 때 하나 이상의 옵션을 선택 해야 합니다. 

> [!NOTE]
> 서버를 만든 후에는 네트워킹 옵션을 변경할 수 없습니다. 

* **프라이빗 액세스(VNet 통합)** – [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.

   다음과 같은 기능을 원하는 경우 VNet 통합 옵션을 선택합니다.
   * 개인 IP 주소를 사용하여 동일한 가상 네트워크의 Azure 리소스에서 유연한 서버에 연결
   * VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
   * 유연한 서버에는 공용 끝점이 없습니다.

* **공용 액세스 (허용 된 IP 주소)** – 유연한 서버는 공용 끝점을 통해 액세스 됩니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙**이라고 합니다. 

   다음 기능을 사용 하려면 공용 액세스 방법을 선택 합니다.
   * 가상 네트워크를 지원 하지 않는 Azure 리소스에서 연결
   * VPN 또는 Express 경로를 통해 연결 되지 않은 Azure 외부의 리소스에서 연결 
   * 유연한 서버에는 공용 끝점이 있습니다.

다음 특징은 개인 액세스 또는 공용 액세스 옵션을 사용 하도록 선택 하는 경우에 적용 됩니다.
* 허용 되는 IP 주소에서의 연결은 유효한 자격 증명을 사용 하 여 PostgreSQL 서버에 인증 해야 합니다.
* 네트워크 트래픽에 대 한 [연결 암호화](#tls-and-ssl) 를 사용할 수 있습니다.
* 서버에 fqdn (정규화 된 도메인 이름)이 있습니다. 연결 문자열의 hostname 속성에는 IP 주소 대신 fqdn을 사용 하는 것이 좋습니다.
* 두 옵션은 데이터베이스 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어 합니다. PostgreSQL의 역할 속성을 사용 하 여 데이터베이스, 테이블 및 기타 개체 액세스를 제어 합니다.


## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)
Vnet (virtual network) 통합을 통한 개인 액세스는 PostgreSQL 유연한 서버에 대 한 개인 및 보안 통신을 제공 합니다.

### <a name="virtual-network-concepts"></a>가상 네트워크 개념
PostgreSQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념은 다음과 같습니다.

* **Virtual network** -VNet (Azure Virtual Network)은 사용 하도록 구성 된 개인 IP 주소 공간을 포함 합니다. Azure 가상 네트워킹에 대해 자세히 알아보려면 [azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md) 를 참조 하세요.

    가상 네트워크는 유연한 서버와 동일한 Azure 지역에 있어야 합니다.


* **위임 된 서브넷** -가상 네트워크에 서브넷 (하위 네트워크)이 포함 됩니다. 서브넷을 사용 하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포 됩니다. 

   PostgreSQL 유연한 서버는 PostgreSQL 유연한 서버 사용을 위해 **위임** 된 서브넷에 있어야 합니다. 이 위임은 Azure Database for PostgreSQL 유연한 서버 에서만 해당 서브넷을 사용할 수 있음을 의미 합니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 위임 속성을 DBforPostgreSQL/flexibleServers로 할당 하 여 서브넷을 위임 합니다.

* **NSG (네트워크 보안 그룹)** 네트워크 보안 그룹의 보안 규칙을 사용 하면 가상 네트워크 서브넷 및 네트워크 인터페이스에서 들어오고 나가는 네트워크 트래픽 유형을 필터링 할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md) 를 참조 하세요.


### <a name="unsupported-virtual-network-scenarios"></a>지원 되지 않는 가상 네트워크 시나리오
* 공용 끝점 (또는 공용 IP 또는 DNS)-가상 네트워크에 배포 된 유연한 서버는 공용 끝점을 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포 된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기 (주소 공간)를 늘릴 수 없습니다.
* 지역 간 피어 링 Vnet 지원 되지 않습니다.

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 개인 액세스 (VNet 통합)를 사용 하 여 유연한 서버를 만드는 방법에 대해 알아봅니다.

> [!NOTE]
> 사용자 지정 DNS 서버를 사용 하는 경우에는 DNS 전달자를 사용 하 여 Azure Database for MySQL 유연한 서버의 FQDN을 확인 해야 합니다. 자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 을 참조 하세요.

## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)
공용 액세스 방법의 특성은 다음과 같습니다.
* 허용 하는 IP 주소에만 PostgreSQL 유연한 서버에 액세스할 수 있는 권한이 있습니다. 기본적으로 IP 주소는 허용 되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* PostgreSQL 서버에 공개적으로 확인할 때 DNS 이름이 있습니다.
* 유연한 서버가 Azure virtual network 중 하나가 아닙니다.
* 서버에서 들어오고 나가는 네트워크 트래픽이 개인 네트워크를 통해 이동 하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용 합니다.

### <a name="firewall-rules"></a>방화벽 규칙
IP 주소에 대 한 권한을 부여 하는 것을 방화벽 규칙 이라고 합니다. 허용 되지 않는 IP 주소에서 연결을 시도 하면 원래 클라이언트에 오류가 표시 됩니다.

[Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)에서 공용 액세스 (허용 되는 IP 주소)를 사용 하 여 유연한 서버를 만드는 방법에 대해 알아봅니다.


### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용
Azure 서비스에 대해 고정 된 나가는 IP 주소를 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> Azure **내에서 azure 서비스 및 리소스에서 공용 액세스 허용** 옵션은 다른 고객의 구독에서 연결을 포함 하 여 azure의 모든 연결을 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

### <a name="troubleshooting-public-access-issues"></a>공용 액세스 문제 해결
PostgreSQL용 Microsoft Azure 데이터베이스 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록의 변경사항이 아직 적용되지 않았습니다.** PostgreSQL용 Azure 데이터베이스 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패:** 사용자에 게 Azure Database for PostgreSQL 서버에 대 한 권한이 없거나 사용 된 암호가 잘못 된 경우 Azure Database for PostgreSQL 서버에 대 한 연결이 거부 됩니다. 방화벽 설정을 만들면 클라이언트에 서버에 대 한 연결을 시도할 수 있는 기회가 제공 됩니다. 각 클라이언트는 여전히 필요한 보안 자격 증명을 제공 해야 합니다.

* **동적 클라이언트 IP 주소:** 동적 IP 주소 지정을 사용 하 여 인터넷에 연결 되어 있고 방화벽을 통과 하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도해 볼 수 있습니다.

   * ISP(인터넷 서비스 공급자)는 PostgreSQL용 Azure 데이터베이스 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.
   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.


## <a name="hostname"></a>Hostname
선택한 네트워킹 옵션에 관계 없이 유연한 서버에 연결할 때 항상 FQDN (정규화 된 도메인 이름)을 호스트 이름으로 사용 하는 것이 좋습니다. 서버의 IP 주소는 정적으로 유지 되지 않을 수 있습니다. FQDN을 사용 하면 연결 문자열을 변경 하지 않아도 됩니다. 

예제
* 바람직하지 `hostname = servername.postgres.database.azure.com`
* 가능 하면 `hostname = 10.0.0.4` (개인 주소) 또는 `hostname = 40.2.45.67` (공용 주소)를 사용 하지 마십시오.


## <a name="tls-and-ssl"></a>TLS 및 SSL
Azure Database for PostgreSQL 유연한 서버는 TLS (Transport Layer Security)를 사용 하 여 PostgreSQL 서비스에 클라이언트 응용 프로그램을 연결 하도록 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 암호화 된 네트워크 연결을 보장 하는 업계 표준 프로토콜입니다. TLS는 SSL (SSL(Secure Sockets Layer))의 업데이트 된 프로토콜입니다.

Azure Database for PostgreSQL 유연한 서버는 전송 계층 보안을 사용 하는 암호화 된 연결만 지원 합니다. TLS 1.0 및 TLS 1.1를 사용 하는 모든 들어오는 연결은 거부 됩니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 **개인 액세스 (VNet 통합)** 를 사용 하 여 유연한 서버를 만드는 방법에 대해 알아봅니다.
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)에서 **공용 액세스 (허용 되는 IP 주소)** 를 사용 하 여 유연한 서버를 만드는 방법에 대해 알아봅니다.

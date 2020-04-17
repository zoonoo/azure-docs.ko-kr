---
title: Azure 가상 네트워크 서비스 엔드포인트
titlesuffix: Azure Virtual Network
description: 서비스 엔드포인트를 사용하여 가상 네트워크에서 Azure 리소스에 대한 직접 액세스를 사용하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: b75e0c1e53f1e00579de73897197cdd2f14d79af
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455413"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network 서비스 엔드포인트

가상 네트워크(VNet) 서비스 엔드포인트는 가상 네트워크 개인 주소 공간을 확장합니다. 끝점은 직접 연결을 통해 VNet의 ID를 Azure 서비스로 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.

이 기능은 다음 Azure 서비스 및 지역에서 사용할 수 있습니다. *Microsoft.\* * 리소스는 괄호 안에 있습니다. 서비스에 대한 서비스 끝점을 구성하는 동안 서브넷 측에서 이 리소스를 활성화합니다.

**일반 공급**

- **[Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** *저장소(Microsoft.Storage):* 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *데이터베이스(Microsoft.Sql):* 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure SQL 데이터 웨어하우스(Microsoft.Sql):](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.*Microsoft.Sql*
- **[PostgreSQL 서버용 Azure](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** 데이터베이스(Microsoft.Sql): 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.*Microsoft.Sql*
- **[MySQL 서버용 Azure](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *데이터베이스(Microsoft.Sql):* 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.
- **[MariaDB(Microsoft.Sql)에 대한 Azure 데이터베이스:](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.*Microsoft.Sql*
- **[Azure 코스모스 DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureCosmosDB):* 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure 키 볼트](../key-vault/general/overview-vnet-service-endpoints.md)** *(Microsoft.KeyVault):* 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure 서비스](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *버스(Microsoft.ServiceBus):* 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure 이벤트](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** 허브(Microsoft.EventHub): 일반적으로 모든 Azure 지역에서 사용할 수 있습니다.*Microsoft.EventHub*
- **[Azure 데이터 레이크 저장소 세대 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureActiveDirectory):* 일반적으로 ADLS Gen1을 사용할 수 있는 모든 Azure 지역에서 사용할 수 있습니다.
- **[Azure 앱 서비스](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)**: 일반적으로 앱 서비스를 사용할 수 있는 모든 Azure 지역에서 사용할 수 있습니다.

**공개 미리 보기**

- **[Azure 컨테이너](../container-registry/container-registry-vnet.md)** 레지스트리(Microsoft.ContainerRegistry): Azure 컨테이너 레지스트리를 사용할 수 있는 모든 Azure 지역에서 미리 보기를 사용할 수 있습니다.*Microsoft.ContainerRegistry*

최신 알림은 [Azure 가상 네트워크 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 확인합니다.

## <a name="key-benefits"></a>주요 이점

서비스 엔드포인트는 다음과 같은 이점을 제공합니다.

- **Azure 서비스 리소스에 대한 향상된 보안:** VNet 개인 주소 공간이 겹칠 수 있습니다. 중복된 공간을 사용하여 VNet에서 발생한 트래픽을 고유하게 식별할 수 없습니다. 서비스 끝점은 VNet ID를 서비스로 확장하여 가상 네트워크에 Azure 서비스 리소스를 보호하는 기능을 제공합니다. 가상 네트워크에서 서비스 끝점을 사용하도록 설정하면 가상 네트워크 규칙을 추가하여 Azure 서비스 리소스를 가상 네트워크에 보호할 수 있습니다. 이 규칙 추가는 리소스에 대한 공용 인터넷 액세스를 완전히 제거하고 가상 네트워크에서만 트래픽을 허용하여 향상된 보안을 제공합니다.
- **가상 네트워크에서 Azure 서비스 트래픽에 대한 최적의 라우팅**: 오늘날 온-프레미스 및/또는 가상 어플라이언스로 인터넷 트래픽을 강제하는 가상 네트워크의 모든 경로는 Azure 서비스 트래픽이 인터넷 트래픽과 동일한 경로를 수행하도록 강제합니다. 서비스 엔드포인트는 Azure 트래픽에 대한 최적의 라우팅을 제공합니다. 

  엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동시킵니다. 트래픽을 Azure 백본 네트워크에 유지하면 서비스 트래픽에 영향을 주지 않고 강제 터널링을 통해 가상 네트워크의 아웃바운드 인터넷 트래픽을 계속 감사하고 모니터링할 수 있습니다. 사용자 정의 경로 및 강제 터널링에 대한 자세한 내용은 [Azure 가상 네트워크 트래픽 라우팅을](virtual-networks-udr-overview.md)참조하십시오.
- **관리 오버 헤드를 덜 사용하여 간단히 설정**: IP 방화벽을 통해 Azure 리소스를 보호하기 위해 가상 네트워크에서 예약된 공용 IP 주소가 더 이상 필요하지 않습니다. 서비스 끝점을 설정하는 데 필요한 NAT(네트워크 주소 번역) 또는 게이트웨이 장치가 없습니다. 서브넷을 간단하게 클릭하여 서비스 끝점을 구성할 수 있습니다. 끝점을 유지 관리하는 데 추가 오버헤드가 없습니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 엔드포인트는 Azure 가상 네트워크에서 구성된 서브넷에서 활성화됩니다. 엔드포인트는 구내에서 Azure 서비스로의 트래픽에 사용할 수 없습니다. 자세한 내용은 [온-프레미스에서 Azure 서비스 보안 액세스를](#secure-azure-services-to-virtual-networks) 참조하세요.
- Azure SQL의 경우 서비스 엔드포인트는 가상 네트워크의 지역 내에서 Azure 서비스 트래픽에만 적용됩니다. Azure Storage의 경우 끝점은 읽기 액세스 지리적 중복 저장소(RA-GRS) 및 GRS(지리적 중복 저장소) 트래픽을 지원하기 위해 가상 네트워크를 배포하는 쌍을 이루는 지역으로 확장됩니다. 자세한 내용은 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)을 참조하세요.
- Azure 데이터 레이크 저장소(ADLS) Gen 1의 경우 VNet 통합 기능은 동일한 지역 내의 가상 네트워크에서만 사용할 수 있습니다. 또한 ADLS Gen1용 가상 네트워크 통합은 가상 네트워크와 Azure Active Directory(Azure AD) 간의 가상 네트워크 서비스 엔드포인트 보안을 사용하여 액세스 토큰에서 추가 보안 클레임을 생성합니다. 그런 다음, 이러한 클레임을 사용하여 Data Lake Storage Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다. 서비스 끝점을 지원하는 서비스 아래에 나열된 *Microsoft.AzureActiveDirectory* 태그는 ADLS Gen 1에 대한 서비스 끝점을 지원하는 데만 사용됩니다. Azure AD는 기본적으로 서비스 끝점을 지원하지 않습니다. Azure Data Lake Store Gen 1 VNet 통합에 대한 자세한 내용은 [Azure Data Lake 저장소 Gen1의 네트워크 보안을](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)참조하십시오.

## <a name="secure-azure-services-to-virtual-networks"></a>가상 네트워크에 대한 보안 Azure 서비스

- 가상 네트워크 서비스 엔드포인트는 Azure 서비스에 가상 네트워크의 ID를 제공합니다. 가상 네트워크에서 서비스 끝점을 사용하도록 설정하면 가상 네트워크 규칙을 추가하여 Azure 서비스 리소스를 가상 네트워크에 보호할 수 있습니다.
- 현재 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. 서비스 엔드포인트에서 서비스 트래픽은 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다. 이 스위치를 사용하면 IP 방화벽에서 사용되는 예약된 공용 IP 주소가 필요 없이 서비스에 액세스할 수 있습니다.

   >[!NOTE]
   > 서비스 엔드포인트를 사용하면 서비스 트래픽에 대한 서브넷의 가상 머신 원본 IP 주소가 공용 IPv4 주소에서 프라이빗 IPv4 주소로 전환됩니다. Azure 공용 IP 주소를 사용하는 기존 Azure 서비스 방화벽 규칙은 더 이상 이 스위치에 작동하지 않습니다. 서비스 엔드포인트를 설정하기 전에 Azure 서비스 방화벽 규칙에서 이 스위치를 허용해야 합니다. 서비스 엔드포인트를 구성하는 동안 이 서브넷의 서비스 트래픽이 일시적으로 중단될 수도 있습니다. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>온-프레미스에서 안전한 Azure 서비스 액세스

  기본적으로 가상 네트워크에 보호되는 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. Azure 서비스 리소스에 대한 IP 방화벽 구성을 통해 이러한 IP 주소를 추가할 수 있습니다.

  ExpressRoute: 공용 피어링또는 구내에서 피어링을 위해 [ExpressRoute를](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 사용하는 경우 사용 중이신 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본에 들어갈 때 기본적으로 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 NAT IP 주소는 서비스 공급자가 제공하거나 제공하는 고객입니다.서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다.공용 피어링 ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). ExpressRoute 공용 및 Microsoft 피어링에 대한 NAT에 대한 자세한 내용은 [ExpressRoute NAT 요구 사항을](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)참조하십시오.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuration

- 가상 네트워크의 서브넷에서 서비스 끝점을 구성합니다. 엔드포인트는 해당 서브넷 내에서 실행되는 모든 컴퓨팅 인스턴스를 사용합니다.
- 서브넷에서 지원되는 모든 Azure 서비스(예: Azure 저장소 또는 Azure SQL Database)에 대해 여러 서비스 끝점을 구성할 수 있습니다.
- Azure SQL Database의 경우 가상 네트워크는 Azure 서비스 리소스와 동일한 지역에 있어야 합니다. GRS 및 RA-GRS Azure Storage 계정을 사용하는 경우 기본 계정은 가상 네트워크와 동일한 지역에 있어야 합니다. 다른 모든 서비스의 경우 모든 리전의 가상 네트워크에 Azure 서비스 리소스를 보호할 수 있습니다. 
- 엔드포인트가 구성된 가상 네트워크는 Azure 서비스 리소스와 동일하거나 다른 구독에 구성될 수 있습니다. 엔드포인트를 설정하고 Azure 서비스를 보호하는 데 필요한 사용 권한에 대한 자세한 내용은 [프로비전](#provisioning)을 참조하세요.
- 지원되는 서비스의 경우 서비스 엔드포인트를 사용하여 가상 네트워크에 대한 기존 또는 새로운 리소스를 보호할 수 있습니다.

### <a name="considerations"></a>고려 사항

- 서비스 끝점을 사용하도록 설정한 후 서브넷 스위치에서 가상 시스템의 소스 IP 주소입니다. 원본 IP 주소는 공용 IPv4 주소를 사용하는 것에서 해당 서브넷에서 서비스와 통신할 때 개인 IPv4 주소를 사용하는 것으로 전환됩니다. 이 전환 중에 서비스에 대한 기존의 모든 오픈 TCP 연결이 닫힙니다. 서브넷의 서비스에 서비스 엔드포인트를 사용하거나 사용하지 않도록 설정하는 경우 중요한 작업이 실행되지 않아야 합니다. 또한 IP 주소를 전환한 후에 애플리케이션이 Azure 서비스에 자동으로 연결될 수 있어야 합니다.

  IP 주소 전환은 가상 네트워크의 서비스 트래픽에만 영향을 줍니다. 가상 시스템에 할당된 공용 IPv4 주소로 처리되는 다른 트래픽에는 영향을 주지 않습니다. Azure 서비스의 경우 Azure 공용 IP 주소를 사용하는 기존 방화벽 규칙이 있는 경우 이러한 규칙은 가상 네트워크 프라이빗 주소로 전환하는 동시에 작동이 중지됩니다.
- 서비스 끝점을 사용하면 Azure 서비스에 대한 DNS 항목은 현재 상태로 유지되며 Azure 서비스에 할당된 공용 IP 주소로 계속 확인됩니다.

- 서비스 엔드포인트의 NSG(네트워크 보안 그룹):
  - 기본적으로 NSG는 아웃바운드 인터넷 트래픽을 허용하고 VNet에서 Azure 서비스로의 트래픽을 허용합니다. 이 트래픽은 서비스 끝점에서 계속 작동합니다. 
  - 모든 아웃바운드 인터넷 트래픽을 거부하고 특정 Azure 서비스에 대한 트래픽만 허용하려면 NSG에서 [서비스 태그를](security-overview.md#service-tags) 사용하여 허용할 수 있습니다. NSG 규칙에서 지원되는 Azure 서비스를 대상으로 지정할 수 있으며 Azure는 각 태그의 기본 IP 주소유지 관리도 제공합니다. 자세한 내용은 [NSG의 Azure 서비스 태그](security-overview.md#service-tags)를 참조하세요. 

### <a name="scenarios"></a>시나리오

- **피어링되거나 연결된 여러 가상 네트워크**: 하나의 가상 네트워크 또는 여러 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷에서 서비스 엔드포인트를 독립적으로 활성화하고 모든 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **가상 네트워크에서 Azure 서비스로 아웃바운드 트래픽을 필터링:** 가상 네트워크에서 Azure 서비스로 전송된 트래픽을 검사하거나 필터링하려는 경우 가상 네트워크 내에 네트워크 가상 어플라이언스를 배포할 수 있습니다. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고 이 서브넷에 대한 Azure 서비스 리소스만을 보호할 수 있습니다. 이 시나리오는 네트워크 가상 어플라이언스 필터링을 사용하여 가상 네트워크에서 Azure 서비스 액세스를 특정 Azure 리소스로만 제한하려는 경우에 유용할 수 있습니다. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.
- **가상 네트워크에 직접 배포되는 서비스에 Azure 리소스 보안:** 가상 네트워크의 특정 서브넷에 다양한 Azure 서비스를 직접 배포할 수 있습니다. 관리되는 서비스 서브넷에서 서비스 엔드포인트를 설정하여 [관리되는 서비스](virtual-network-for-azure-services.md) 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Azure 가상 컴퓨터의 디스크 트래픽**: 관리및 관리되지 않는 디스크에 대한 가상 시스템 디스크 트래픽은 Azure Storage에 대한 서비스 끝점 라우팅 변경 사항의 영향을 받지 않습니다. 이 트래픽에는 diskIO뿐만 아니라 마운트 및 마운트 해제가 포함됩니다. 서비스 끝점 및 [Azure Storage 네트워크 규칙을](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)통해 네트워크를 선택하도록 페이지 Blob에 대한 REST 액세스를 제한할 수 있습니다. 

### <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결

서비스 끝점을 특정 서비스에 구성한 후 다음을 통해 서비스 끝점 경로가 적용되는지 확인합니다. 
 
- 서비스 진단에서 모든 서비스 요청의 원본 IP 주소 유효성을 검사합니다. 서비스 엔드포인트에서 모든 새로운 요청은 요청의 원본 IP 주소를 가상 네트워크 개인 IP 주소로 표시하고 가상 네트워크에서 요청한 클라이언트에 할당됩니다. 엔드포인트가 없는 경우 주소는 Azure 공용 IP 주소입니다.
- 서브넷의 모든 네트워크 인터페이스에서 유효 경로를 볼 수 있습니다. 서비스에 대한 경로:
  - 각 서비스의 주소를 지정하는 구체적인 기본 경로를 표시합니다.
  - *VirtualNetworkServiceEndpoint*의 nextHopType이 있습니다.
  - 강제 터널링 경로에 비해 서비스에 대한 보다 직접적인 연결이 적용되고 있음을 나타냅니다.

>[!NOTE]
> 서비스 엔드포인트 경로는 Azure 서비스의 주소 접두사에 대한 BGP 또는 UDR 경로를 재정의합니다. 자세한 내용은 [효과적인 경로로 문제 해결을](diagnose-network-routing-problem.md)참조하십시오.

## <a name="provisioning"></a>프로비전

서비스 끝점은 가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자가 가상 네트워크에서 독립적으로 구성할 수 있습니다. VNet에 Azure 서비스 리소스를 보호하려면 사용자는 *Microsoft.Network/가상 네트워크/서브넷/joinViaServiceEndpoint/추가된* 서브넷에 대한 작업에 대한 권한이 있어야 합니다. 기본 제공 서비스 관리자 역할에는 기본적으로 이 권한이 포함됩니다. 사용자 지정 역할을 만들어 권한을 수정할 수 있습니다.

기본 제공 역할에 대한 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할을](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)참조하십시오. 사용자 지정 역할에 특정 권한을 할당하는 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할을](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)참조합니다.

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. 가상 네트워크 및 Azure 서비스 리소스가 다른 구독에 있는 경우 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다. 

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 끝점 사용에 대한 추가 요금은 없습니다. Azure 서비스(Azure 저장소, Azure SQL Database 등)에 대한 현재 가격 책정 모델은 현재 와 같이 적용됩니다.

가상 네트워크의 총 서비스 끝점 수에는 제한이 없습니다.

Azure 저장소 계정과 같은 특정 Azure 서비스는 리소스 를 보호하는 데 사용되는 서브넷 수에 제한을 적용할 수 있습니다. 자세한 내용은 [다음 단계](#next-steps) 섹션의 다양한 서비스에 대한 설명서를 참조하십시오.

## <a name="vnet-service-endpoint-policies"></a>VNet 서비스 엔드포인트 정책 

VNet 서비스 엔드포인트 정책을 사용하면 Azure 서비스에 대한 가상 네트워크 트래픽을 필터링할 수 있습니다. 이 필터는 서비스 끝점에 대한 특정 Azure 서비스 리소스만 허용합니다. 서비스 엔드포인트 정책은 Azure 서비스의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트 정책을](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)참조하십시오.

## <a name="faqs"></a>FAQ

FAQ는 가상 [네트워크 서비스 엔드포인트 FAQ를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트 구성](tutorial-restrict-network-access-to-resources.md)
- [Azure 저장소 계정을 가상 네트워크에 보호](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL 데이터베이스를 가상 네트워크에 보호](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL 데이터 웨어하우스를 가상 네트워크에 보호](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [가상 네트워크의 Azure 서비스 통합](virtual-network-for-azure-services.md)
- [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)


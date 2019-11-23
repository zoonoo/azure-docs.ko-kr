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
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378688"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network 서비스 엔드포인트

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**일반 공급**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**공개 미리 보기**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

최신 알림은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 확인하세요.

## <a name="key-benefits"></a>주요 이점

서비스 엔드포인트는 다음과 같은 이점을 제공합니다.

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. 서비스 엔드포인트는 Azure 트래픽에 대한 최적의 라우팅을 제공합니다. 

  엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동시킵니다. 트래픽을 Azure 백본 네트워크에 유지하면 서비스 트래픽에 영향을 주지 않고 강제 터널링을 통해 가상 네트워크의 아웃바운드 인터넷 트래픽을 계속 감사하고 모니터링할 수 있습니다. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **관리 오버 헤드를 덜 사용하여 간단히 설정**: IP 방화벽을 통해 Azure 리소스를 보호하기 위해 가상 네트워크에서 예약된 공용 IP 주소가 더 이상 필요하지 않습니다. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>제한 사항

- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 엔드포인트는 Azure 가상 네트워크에서 구성된 서브넷에서 활성화됩니다. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- Azure SQL의 경우 서비스 엔드포인트는 가상 네트워크의 지역 내에서 Azure 서비스 트래픽에만 적용됩니다. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. 자세한 내용은 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)을 참조하세요.
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. 그런 다음, 이러한 클레임을 사용하여 Data Lake Storage Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- 가상 네트워크 서비스 엔드포인트는 Azure 서비스에 가상 네트워크의 ID를 제공합니다. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- 현재 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. 서비스 엔드포인트에서 서비스 트래픽은 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다. 이 스위치를 사용하면 IP 방화벽에서 사용되는 예약된 공용 IP 주소가 필요 없이 서비스에 액세스할 수 있습니다.

   >[!NOTE]
   > 서비스 엔드포인트를 사용하면 서비스 트래픽에 대한 서브넷의 가상 머신 원본 IP 주소가 공용 IPv4 주소에서 프라이빗 IPv4 주소로 전환됩니다. Azure 공용 IP 주소를 사용하는 기존 Azure 서비스 방화벽 규칙은 더 이상 이 스위치에 작동하지 않습니다. 서비스 엔드포인트를 설정하기 전에 Azure 서비스 방화벽 규칙에서 이 스위치를 허용해야 합니다. 서비스 엔드포인트를 구성하는 동안 이 서브넷의 서비스 트래픽이 일시적으로 중단될 수도 있습니다. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>구성

- Configure service endpoints on a subnet in a virtual network. 엔드포인트는 해당 서브넷 내에서 실행되는 모든 컴퓨팅 인스턴스를 사용합니다.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- Azure SQL Database의 경우 가상 네트워크는 Azure 서비스 리소스와 동일한 지역에 있어야 합니다. GRS 및 RA-GRS Azure Storage 계정을 사용하는 경우 기본 계정은 가상 네트워크와 동일한 지역에 있어야 합니다. For all other services, you can secure Azure service resources to virtual networks in any region. 
- 엔드포인트가 구성된 가상 네트워크는 Azure 서비스 리소스와 동일하거나 다른 구독에 구성될 수 있습니다. 엔드포인트를 설정하고 Azure 서비스를 보호하는 데 필요한 사용 권한에 대한 자세한 내용은 [프로비전](#provisioning)을 참조하세요.
- 지원되는 서비스의 경우 서비스 엔드포인트를 사용하여 가상 네트워크에 대한 기존 또는 새로운 리소스를 보호할 수 있습니다.

### <a name="considerations"></a>고려 사항

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. 이 전환 중에 서비스에 대한 기존의 모든 오픈 TCP 연결이 닫힙니다. 서브넷의 서비스에 서비스 엔드포인트를 사용하거나 사용하지 않도록 설정하는 경우 중요한 작업이 실행되지 않아야 합니다. 또한 IP 주소를 전환한 후에 애플리케이션이 Azure 서비스에 자동으로 연결될 수 있어야 합니다.

  IP 주소 전환은 가상 네트워크의 서비스 트래픽에만 영향을 줍니다. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. Azure 서비스의 경우 Azure 공용 IP 주소를 사용하는 기존 방화벽 규칙이 있는 경우 이러한 규칙은 가상 네트워크 프라이빗 주소로 전환하는 동시에 작동이 중지됩니다.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- 서비스 엔드포인트의 NSG(네트워크 보안 그룹):
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. 자세한 내용은 [NSG의 Azure 서비스 태그](security-overview.md#service-tags)를 참조하세요. 

### <a name="scenarios"></a>시나리오

- **피어링되거나 연결된 여러 가상 네트워크**: 하나의 가상 네트워크 또는 여러 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷에서 서비스 엔드포인트를 독립적으로 활성화하고 모든 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고 이 서브넷에 대한 Azure 서비스 리소스만을 보호할 수 있습니다. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. 관리되는 서비스 서브넷에서 서비스 엔드포인트를 설정하여 [관리되는 서비스](virtual-network-for-azure-services.md) 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- 서비스 진단에서 모든 서비스 요청의 원본 IP 주소 유효성을 검사합니다. 서비스 엔드포인트에서 모든 새로운 요청은 요청의 원본 IP 주소를 가상 네트워크 개인 IP 주소로 표시하고 가상 네트워크에서 요청한 클라이언트에 할당됩니다. 엔드포인트가 없는 경우 주소는 Azure 공용 IP 주소입니다.
- 서브넷의 모든 네트워크 인터페이스에서 유효 경로를 볼 수 있습니다. 서비스에 대한 경로:
  - 각 서비스의 주소를 지정하는 구체적인 기본 경로를 표시합니다.
  - *VirtualNetworkServiceEndpoint*의 nextHopType이 있습니다.
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> 서비스 엔드포인트 경로는 Azure 서비스의 주소 접두사에 대한 BGP 또는 UDR 경로를 재정의합니다. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>프로비저닝

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. 가상 네트워크 및 Azure 서비스 리소스가 다른 구독에 있는 경우 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다. 

## <a name="pricing-and-limits"></a>가격 책정 및 제한

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. 서비스 엔드포인트 정책은 Azure 서비스의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>FAQ

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>다음 단계

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)


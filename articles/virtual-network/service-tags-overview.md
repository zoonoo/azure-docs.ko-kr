---
title: Azure service tags overview
titlesuffix: Azure Virtual Network
description: Learn about service tags. Service tags help minimize complexity of security rule creation.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384151"
---
# <a name="virtual-network-service-tags"></a>Virtual network service tags 
<a name="network-service-tags"></a>

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize complexity of frequent updates on network security rules. You can use service tags to define network access controls on [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. By specifying the service tag name (e.g., **ApiManagement**) in the appropriate *source* or *destination* field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. 

## <a name="available-service-tags"></a>Available service tags
The following table includes all of the service tags available to be used in [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) rules.

The columns indicate whether the tag is:

- Suitable for rules covering inbound or outbound traffic
- Support [regional](https://azure.microsoft.com/regions) scope 
- Usable in [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) rules

By default, service tags reflect the ranges for the entire cloud.  Some service tags also allow more finer-grain control by restricting the corresponding IP ranges to a specified region.  For example while the service tag **Storage** represents Azure Storage for the entire cloud,  **Storage.WestUS** narrows that to only the storage IP address ranges from the WestUS region.  The descriptions of each Service tag below indicates whether they support such regional scope.  



| 태그 | 용도 | Can use Inbound or Outbound? | Can be Regional? | Can use with Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Management traffic for APIM dedicated deployments. | 둘 다 | 아닙니다. | yes |
| **AppService**    | App Service service. This tag is recommended for outbound security rules to WebApp frontends. | 아웃바운드 | yes | yes |
| **AppServiceManagement** | Management traffic for App Service Environment dedicated deployments. | 둘 다 | 아닙니다. | yes |
| **AzureActiveDirectory** | Azure Active Directory service. | 아웃바운드 | 아닙니다. | yes |
| **AzureActiveDirectoryDomainServices** | Management traffic for Azure Active Directory Domain Services dedicated deployments. | 둘 다 | 아닙니다. | yes |
| **AzureBackup** |Azure Backup service.<br/><br/>*Note:* This tag has a dependency on the **Storage** and **AzureActiveDirectory** tags. | 아웃바운드 | 아닙니다. | yes |
| **AzureCloud** | All [datacenter public IP addresses](https://www.microsoft.com/download/details.aspx?id=41653). | 아웃바운드 | yes | yes |
| **AzureConnectors** | Logic Apps connectors for probe/backend connections. | 인바운드 | yes | yes |
| **AzureContainerRegistry** | Azure Container Registry service. | 아웃바운드 | yes | yes |
| **AzureCosmosDB** | Azure Cosmos Database service. | 아웃바운드 | yes | yes |
| **AzureDataLake** | Azure Data Lake service. | 아웃바운드 | 아닙니다. | yes |
| **AzureIoTHub** | Azure IoT Hub service. | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureKeyVault** | Azure KeyVault service.<br/><br/>*Note:* This tag has a dependency on the **AzureActiveDirectory** tag. | 아웃바운드 | yes | yes |
| **AzureLoadBalancer** | Azure's infrastructure load balancer. 태그는 Azure의 상태 프로브가 시작되는 [호스트의 가상 IP 주소](security-overview.md#azure-platform-considerations)(168.63.129.16)로 변환됩니다. Azure Load Balancer를 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다. | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureMachineLearning** | Azure Machine Learning service. | 아웃바운드 | 아닙니다. | yes |
| **AzureMonitor** | Log Analytics, App Insights, AzMon, and custom metrics (GiG endpoints).<br/><br/>*Note:* For Log Analytics, this tag has dependency on the **Storage** tag. | 아웃바운드 | 아닙니다. | yes |
| **AzurePlatformDNS** | The basic infrastructure (default) DNS service.<br/><br>You can use this tag to disable the default DNS. Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzurePlatformIMDS** | IMDS, which is a basic infrastructure service.<br/><br/>You can use this tag to disable the default IMDS.  Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzurePlatformLKM** | Windows licensing or key management service.<br/><br/>You can use this tag to disable the defaults for licensing. Please exercise caution in using this tag.  Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureTrafficManaged** | Azure Traffic Manager probe IP addresses.<br/><br/>Traffic Manager 프로브 IP 주소에 대한 자세한 내용은 [Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)에서 찾을 수 있습니다. | 인바운드 | 아닙니다. | yes |  
| **BatchNodeManagement** | Management traffic for Azure Batch dedicated deployments. | 둘 다 | 아닙니다. | yes |
| **CognitiveServicesManagement** | The address ranges for traffic for Cognitive Services | 아웃바운드 | 아닙니다. | 아닙니다. |
| **Dynamics365ForMarketingEmail** | The address ranges for the marketing email service of Dynamics 365. | 아웃바운드 | yes | 아닙니다. |
| **EventHub** | Azure EventHub service. | 아웃바운드 | yes | yes |
| **GatewayManager** | Management traffic for VPN/App Gateways dedicated deployments. | 인바운드 | 아닙니다. | 아닙니다. |
| **인터넷** | The IP address space that is outside the virtual network and reachable by the public Internet.<br/><br/>주소 범위에는 [Azure에서 소유하는 공용 IP 주소 공간](https://www.microsoft.com/download/details.aspx?id=41653)이 포함됩니다. | 둘 다 | 아닙니다. | 아닙니다. |
| **MicrosoftContainerRegistry** | Microsoft Container Registry service. | 아웃바운드 | yes | yes |
| **Service Bus** | Azure Service Bus service using the Premium service tier. | 아웃바운드 | yes | yes |
| **ServiceFabric** | Service Fabric service. | 아웃바운드 | 아닙니다. | 아닙니다. |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and Azure SQL Data Warehouse services.<br/><br/>*Note:* This tag represents the service, but not specific instances of the service. 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. | 아웃바운드 | yes | yes |
| **SqlManagement** | Management traffic for SQL dedicated deployments. | 둘 다 | 아닙니다. | yes |
| **Storage** | Azure Storage service. <br/><br/>*Note:* The tag represents the service, but not specific instances of the service. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. | 아웃바운드 | yes | yes |
| **VirtualNetwork** | The virtual network address space (all IP address ranges defined for the virtual network), all connected on-premises address spaces, [peered](virtual-network-peering-overview.md) virtual networks, or virtual network connected to a [virtual network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), the [virtual IP address of the host](security-overview.md#azure-platform-considerations) and address prefixes used on [user-defined routes](virtual-networks-udr-overview.md). Be aware that this tag may also contain default routes. | 둘 다 | 아닙니다. | 아닙니다. |

>[!NOTE]
>When working in the *Classic* (pre-Azure Resource Manager) environment, a select set of the above tags are supported.  These use an alternative spelling:

| Classic spelling | Equivalent Resource Manager tag |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| 인터넷 | 인터넷 |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 서비스의 서비스 태그는 사용되는 특정 클라우드의 주소 접두사를 나타냅니다. e.g. the underlying IP ranges corresponding to the **Sql** tag value will differ between the Azure Public cloud and the Azure China cloud.

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 [경로](virtual-networks-udr-overview.md#optional-default-routes)를 추가합니다. 경로의 주소 접두사는 해당하는 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.



## <a name="service-tags-in-on-premises"></a>Service tags in on-premises  
You can obtain the current service tag and range information to include as part of your on-premises firewall configurations.  This information is the current point-in-time list of the IP ranges corresponding to each service tag.  The information can be obtained programmatically or via JSON file download as follows.

### <a name="service-tag-discovery-api-public-preview"></a>Service tag Discovery API (Public Preview)
You can programmatically retrieve the current list of service tags with IP address range details:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> While in Public Preview, the Discovery API may return information that is not as current as the JSON downloads (below).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Discover service tags using downloadable JSON files 
You can download JSON files containing the current list of service tags with IP address range details. These are updated and published weekly.  Locations for each cloud are:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>A subset of this information has previously been published in XML files for [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) and [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). These XML downloads will be deprecated by June 30, 2020 and will no longer be available after that date. Please migrate to using the Discovery API or JSON file downloads as described above.

### <a name="tips"></a>팁 
- You can detect updates from one publishing to the next via increased *changeNumber* values within the JSON file. Each subsection (e.g. **Storage.WestUS**) has its own *changeNumber* that is incremented as changes occur.  The top level of the file's *changeNumber* is incremented when any of the subsections has changed.
- For examples of how to parse the service tag information (e.g. get all address ranges for Storage in WestUS), please refer to the [Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) documentation.

## <a name="next-steps"></a>다음 단계
- [네트워크 보안 그룹 만들기](tutorial-filter-network-traffic.md)에 대해 알아보세요.


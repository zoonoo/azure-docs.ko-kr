---
title: Azure 서비스에 대한 가상 네트워크
titlesuffix: Azure Virtual Network
description: 가상 네트워크에 리소스를 배포하는 이점을 알아봅니다. 가상 네트워크의 리소스는 트래픽이 인터넷을 트래버스하지 않고 서로 간에, 그리고 온-프레미스 리소스와 통신할 수 있습니다.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409501"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 서비스에 대한 가상 네트워크 통합

Azure 서비스를 Azure 가상 네트워크에 통합하면 가상 네트워크의 가상 머신 또는 컴퓨팅 리소스에서 서비스에 대한 프라이빗 액세스가 가능합니다.
다음 옵션을 사용하여 가상 네트워크의 Azure 서비스를 통합할 수 있습니다.
- 서비스의 전용 인스턴스를 가상 네트워크에 배포합니다. 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- 서비스 엔드포인트를 통해 가상 네트워크를 서비스로 확장합니다. 서비스 엔드포인트를 통해 개별 서비스 리소스를 가상 네트워크로 보호할 수 있습니다.

여러 Azure 서비스를 가상 네트워크에 통합하려면 위의 패턴 중 하나 이상을 결합하면 됩니다. 예를 들어 가상 네트워크에 HDInsight를 배포하고 서비스 엔드포인트를 통해 저장소 계정을 HDInsight 서브넷으로 보호할 수 있습니다.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>가상 네트워크에 Azure 서비스 배포

[가상 네트워크](virtual-networks-overview.md)에 전용 Azure 서비스를 배포하는 경우 사설 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 배포된 서비스](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

가상 네트워크 내에 서비스를 배포하면 다음과 같은 기능이 제공됩니다.

- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 [사이트 간 VPN(VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 [피어링](virtual-network-peering-overview.md)하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 일반적으로 가상 네트워크에서 서비스 인스턴스 Azure 서비스에 의해 완전히 관리 됩니다. 여기에 리소스의 상태를 모니터링 및 부하를 사용 하 여 크기 조정 합니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 인바운드 및 서브넷에 대 한 아웃 바운드 네트워크 액세스를 통해 열어야 [네트워크 보안 그룹](security-overview.md#network-security-groups), 서비스에서 제공한 지침에 따라 합니다.
- 특정 서비스는 제한 정책, 경로 또는 결합 Vm 및 동일한 서브넷 내에서 서비스 리소스의 응용 프로그램에서 배포 된 서브넷에 대 한 제한도 적용 합니다. 시간이 지남에 따라 변경 될 수 있기에 특정 제한에 각 서비스를 사용 하 여 확인 합니다. 이러한 서비스의 예로 Azure NetApp 파일 전용 HSM을 Azure Container Instances를 App Service입니다. 
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 [위임된 서브넷](virtual-network-manage-subnet.md#add-a-subnet)을 요구할 수 있습니다. 에 위임 하 여 서비스에는 위임 된 서브넷에서 서비스 관련 리소스를 만드는 명시적 권한을 얻습니다.
- REST API 응답의 예 참조는 [위임 된 서브넷이 있는 가상 네트워크](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet)합니다. 통해 위임 된 서브넷 모델을 사용 하는 서비스의 종합적인 목록을 얻을 수는 [사용 가능한 위임](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>가상 네트워크에 배포할 수 있는 서비스

|Category|서비스| Dedicated¹ 서브넷
|-|-|-|
| 컴퓨팅 | 가상 머신: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[클라우드 서비스](https://msdn.microsoft.com/library/azure/jj156091): Virtual Network(클래식)만 해당<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 아닙니다. <br/> 아니요 <br/> 아닙니다. <br/> No²
| 네트워크 | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[네트워크 가상 어플라이언스](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | 예. <br/> 예 <br/> 예 <br/> 아닙니다.
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 예. <br/> 예. <br/> 
|분석 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| ID | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |아닙니다. <br/>
| 컨테이너 | [AKS(Azure Kubernetes Service)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[ACI(Azure Container Instance)](https://www.aka.ms/acivnet)<br/>Azure Virtual Network CNI [플러그 인](https://github.com/Azure/acs-engine/tree/master/examples/vnet)을 사용하는 [Azure Container Service 엔진](https://github.com/Azure/acs-engine)|No²<br/> 예. <br/><br/> 아닙니다.
| 웹 | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예. <br/> 예 <br/> 예.
| 호스트됨 | [Azure 전용 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예. <br/> 예 <br/>
| | |

서비스 특정 리소스만이 서브넷에 배포할 수 있습니다 하 고 고객 VM/VMSSs 함께 사용할 수 없습니다 '전용' 의미 ¹ <br/> ² 권장 하지만 서비스에 의해 적용 된 필수 요구 되지 않습니다.


## <a name="service-endpoints-for-azure-services"></a>Azure 서비스에 대한 서비스 엔드포인트

일부 Azure 서비스는 가상 네트워크에 배포할 수 없습니다. 필요한 경우 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하여 일부 서비스 리소스에 대한 액세스를 특정 가상 네트워크 서브넷으로만 제한할 수 있습니다.  [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md) 및 엔드포인트를 사용할 수 있는 서비스에 대해 자세히 알아봅니다.

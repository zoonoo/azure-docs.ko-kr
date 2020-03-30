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
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279664"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 서비스에 대한 가상 네트워크 통합

Azure 서비스를 Azure 가상 네트워크에 통합하면 가상 네트워크의 가상 머신 또는 컴퓨팅 리소스에서 서비스에 대한 비공개 액세스가 가능합니다.
다음 옵션을 사용하여 가상 네트워크의 Azure 서비스를 통합할 수 있습니다.
- 서비스의 전용 인스턴스를 가상 네트워크에 배포합니다. 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- [개인 링크를](../private-link/private-link-overview.md) 사용하여 가상 네트워크및 온-프레미스 네트워크에서 서비스의 특정 인스턴스에 개인적으로 액세스합니다.

또한 서비스 끝점을 통해 가상 네트워크를 서비스로 확장하여 공용 끝점을 사용하여 서비스에 액세스할 수도 [있습니다.](virtual-network-service-endpoints-overview.md) 서비스 끝점을 사용하면 서비스 리소스를 가상 네트워크에 보호할 수 있습니다.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>가상 네트워크에 Azure 서비스 배포

[가상 네트워크](virtual-networks-overview.md)에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 배포된 서비스](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

가상 네트워크 내에 서비스를 배포하면 다음과 같은 기능이 제공됩니다.

- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 [사이트 간 VPN(VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 [피어링](virtual-network-peering-overview.md)하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스에서 완전히 관리됩니다. 여기에는 리소스의 상태를 모니터링하고 부하로 확장해야 합니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대한 인바운드 및 아웃바운드 네트워크 액세스는 서비스에서 제공하는 지침에 따라 [네트워크 보안 그룹을](security-overview.md#network-security-groups)통해 열어야 합니다.
- 또한 특정 서비스는 배포되는 서브넷에 제한을 두어 정책, 경로의 적용을 제한하거나 동일한 서브넷 내에서 VM과 서비스 리소스를 결합합니다. 각 서비스에 따라 변경될 수 있는 특정 제한 사항을 확인합니다. 이러한 서비스의 예로는 Azure NetApp 파일, 전용 HSM, Azure 컨테이너 인스턴스, 앱 서비스가 있습니다. 
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 [위임된 서브넷](virtual-network-manage-subnet.md#add-a-subnet)을 요구할 수 있습니다. 위임을 통해 서비스는 위임된 서브넷에서 서비스별 리소스를 만들 수 있는 명시적 권한을 얻습니다.
- [위임된 서브넷이](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)있는 가상 네트워크에서 REST API 응답의 예를 참조하십시오. 위임된 서브넷 모델을 사용하는 서비스의 포괄적인 목록은 사용 가능한 [위임 API를](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) 통해 얻을 수 있습니다.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>가상 네트워크에 배포할 수 있는 서비스

|Category|서비스| 전용¹ 서브넷
|-|-|-|
| 컴퓨팅 | 가상 머신: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[가상 머신 스케일 세트](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[클라우드 서비스](https://msdn.microsoft.com/library/azure/jj156091): 가상 네트워크(클래식)만 해당<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 예 <br/> 예 <br/> 예 <br/> 아니오²
| 네트워크 | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[네트워크 가상 어플라이언스](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | yes <br/> yes <br/> yes <br/> 예
|데이터|[레디스캐시](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL 데이터베이스 관리 인스턴스](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| yes <br/> yes <br/> 
|분석 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 데이터 브릭](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |아니오² <br/> 아니오² <br/> 
| Identity | [Azure Active 디렉터리 도메인 서비스](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |예 <br/>
| 컨테이너 | [Azure Kubernetes 서비스(AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 컨테이너 인스턴스(ACI)](https://www.aka.ms/acivnet)<br/>Azure Virtual Network CNI [플러그 인](https://github.com/Azure/acs-engine/tree/master/examples/vnet)을 사용하는 [Azure Container Service 엔진](https://github.com/Azure/acs-engine)|아니오²<br/> yes <br/><br/> 예
| 웹 | [API 관리](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[앱 서비스 환경](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|yes <br/> yes <br/> yes
| 호스트형 | [Azure 전용 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|yes <br/> yes <br/>
| | |

¹ '전용'은 서비스 특정 리소스만 이 서브넷에 배포할 수 있으며 고객 VM/VMSS와 결합할 수 없음을 의미합니다. <br/> ² 이러한 서비스를 전용 서브넷에 사용하는 것이 좋지만 서비스에 의해 부과되는 필수 요구 사항은 아닙니다.

---
title: Azure(대규모 인스턴스)에서 SAP HANA 네트워크 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 네트워크 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3777180a4d62f8b253ac4cd096bff15613f33565
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206617"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA(대규모 인스턴스) 네트워크 아키텍처

Azure 네트워크 서비스의 아키텍처는 SAP 애플리케이션을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. It's likely that not all IT systems are located in Azure already. Your SAP landscape is often hybrid as well from a DBMS point and SAP application point of view using a mixture of NetWeaver, and S/4HANA and SAP HANA and other DBMS. Azure offers different services that allow you to run the different DBMS, NetWeaver, and S/4HANA systems in Azure. Azure also offers you network technology to make Azure look like a virtual data center to your on-premises software deployments

Unless your complete IT systems are hosted in Azure. Azure networking functionality is used to connect the on-premises world with your Azure assets to make Azure look like a virtual datacenter of yours. The Azure network functionality used is: 

- Azure virtual networks are connected to the [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit that connects to your on-premises network assets.
- An ExpressRoute circuit that connects on-premises to Azure should have a minimum bandwidth of [1 Gbps or higher](https://azure.microsoft.com/pricing/details/expressroute/). 이 최소 대역폭을 통해 온-프레미스 시스템과 VM에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- All SAP systems in Azure are set up in virtual networks to communicate with each other.
- Active Directory and DNS hosted on-premises are extended into Azure through ExpressRoute from on-premises, or are running complete in Azure.

For the specific case of integrating HANA Large Instances into the Azure data center network fabric, Azure ExpressRoute technology is used as well


> [!NOTE] 
> Only one Azure subscription can be linked to only one tenant in a HANA Large Instance stamp in a specific Azure region. Conversely, a single HANA Large Instance stamp tenant can be linked to only one Azure subscription. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

If SAP HANA on Azure (Large Instances) is deployed in multiple different Azure regions, a separate tenant is deployed in the HANA Large Instance stamp. 이러한 인스턴스가 동일한 SAP 자산에 포함되면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> Only the Azure Resource Manager deployment method is supported with SAP HANA on Azure (Large Instances).

 

## <a name="additional-virtual-network-information"></a>추가 가상 네트워크 정보

To connect a virtual network to ExpressRoute, an Azure ExpressRoute gateway must be created. For more information, see [About Expressroute gateways for ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

An Azure ExpressRoute gateway is used with ExpressRoute to an infrastructure outside of Azure or to an Azure Large Instance stamp. You can connect the Azure ExpressRoute gateway to a maximum of four different ExpressRoute circuits as long as those connections come from different Microsoft enterprise edge routers. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> The maximum throughput you can achieve with a ExpressRoute gateway is 10 Gbps by using an ExpressRoute connection. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. To leverage the complete bandwidth of the ExpressRoute gateway, use multiple streams. 또는 단일 파일의 병렬 스트림에서 다른 파일을 복사해야 합니다.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. This Expressroute circuit is fully paid by you as a customer. The bandwidth should be large enough to handle the network traffic between your on-premises assets and the Azure region you are connecting against. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- Azure network services, as previously discussed, with virtual networks, which again need ExpressRoute gateways added. 이 부분은 애플리케이션 요구 사항, 보안 및 규정 준수 요구 사항에 적합한 디자인을 찾아야 하는 영역입니다. HANA 대규모 인스턴스를 사용하는지 여부는 선택할 가상 네트워크와 Azure 게이트웨이 SKU의 수 측면에서 고려해야 할 또 다른 요소입니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute 기술을 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. There is no additional fee for you as a customer for the connectivity between the Azure data center network fabric and HANA Large Instance units.
- Networking within the HANA Large Instance stamp, which is mostly transparent for you.

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image1-architecture.png)

HANA 대규모 인스턴스를 사용하기 때문에 온-프레미스 자산에 있는 사용자가 ExpressRoute를 통해 Azure에 연결해야 한다는 요구 사항은 변경되지 않습니다. HANA 대규모 인스턴스 장치에서 호스팅되는 HANA 인스턴스에 연결하는 애플리케이션 계층을 호스팅하는 VM을 실행하는 하나 이상의 가상 네트워크가 있어야 한다는 요구 사항도 변경되지 않습니다. 

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 고객 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로 통해 가상 네트워크에 연결됩니다. To separate load conditions, the on-premises to Azure virtual network ExpressRoute circuits and the circuits between Azure virtual networks and HANA Large Instances don't share the same routers.
- SAP 애플리케이션 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 SAP HANA에서 애플리케이션 계층으로의 데이터 전송(결과 집합)과 같이 많은 작은 요청과 버스트와는 다른 특성을 가지고 있습니다.
- SAP 애플리케이션 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- The Azure ExpressRoute gateway has at least two ExpressRoute connections. One circuit that is connected from on-premises and one that is connected from HANA Large Instances. This leaves only room for another two additional circuits from different MSEEs to connect to on ExpressRoute Gateway. This restriction is independent of the usage of ExpressRoute Fast Path. All the connected circuits share the maximum bandwidth for incoming data of the ExpressRoute gateway.

With Revision 3 of HANA Large Instance stamps, the network latency experienced between VMs and HANA Large Instance units can be higher than a typical VM-to-VM network round-trip latency. Azure 지역에 따라 측정값은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)에서 평균 미만으로 분류된 0.7ms 왕복 대기 시간을 초과할 수 있습니다. Azure VM 및 HANA 대규모 인스턴스 단위 간의 네트워크 왕복 대기 시간을 측정하는 Azure 지역 및 도구에 따라 측정된 대기 시간은 최대 약 2밀리초일 수 있습니다. 그럼에도 불구하고 고객은 SAP HANA 기반 프로덕션 SAP 애플리케이션을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다. A new functionality, called ExpressRoute Fast Path, is able to reduce the network latency between HANA Large Instances and application layer VMs in Azure substantially (see below). 

With Revision 4 of HANA Large Instance stamps, the network latency between Azure VMs that are deployed in proximity to the HANA Large Instance stamp, is experienced to meet the average or better than average classification as documented in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) if Azure ExpressRoute Fast Path is configured (see below). In order to deploy Azure VMs in close proximity to HANA Large Instance units of Revision 4, you need to leverage [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). The way how proximity placement groups can be used to locate the SAP application layer in the same Azure datacenter as Revision 4 hosted HANA Large Instance units is described in [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).

To provide deterministic network latency between VMs and HANA Large Instance, the choice of the ExpressRoute gateway SKU is essential. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 전송하도록 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. For the Type II class of HANA Large Instance SKUs, the use of the UltraPerformance gateway SKU as a ExpressRotue gateway is mandatory.

> [!IMPORTANT] 
> SAP 애플리케이션과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. For HANA Large Instance Type II SKUs, only the UltraPerformance gateway SKU is supported as a ExpressRoute gateway. Exceptions apply when using ExpressRoute Fast Path (see below)

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
To lower the latency, ExpressRoute Fast Path got introduced and released in May 2019 for the specific connectivity of HANA Large Instances to Azure virtual networks that host the SAP application VMs. The major difference to the solution rolled out so far, is, that the data flows between VMs and HANA Large Instances are not routed through the ExpressRoute gateway anymore. Instead the VMs assigned in the subnet(s) of the Azure virtual network are directly communicating with the dedicated enterprise edge router. 

> [!IMPORTANT] 
> The ExpressRoute Fast Path functionality requires that the subnets running the SAP application VMs are in the same Azure virtual network that got connected to the HANA Large Instances. VMs located in Azure virtual networks that are peered with the Azure virtual network connected directly to the HANA Large Instance units are not benefiting from ExpressRoute Fast Path. As a result typical hub and spoke virtual network designs, where the ExpressRoute circuits are connecting against a hub virtual network and virtual networks containing the SAP application layer (spokes) are getting peered, the optimization by ExpressRoute Fast Path will not work. In addtion, ExpressRoute Fast Path does not support user defined routing rules (UDR) today. For more information, see [ExpressRoute virtual network gateway and FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


For more details on how to configure ExpressRoute Fast Path, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> An UltraPerformance ExpressRoute gateway is required to have ExpressRoute Fast Path working


## <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. The ExpressRoute circuit connects into a Microsoft enterprise edge router (MSEE). 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. After the route is established, it connects into the Azure backbone.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. HANA Large Instance stamps are connected through dedicated enterprise edge router devices to minimize network latency between VMs in Azure IaaS and HANA Large Instance stamps.

The ExpressRoute gateway for the VMs that host SAP application instances are connected to one ExpressRoute circuit that connects to on-premises. 동일한 가상 네트워크는 대규모 인스턴스 스탬프에 전용으로 연결하기 위해 별도의 엔터프라이즈 에지 라우터에 연결됩니다. Using ExpressRoute Fast Path, the data flow from HANA Large Instances to the SAP application layer VMs are not routed through the ExpressRoute gateway anymore and with that reduce the network round-trip latency.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 애플리케이션 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. The assumption is that the ExpressRoute gateway bandwidth of 2-Gbps or 10-Gbps throughput doesn't represent a bottleneck.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

If multiple SAP systems or large SAP systems are deployed to connect to SAP HANA on Azure (Large Instances), the throughput of the ExpressRoute gateway might become a bottleneck. Or you want to isolate production and non-production systems in different Azure virtual networks. 이 경우 애플리케이션 계층을 여러 가상 네트워크로 분할합니다. 또한 다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수도 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

Use a separate virtual network to host VMs that manage storage for mass transfer of data between HANA Large Instances and Azure. This arrangement avoids the effects of large file or data transfer from HANA Large Instance to Azure on the ExpressRoute gateway that serves the VMs that run the SAP application layer. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 더 큰 단일 SAP 애플리케이션 계층에 대해 여러 가상 네트워크를 활용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

  Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 가상 네트워크를 통해 SAP 애플리케이션 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependent on the rules and restrictions, you want to apply between the different virtual networks hosting VMs of different SAP systems, you should peer those virtual networks. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.


## <a name="routing-in-azure"></a>Azure에서 라우팅

By default deployment, three network routing considerations are important for SAP HANA on Azure (Large Instances):

* SAP HANA on Azure (Large Instances) can be accessed only through Azure VMs and the dedicated ExpressRoute connection, not directly from on-premises. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 애플리케이션 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다. For exceptions check the section 'Direct Routing to HANA Large Instances'.

* If you have HANA Large Instance units deployed in two different Azure regions for disaster recovery, the same transient routing restrictions applied in the past. In other words, IP addresses of a HANA Large Instance unit in one region (for example, US West) were not routed to a HANA Large Instance unit deployed in another region (for example, US East). This restriction was independent of the use of Azure network peering across regions or cross-connecting the ExpressRoute circuits that connect HANA Large Instance units to virtual networks. 그래픽 표현은 "여러 지역에서 HANA 대규모 인스턴스 장치 사용" 섹션의 그림을 참조하세요. This restriction, which came with the deployed architecture, prohibited the immediate use of HANA System Replication as disaster recovery functionality. For recent changes, look up the section 'Use HANA Large Instance units in multiple regions'. 

* SAP HANA on Azure (Large Instances) units have an assigned IP address from the server IP pool address range that you submitted when requesting the HANA Large Instance deployment. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. This IP address is accessible through the Azure subscriptions and circuit  that connects Azure virtual networks to HANA Large Instances. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션을 처음 배포하는 경우와 같이 더 이상 NAT를 통해 *할당되지 않습니다*. 

### <a name="direct-routing-to-hana-large-instances"></a>Direct Routing to HANA Large Instances

By default, the transitive routing does not work in these scenarios:

* Between HANA Large Instance units and an on-premises deployment.

* Between HANA Large Instance routing that are deployed in two different regions.

There are three ways to enable transitive routing in those scenarios:

- 데이터를 라우팅하는 역방향 프록시를 사용합니다. For example, F5 BIG-IP, NGINX with Traffic Manager deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises as a virtual firewall/traffic routing solution.
- Linux VM에서 [IPTables 규칙](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다. The VM running IPTables needs to be deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises. The VM needs to be sized accordingly, so, that the network throughput of the VM is sufficient for the expected network traffic. For details on VM network bandwidth, check the article [Sizes of Linux virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) would be another solution to enable direct traffic between on-premises and HANA Large instance units. 

All the traffic of these solutions would be routed through an Azure virtual network and as such the traffic could be additionally restricted by the soft appliances used or by Azure Network Security Groups, so, that certain IP addresses or IP address ranges from on-premises could be blocked or explicitly allowed accessing HANA Large Instances. 

> [!NOTE]  
> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft introduced a new functionality called [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach can be used for HANA Large Instances in two scenarios:

- Enable direct access from on-premises to your HANA Large Instance units deployed in different regions
- Enable direct communication between your HANA Large Instance units deployed in different regions


##### <a name="direct-access-from-on-premises"></a>Direct Access from on-premises
In the Azure regions where Global Reach is offered, you can request enabling the Global Reach functionality for your ExpressRoute circuit that connects your on-premises network to the Azure virtual network that connects to your HANA Large Instance units as well. There are some cost implications for the on-premises side of your ExpressRoute circuit. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). There are no additional costs for you related to the circuit that connects the HANA Large Instance unit(s) to Azure. 

> [!IMPORTANT]  
> In case of using Global Reach for enabling direct access between your HANA Large Instance units and on-premises assets, the network data and control flow is **not routed through Azure virtual networks**, but directly between the Microsoft enterprise exchange routers. As a result any NSG or ASG rules, or any type of firewall, NVA, or proxy you deployed in an Azure virtual network, are not getting touched. **If you use ExpressRoute Global Reach to enable direct access from on-premises to HANA Large instance units restrictions and permissions to access HANA large Instance units need to be defined in firewalls on the on-premises side** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connecting HANA Large Instances in different Azure regions
In the same way, as ExpressRoute Global Reach can be used for connecting on-premises to HANA Large Instance units, it can be used to connect tow HANA Large Instance tenants that are deployed for you in two different regions. The isolation is the ExpressRoute circuits that your HANA Large Instance tenants are using to connect to Azure in both regions. There are no additional charges for connecting two HANA Large Instance tenants that are deployed in two different regions. 

> [!IMPORTANT]  
> The data flow and control flow of the network traffic between the different HANA Large instance tenants will not be routed through azure networks. As a result you can't use Azure functionality or NVAs to enforce communication restrictions between your two HANA Large Instances tenants. 

For more details on how to get ExpressRoute Global Reach enabled, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스는 인터넷에 직접 *연결되지 않습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 애플리케이션 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [이 SAP 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

To realize disaster recovery set ups, you need to have SHANA Large Instance units in multiple Azure regions. Even with using Azure [Global Vnet Peering], the transitive routing by default is not working between HANA Large Instance tenants in two different regions. However, Global Reach opens up the communication path between the HANA Large Instance units you have provisioned in two different regions. This usage scenario of ExpressRoute Global Reach enables:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes


![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

The figure shows how the different virtual networks in both regions are connected to two different ExpressRoute circuits that are used to connect to SAP HANA on Azure (Large Instances) in both Azure regions (grey lines). Reason for this two cross connections is to protect from an outage of the MSEEs on either side. The communication flow between the two virtual networks in the two Azure regions is supposed to be handled over the [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) of the two virtual networks in the two different regions (blue dotted line). The thick red line describes the ExpressRoute Global Reach connection, which allows the HANA Large Instance units of your tenants in two different regions to communicate with each other. 

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.

**다음 단계**
- [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](hana-storage-architecture.md) 참조

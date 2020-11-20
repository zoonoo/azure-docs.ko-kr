---
title: 개념-허브 및 스포크 아키텍처에서 Azure VMware 솔루션 배포 통합
description: Azure의 허브 및 스포크 아키텍처에서 Azure VMware 솔루션 배포를 통합 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 788ef9886e0d102a549e84cd01c658e9e4131c63
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967451"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>허브 및 스포크 아키텍처에서 Azure VMware 솔루션 통합

이 문서에서는 azure에서 기존 또는 새 [허브 및 스포크 아키텍처](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) 에 Azure VMware 솔루션 배포를 통합 하기 위한 권장 사항을 제공 합니다. 


허브 및 스포크 시나리오에서는 하이브리드 클라우드 환경에서 워크 로드를 사용 한다고 가정 합니다.

* IaaS 또는 PaaS 서비스를 사용 하는 기본 Azure
* Azure VMware 솔루션 
* 온-프레미스의 vSphere

## <a name="architecture"></a>Architecture

*허브* 는 온-프레미스 및 Azure VMware 솔루션 사설 클라우드에 대 한 중앙 연결의 역할을 하는 azure Virtual Network입니다. *스포크* 는 가상 네트워크를 허브와 피어 링 하 여 가상 네트워크 간 통신을 가능 하 게 합니다.

온-프레미스 데이터 센터, Azure VMware 솔루션 사설 클라우드 및 허브 간의 트래픽은 Azure Express 경로 연결을 통해 진행 됩니다. 스포크 가상 네트워크는 일반적으로 IaaS 기반 워크 로드를 포함 하지만 Virtual Network와 직접 통합 되거나 [Azure 개인 링크](../private-link/index.yml) 를 사용 하는 다른 paas 서비스를 사용 하는 [App Service Environment](../app-service/environment/intro.md)와 같은 paas 서비스를 사용할 수 있습니다.

>[!IMPORTANT]
>가상 네트워크당 4개의 ExpressRoute 회로 제한을 초과하지 않는 한 기존 ExpressRoute 게이트웨이를 사용하여 Azure VMware Solution에 연결할 수 있습니다.  그러나 ExpressRoute를 통해 온-프레미스에서 Azure VMware Solution에 액세스하려면 ExpressRoute 게이트웨이가 연결된 회로 간에 전이적 라우팅을 제공하지 않으므로 ExpressRoute Global Reach가 있어야 합니다.

이 다이어그램은 Azure에서 Express 경로 Global Reach를 통해 온-프레미스 및 Azure VMware 솔루션에 연결 된 허브 및 스포크 배포의 예를 보여 줍니다.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware 솔루션 허브 및 스포크 통합 배포" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

아키텍처에는 다음과 같은 주요 구성 요소가 있습니다.

- **온-프레미스 사이트:** Express 경로 연결을 통해 Azure에 연결 된 고객 온-프레미스 데이터 센터.

- **Azure VMware 솔루션 사설 클라우드:** 하나 이상의 vSphere 클러스터로 구성 된 Azure VMware 솔루션 SDDC (각각 최대 16 개의 호스트 포함)

- **Express 경로 게이트웨이:** Azure VMware 솔루션 사설 클라우드, 허브 가상 네트워크의 공유 서비스 및 스포크 가상 네트워크에서 실행 되는 워크 로드 간에 통신을 사용 하도록 설정 합니다.

- **Express 경로 Global Reach:** 온-프레미스와 Azure VMware 솔루션 사설 클라우드 간에 연결을 사용 하도록 설정 합니다. Azure VMware 솔루션과 Azure 패브릭 간의 연결은 Express 경로 Global Reach만을 통해 연결 됩니다. Express 경로 빠른 경로 이외의 옵션은 선택할 수 없습니다.  Express 경로 직접 지원 되지 않습니다.


- **S2S VPN 고려 사항:** Azure VMware 솔루션 프로덕션 배포의 경우 VMware HCX의 네트워크 요구 사항으로 인해 Azure S2S VPN이 지원 되지 않습니다. 그러나 PoC 배포에 사용할 수 있습니다.


- **허브 가상 네트워크:** 온-프레미스 네트워크 및 Azure VMware 솔루션 사설 클라우드로의 중앙 연결 지점 역할을 합니다.

- **스포크 가상 네트워크**

    - **IaaS 스포크:** IaaS 스포크는 VM 가용성 집합, 가상 머신 확장 집합 및 해당 하는 네트워크 구성 요소를 포함 하 여 Azure IaaS 기반 워크 로드를 호스팅합니다.

    - **PaaS 스포크:** PaaS 스포크는 개인 [끝점](../private-link/private-endpoint-overview.md) 및 개인 [링크](../private-link/private-link-overview.md)를 사용 하 여 개인 주소 지정을 통해 Azure PaaS 서비스를 호스팅합니다.

- **Azure 방화벽:** 스포크와 Azure VMware 솔루션 간에 트래픽을 분할 하는 중심의 역할을 합니다.

- **Application Gateway:** Azure IaaS/PaaS 또는 Azure VMware 솔루션 Vm (가상 머신)에서 실행 되는 웹 앱을 노출 하 고 보호 합니다. API Management와 같은 다른 서비스와 통합 됩니다.

## <a name="network-and-security-considerations"></a>네트워크 및 보안 고려 사항

Express 경로 연결을 사용 하면 온-프레미스, Azure VMware 솔루션 및 Azure 네트워크 패브릭 간에 트래픽을 전달할 수 있습니다. Azure VMware 솔루션은 [express 경로 Global Reach](../expressroute/expressroute-global-reach.md) 를 사용 하 여이 연결을 구현 합니다.

Express 경로 게이트웨이는 연결 된 회로 간에 전이적 라우팅을 제공 하지 않으므로 온-프레미스 연결은 Express 경로 Global Reach를 사용 하 여 온-프레미스 vSphere 환경 및 Azure VMware 솔루션 간에 통신 해야 합니다. 

* **온-프레미스와 Azure VMware 솔루션 트래픽 흐름**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="온-프레미스와 Azure VMware 솔루션 트래픽 흐름" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **허브 VNET 트래픽 흐름에 대 한 Azure VMware 솔루션**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="허브 가상 네트워크 트래픽 흐름에 대 한 Azure VMware 솔루션" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Azure vmware 솔루션 네트워킹 및 연결 개념에 대 한 자세한 내용은 [Azure Vmware 솔루션 제품 설명서](./concepts-networking.md)에서 확인할 수 있습니다.

### <a name="traffic-segmentation"></a>트래픽 조각화

[Azure 방화벽](../firewall/index.yml) 은 허브 및 스포크 토폴로지의 중앙 조각으로, 허브 가상 네트워크에 배포 됩니다. Azure 방화벽 또는 다른 Azure 지원 네트워크 가상 어플라이언스를 사용 하 여 트래픽 규칙을 설정 하 고 여러 스포크 및 Azure VMware 솔루션 워크 로드 간 통신을 분할 합니다.

경로 테이블을 만들어 트래픽을 Azure 방화벽으로 보냅니다.  스포크 가상 네트워크의 경우 기본 경로를 Azure 방화벽의 내부 인터페이스로 설정 하는 경로를 만듭니다. 이러한 방식으로 Virtual Network의 워크 로드가 Azure VMware 솔루션 주소 공간에 연결 되어야 하는 경우 방화벽은이를 평가 하 고 허용 하거나 거부 하는 해당 트래픽 규칙을 적용할 수 있습니다.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Azure 방화벽으로 트래픽을 전송 하는 경로 테이블 만들기" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> **게이트웨이 서브넷** 설정에서 주소 접두사가 0.0.0.0/0 인 경로는 지원 되지 않습니다.

해당 경로 테이블의 특정 네트워크에 대 한 경로를 설정 합니다. 예를 들어, 스포크 워크 로드에서 Azure VMware 솔루션 관리 및 워크 로드 IP 접두사에 도달 하는 경로 및 그 밖의 방법입니다.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="해당 경로 테이블의 특정 네트워크에 대 한 경로 설정" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

스포크 및 허브 내의 네트워크 보안 그룹을 사용 하 여 두 번째 트래픽 조각화를 통해 보다 세분화 된 트래픽 정책을 만들 수 있습니다.

> [!NOTE]
> **온-프레미스에서 Azure VMware 솔루션으로의 트래픽:** 온-프레미스 워크 로드 간의 트래픽 (vSphere 기반 또는 기타)은 Global Reach에서 사용 하도록 설정 되지만 트래픽이 허브의 Azure 방화벽을 통과 하지 않습니다. 이 시나리오에서는 온-프레미스 또는 Azure VMware 솔루션에서 트래픽 조각화 메커니즘을 구현 해야 합니다.

### <a name="application-gateway"></a>Application Gateway

Azure 애플리케이션 Gateway V1 및 V2는 백 엔드 풀로 Azure VMware 솔루션 Vm에서 실행 되는 웹 앱을 사용 하 여 테스트 되었습니다. Application Gateway는 현재 Azure VMware 솔루션 Vm에서 실행 되는 웹 앱을 인터넷에 노출 하는 유일한 지원 방법입니다. 또한 앱을 내부 사용자에 게 안전 하 게 노출할 수 있습니다.

세부 정보 및 요구 사항은 [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) 에서 Azure VMware 솔루션 관련 문서를 검토 합니다.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="네트워크 보안 그룹을 사용 하는 두 번째 트래픽 조각화 수준" border="false":::


### <a name="jump-box-and-azure-bastion"></a>점프 상자 및 Azure 방호

허브 가상 네트워크 내의 공유 서비스 서브넷에 배포 된 Windows 10 또는 Windows Server VM 인 점프 상자를 사용 하 여 Azure VMware 솔루션 환경에 액세스 합니다.

>[!IMPORTANT]
>Azure는 Azure VMware 솔루션을 인터넷에 노출 하지 않도록 하기 위해 점프 상자에 연결 하는 서비스입니다. Azure 가상 사용자는 azure IaaS 개체가 아니기 때문에 azure 방호를 사용 하 여 azure VMware 솔루션 Vm에 연결할 수 없습니다.  

보안 모범 사례에 따라 허브 가상 네트워크 내에 [Microsoft Azure 방호](../bastion/index.yml) 서비스를 배포 합니다. Azure 방호는 이러한 리소스에 공용 IP 주소를 프로 비전 할 필요 없이 Azure에 배포 된 Vm에 대 한 원활한 RDP 및 SSH 액세스를 제공 합니다. Azure 방호 서비스를 프로 비전 하 고 나면 Azure Portal에서 선택한 VM에 액세스할 수 있습니다. 연결을 설정 하 고 나면 점프 상자 바탕 화면을 표시 하는 새 탭이 열리고, 해당 바탕 화면에서 Azure VMware 솔루션 사설 클라우드 관리 평면에 액세스할 수 있습니다.

> [!IMPORTANT]
> 공용 IP 주소를 점프 상자 VM에 제공 하거나 3389/TCP 포트를 공용 인터넷에 노출 하지 마십시오. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure 방호 허브 가상 네트워크" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 확인 고려 사항

Azure DNS 해결에는 다음 두 가지 옵션을 사용할 수 있습니다.

-   허브에 배포 된 Azure Active Directory (Azure AD) 도메인 컨트롤러 ( [id 고려 사항](#identity-considerations)참조)를 이름 서버로 사용 합니다.

-   Azure DNS 개인 영역을 배포 하 고 구성 합니다.

가장 좋은 방법은 두 가지를 결합 하 여 Azure VMware 솔루션, 온-프레미스 및 Azure에 대 한 안정적인 이름 확인을 제공 하는 것입니다.

일반적인 디자인 권장 사항으로, 기존 Azure DNS 인프라 (이 경우에는 Active Directory 통합 DNS)를 허브 가상 네트워크에 배포 되 고 스포크 가상 네트워크에 구성 된 두 개 이상의 Azure Vm에 배포 하 여 DNS 설정에서 해당 Azure DNS 서버를 사용 합니다.

Azure 사설 DNS 영역을 가상 네트워크에 연결 하는 Azure 사설 DNS를 사용할 수 있습니다.  DNS 서버는 온-프레미스 또는 고객 Azure 사설 DNS 인프라를 활용 하는 DNS를 실행 하는 Azure VMware 솔루션에 조건부 전달이 있는 하이브리드 확인자로 사용 됩니다. 

스포크 가상 네트워크 내에 배포 된 Vm에 대 한 DNS 레코드의 수명 주기를 자동으로 관리 하려면 이라고를 사용 하도록 설정 합니다. 사용 하도록 설정 하는 경우 최대 개인 DNS 영역 수는 하나 뿐입니다. 사용 하지 않도록 설정 된 경우 최대 수는 1000입니다.

Azure의 azure 사설 DNS 영역에 대해 Azure의 확인자 Vm에 대 한 조건부 전달자를 사용 하 여 온-프레미스 및 Azure VMware 솔루션 서버를 구성할 수 있습니다.

## <a name="identity-considerations"></a>Id 고려 사항

Id를 위해 가장 좋은 방법은 허브에 하나 이상의 AD 도메인 컨트롤러를 배포 하는 것입니다. 영역 분산 방식 또는 VM 가용성 집합에서 두 개의 공유 서비스 서브넷을 사용 합니다. 온-프레미스 AD 도메인을 Azure로 확장 [Azure 아키텍처 센터](/azure/architecture/reference-architectures/identity/adds-extend-domain) 를 참조 하세요.

또한 Azure VMware 솔루션 쪽의 다른 도메인 컨트롤러를 배포 하 여 vSphere 환경 내에서 id 및 DNS 원본으로 작동 합니다.

권장 되는 최선의 방법은 [AD 도메인을 Azure Active Directory와](/azure/architecture/reference-architectures/identity/azure-ad)통합 하는 것입니다.

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->

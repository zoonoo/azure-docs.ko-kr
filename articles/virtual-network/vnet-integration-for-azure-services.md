---
title: 네트워크 격리를 위한 Azure 서비스의 가상 네트워크 통합
titlesuffix: Azure Virtual Network
description: 이 문서에서는 azure 서비스에 안전 하 게 액세스할 수 있도록 하는 가상 네트워크에 Azure 서비스를 통합 하는 다양 한 방법을 설명 합니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: b01ade25c6592e68e34eeb542a90cdd0124a0446
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96738050"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>네트워크 격리를 위해 가상 네트워크와 Azure 서비스 통합

Azure 서비스에 대 한 통합을 Virtual Network 하 여 가상 네트워크 인프라로만 서비스에 대 한 액세스를 잠글 수 있습니다. VNet 인프라에는 피어 링 가상 네트워크 및 온-프레미스 네트워크도 포함 됩니다.

Azure 서비스는 다음 방법 중 하나 이상을 통해 네트워크 격리를 구현 합니다.
- [서비스의 전용 인스턴스를 가상 네트워크에 배포](virtual-network-service-endpoints-overview.md)합니다. 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 [Azure 개인 링크](../private-link/private-link-overview.md)를 통해 제공 되는 서비스에 비공개로 안전 하 게 연결 합니다. 개인 끝점은 VNet의 개인 IP 주소를 사용 하 여 서비스를 가상 네트워크에 효과적으로 제공 합니다.
- 서비스 [끝점](virtual-network-service-endpoints-overview.md)을 통해 가상 네트워크를 서비스로 확장 하 여 공용 끝점을 사용 하 여 서비스에 액세스 합니다. 서비스 엔드포인트를 통해 서비스 리소스를 가상 네트워크로 보호할 수 있습니다.
- [서비스 태그](service-tags-overview.md) 를 사용 하 여 공용 IP 끝점에 대 한 Azure 리소스에 대 한 트래픽을 허용 하거나 거부 합니다.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>가상 네트워크에 전용 Azure 서비스 배포

가상 네트워크에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 전용 Azure 서비스 배포](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

전용 Azure 서비스를 가상 네트워크에 배포 하면 다음과 같은 기능을 제공 합니다.
- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 사이트 간 VPN(VPN Gateway) 또는 ExpressRoute를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 피어링하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스를 통해 완전히 관리됩니다. 여기에는 리소스의 상태를 모니터링 하 고 부하를 통해 크기를 조정 하는 작업이 포함 됩니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대한 인바운드 및 아웃바운드 네트워크 액세스는 서비스에서 제공하는 지침에 따라 네트워크 보안 그룹을 통해 열어야 합니다.
- 또한 특정 서비스는 배포되는 서브넷에 제한을 적용하여 정책 애플리케이션을 제한하고 동일한 서브넷 내에서 VM 및 서비스 리소스를 라우팅 또는 결합합니다. 시간이 지남에 따라 변경될 수 있으므로 특정 제한 사항에 대해서는 각 서비스에 문의하세요. 이러한 서비스의 예로는 Azure NetApp Files, 전용 HSM, Azure Container Instances, App Service가 있습니다.
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 위임된 서브넷을 요구할 수 있습니다. 위임하면 서비스는 위임된 서브넷에서 서비스별 리소스를 만들 수 있는 명시적 권한을 부여합니다.
- 위임된 서브넷이 있는 가상 네트워크에서 REST API 응답의 예를 참조하세요. 위임된 서브넷 모델을 사용하는 포괄적인 서비스 목록은 사용 가능한 위임 API를 통해 얻을 수 있습니다.

가상 네트워크에 배포할 수 있는 서비스 목록은 [가상 네트워크에 전용 Azure 서비스 배포](virtual-network-for-azure-services.md)를 참조 하세요.

## <a name="private-link-and-private-endpoints"></a>개인 링크 및 개인 끝점

개인 끝점을 사용 하 여 공용 인터넷을 통하지 않고 개인 링크를 통해 안전 하 게 가상 네트워크에서 Azure 리소스로 직접 이벤트를 수신 하도록 허용할 수 있습니다. 개인 끝점은 가상 네트워크의 Azure 서비스에 대 한 특별 한 네트워크 인터페이스입니다. Azure 리소스에 대 한 개인 끝점을 만들 때 가상 네트워크와 Azure 리소스의 클라이언트 간에 보안 연결을 제공 합니다. 개인 끝점에는 가상 네트워크의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 Azure 서비스 간의 연결은 보안 개인 링크를 사용 합니다.

다음 예제에서는 가상 네트워크의 클라이언트와 Event Grid 리소스 간에 보안 연결을 제공 하는 Event Grid 리소스 개인 끝점에 대 한 개인 액세스를 보여 줍니다.

![개인 끝점을 사용 하 여 SQL DB 리소스에 대 한 개인 액세스](./media/network-isolation/architecture-diagram.png)

개인 링크 및 지원 되는 Azure 서비스 목록에 대 한 자세한 내용은 [개인 링크 란?](../private-link/private-link-overview.md) 을 참조 하세요.

## <a name="service-endpoints"></a>서비스 엔드포인트
VNet 서비스 끝점은 Azure 백본 네트워크를 통해 최적화 된 경로를 통해 Azure 서비스에 대 한 안전한 직접 연결을 제공 합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 끝점을 통해 vnet의 개인 IP 주소는 VNet에서 공용 IP 주소가 없어도 Azure 서비스의 끝점에 연결할 수 있습니다.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

자세한 내용은 [가상 네트워크 서비스 끝점](virtual-network-service-endpoints-overview.md) 을 참조 하세요.

## <a name="service-tags"></a>서비스 태그

서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. 서비스 태그를 사용 하 여 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 또는 [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags)에서 네트워크 액세스 제어를 정의할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureEventGrid)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다.

![서비스 태그를 사용 하 여 트래픽 허용 또는 거부](./media/network-isolation/service-tags.png)

퍼블릭 엔드포인트가 있는 Azure 서비스에 액세스하면서, 서비스 태그를 사용하여 네트워크 격리를 수행하고 일반 인터넷에서 Azure 리소스를 보호할 수 있습니다. 인바운드/아웃바운드 네트워크 보안 그룹 규칙을 만들어 **인터넷** 에서 들어오고 나가는 트래픽을 거부하고 **AzureCloud** 또는 특정 Azure 서비스의 다른 [사용 가능한 서비스 태그](service-tags-overview.md#available-service-tags)에서 들어오고 나가는 트래픽을 허용합니다.

서비스 태그 및 서비스 태그를 지 원하는 Azure 서비스에 대 한 자세한 내용은 [서비스 태그 개요](service-tags-overview.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 네트워크와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)하는 방법에 대해 알아봅니다.
- [서비스 태그를 사용 하 여 리소스에 대 한 액세스를 제한](tutorial-restrict-network-access-to-resources.md)하는 방법을 알아봅니다.
- [Azure 개인 링크를 사용 하 여 개인적으로 Azure Cosmos 계정에 연결](../private-link/create-private-endpoint-cosmosdb-portal.md)하는 방법을 알아봅니다.
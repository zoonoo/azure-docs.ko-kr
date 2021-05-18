---
title: 네트워크 격리를 위한 Azure 서비스의 가상 네트워크 통합
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure 서비스에 안전하게 액세스할 수 있는 가상 네트워크에 Azure 서비스를 통합하는 다양한 방법을 설명합니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785535"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>네트워크 격리를 위해 가상 네트워크와 Azure 서비스 통합

Azure 서비스에 대한 VNet(가상 네트워크) 통합을 사용하면 가상 네트워크 인프라로만 서비스에 대한 액세스를 잠글 수 있습니다. VNet 인프라에는 피어링된 가상 네트워크와 온-프레미스 네트워크도 포함됩니다.

VNet 통합은 Azure 서비스에 네트워크 격리의 이점을 제공하고 다음 방법 중 하나 이상을 통해 수행할 수 있습니다.
- [서비스의 전용 인스턴스를 가상 네트워크에 배포합니다](virtual-network-for-azure-services.md). 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- [Azure Private Link](../private-link/private-link-overview.md)가 제공하는, 서비스에 비공개로 안전하게 연결하는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용합니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 가상 네트워크로 효과적으로 가져옵니다.
- [서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 통해 가상 네트워크를 서비스로 확장하여 퍼블릭 엔드포인트를 사용하여 서비스에 액세스합니다. 서비스 엔드포인트를 통해 서비스 리소스를 가상 네트워크로 보호할 수 있습니다.
- [서비스 태그](service-tags-overview.md)를 사용하여 공용 IP 엔드포인트에 대한 Azure 리소스의 트래픽을 허용하거나 거부합니다.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>가상 네트워크에 전용 Azure 서비스 배포

가상 네트워크에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 전용 Azure 서비스 배포](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

전용 Azure 서비스를 가상 네트워크에 배포하면 다음과 같은 기능이 제공됩니다.
- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 사이트 간 VPN(VPN Gateway) 또는 ExpressRoute를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 피어링하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스를 통해 완전히 관리됩니다. 여기에는 리소스의 상태 모니터링 및 로드에 따른 크기 조정이 포함됩니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대한 인바운드 및 아웃바운드 네트워크 액세스는 서비스에서 제공하는 지침에 따라 네트워크 보안 그룹을 통해 열어야 합니다.
- 또한 특정 서비스는 배포되는 서브넷에 제한을 적용하여 정책 애플리케이션을 제한하고 동일한 서브넷 내에서 VM 및 서비스 리소스를 라우팅 또는 결합합니다. 시간이 지남에 따라 변경될 수 있으므로 특정 제한 사항에 대해서는 각 서비스에 문의하세요. 이러한 서비스의 예로는 Azure NetApp Files, 전용 HSM, Azure Container Instances, App Service가 있습니다.
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 위임된 서브넷을 요구할 수 있습니다. 위임하면 서비스는 위임된 서브넷에서 서비스별 리소스를 만들 수 있는 명시적 권한을 부여합니다.
- 위임된 서브넷이 있는 가상 네트워크에서 REST API 응답의 예를 참조하세요. 위임된 서브넷 모델을 사용하는 포괄적인 서비스 목록은 사용 가능한 위임 API를 통해 얻을 수 있습니다.

가상 네트워크에 배포할 수 있는 서비스 목록은 [가상 네트워크에 전용 Azure 서비스 배포](virtual-network-for-azure-services.md)를 참조하세요.

## <a name="private-link-and-private-endpoints"></a>프라이빗 링크 및 프라이빗 엔드포인트

프라이빗 엔드포인트를 사용하여 공용 인터넷을 통하지 않고 프라이빗 링크를 통해 안전하게 가상 네트워크에서 Azure 리소스로 직접 이벤트를 수신할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크의 Azure 서비스에 대한 특별한 네트워크 인터페이스입니다. Azure 리소스에 대한 프라이빗 엔드포인트를 만들면 가상 네트워크의 클라이언트와 Azure 리소스 간에 보안 연결이 제공됩니다. 프라이빗 엔드포인트에는 가상 네트워크의 IP 주소 범위에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 Azure 서비스 간의 연결은 보안 프라이빗 링크를 사용합니다.

다음 예에서는 가상 네트워크의 클라이언트와 Event Grid 리소스 간에 보안 연결을 제공하는 Event Grid 리소스 프라이빗 엔드포인트의 프라이빗 액세스를 보여 줍니다.

![프라이빗 엔드포인트를 사용하여 SQL DB 리소스의 프라이빗 액세스](./media/network-isolation/architecture-diagram.png)

프라이빗 링크 및 지원되는 Azure 서비스 목록에 대한 자세한 내용은 [프라이빗 링크란?](../private-link/private-link-overview.md)을 참조하세요.

## <a name="service-endpoints"></a>서비스 엔드포인트
VNet 서비스 엔드포인트는 Azure 백본 네트워크에서 최적화된 경로를 통해 Azure 서비스에 대한 안전한 직접 연결을 제공합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 엔드포인트를 통해 VNet의 개인 IP 주소는 VNet에 공용 IP 주소가 없어도 Azure 서비스의 엔드포인트에 연결할 수 있습니다.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

자세한 내용은 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 참조하세요.

## <a name="service-tags"></a>서비스 태그

서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. 서비스 태그를 사용하여 [네트워크 보안 그룹](./network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 서비스 태그 이름(예: AzureEventGrid)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다.

![서비스 태그를 사용하여 트래픽 허용 또는 거부](./media/network-isolation/service-tags.png)

퍼블릭 엔드포인트가 있는 Azure 서비스에 액세스하면서, 서비스 태그를 사용하여 네트워크 격리를 수행하고 일반 인터넷에서 Azure 리소스를 보호할 수 있습니다. 인바운드/아웃바운드 네트워크 보안 그룹 규칙을 만들어 **인터넷** 에서 들어오고 나가는 트래픽을 거부하고 **AzureCloud** 또는 특정 Azure 서비스의 다른 [사용 가능한 서비스 태그](service-tags-overview.md#available-service-tags)에서 들어오고 나가는 트래픽을 허용합니다.

서비스 태그와 이를 지원하는 Azure 서비스에 대한 자세한 내용은 [서비스 태그 개요](service-tags-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 네트워크와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)하는 방법에 대해 알아봅니다.
- [서비스 태그를 사용하여 리소스에 대한 액세스를 제한](tutorial-restrict-network-access-to-resources.md)하는 방법에 대해 알아봅니다.
- [Azure Private Link를 사용하여 Azure Cosmos 계정에 비공개로 연결](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)하는 방법을 알아봅니다.
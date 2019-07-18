---
title: Azure 가용성 영역의 영역 중복 가상 네트워크 게이트웨이 정보 | Microsoft Docs
description: 가용성 영역의 VPN Gateway 및 ExpressRoute 게이트웨이에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391075"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Azure 가용성 영역의 영역 중복 가상 네트워크 게이트웨이 정보

[Azure Availability Zones](../availability-zones/az-overview.md)에서 VPN 및 ExpressRoute 게이트웨이를 배포할 수 있습니다. 그러면 가상 네트워크 게이트웨이에 복원력, 확장성 및 고가용성이 제공됩니다. Azure Availability Zones에서 게이트웨이를 배포하면 Azure에서 영역 수준 오류로의 온-프레미스 네트워크 연결성을 보호하면서 물리적 및 논리적으로 영역 내 게이트웨이를 구분합니다.

### <a name="zrgw"></a>영역 중복 게이트웨이

가용성 영역에 가상 네트워크 게이트웨이를 자동으로 배포하는 데 영역 중복 가상 네트워크 게이트웨이를 사용할 수 있습니다. 영역 중복 게이트웨이를 사용하면 영역 복원력을 활용하여 Azure의 확장성 있는 중요 업무용 서비스에 액세스할 수 있습니다.

<br>
<br>

![영역 중복 게이트웨이 그래픽](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>영역 게이트웨이

특정 영역에 게이트웨이를 배포하려면 영역 게이트웨이를 사용합니다. 영역 게이트웨이를 배포하면 게이트웨이의 모든 인스턴스가 동일한 가용성 영역에 배포됩니다.

<br>
<br>

![영역 게이트웨이 그래픽](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>게이트웨이 SKU

영역 중복 및 영역 게이트웨이는 새 게이트웨이 SKU로 제공됩니다. Azure AZ 지역에 새 가상 네트워크 게이트웨이 SKU가 추가되었습니다. 이러한 SKU는 영역 중복 및 영역 게이트웨이 특정 SKU라는 점을 제외하고 ExpressRoute 및 VPN Gateway에 해당하는 기존 SKU와 비슷합니다.

새 게이트웨이 SKU는 다음과 같습니다.

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>공용 IP SKU

영역 중복 게이트웨이 및 영역 게이트웨이는 모두 Azure 공용 IP 리소스 *표준* SKU에 의존합니다. Azure 공용 IP 리소스의 구성은 배포하는 게이트웨이가 영역 중복인지 아니면 영역인지 여부를 결정합니다. *기본* SKU를 통해 공용 IP 리소스를 만드는 경우 게이트웨이에는 영역 중복성이 없으며 게이트웨이 리소스는 영역별이 됩니다.

### <a name="pipzrg"></a>영역 중복 게이트웨이

영역을 지정하지 않고 **표준** 공용 IP SKU를 사용하여 공용 IP 주소를 만드는 경우 동작은 게이트웨이가 VPN 게이트웨이인지 아니면 ExpressRoute 게이트웨이인지에 따라 다릅니다. 

* VPN 게이트웨이의 경우 두 게이트웨이 인스턴스가 영역 중복성을 제공하도록 이러한 세 영역 중 2곳에 배포됩니다. 
* ExpressRoute 게이트웨이의 경우 인스턴스가 세 개 이상일 수 있으므로 게이트웨이를 모든 세 영역 모두에서 확장할 수 있습니다.

### <a name="pipzg"></a>영역 게이트웨이

**표준** 공용 IP SKU를 사용하여 공용 IP 주소를 만들고 영역(1, 2 또는 3)을 지정하는 경우 모든 게이트웨이 인스턴스가 동일한 영역에 배포됩니다.

### <a name="piprg"></a>지역 게이트웨이

**기본** 공용 IP SKU를 사용하여 공용 IP 주소를 만드는 경우 게이트웨이가 지역 게이트웨이로 배포되며 게이트웨이에 기본 제공된 영역 중복성이 없습니다.

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>새 SKU를 배포할 때 변경되는 사항은 무엇인가요?

사용자 관점에서 영역 중복성이 포함된 게이트웨이를 배포할 수 있습니다. 즉, Azure Availability Zones 간에 게이트웨이의 모든 인스턴스가 배포되고, 각 가용성 영역은 다른 장애 및 업데이트 도메인입니다. 이렇게 하면 사용자 게이트웨이가 영역 오류에 대해 더 안정적이고, 사용 가능하며, 복원력이 증가합니다.

### <a name="can-i-use-the-azure-portal"></a>Azure Portal을 사용할 수 있나요?

예, Azure Portal을 사용하여 새 SKU를 배포할 수 있습니다. 그러나 Azure 가용성 영역이 있는 Azure 지역에서만 이러한 새 SKU를 볼 수 있습니다.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>새 SKU를 사용할 수 있는 지역은 어디인가요?

Azure 가용성 영역이 있는 Azure 지역(미국 중부, 프랑스 중부, 유럽 북부, 유럽 서부 및 미국 서부 2 지역)에서 새 SKU를 사용할 수 있습니다. 앞으로 다른 Azure 공용 지역에서도 영역 중복 게이트웨이를 사용할 수 있도록 제공할 예정입니다.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>내 기존 가상 네트워크 게이트웨이를 영역 중복 또는 영역 게이트웨이로 변경/마이그레이션/업그레이드할 수 있나요?

영역 중복 또는 영역 게이트웨이로의 기존 가상 네트워크 게이트웨이 마이그레이션은 현재 지원되지 않습니다. 단, 기존 게이트웨이를 삭제하고 영역 중복 또는 영역 게이트웨이를 다시 만들 수는 있습니다.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이를 모두 배포할 수 있나요?

동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이를 함께 사용할 수 있습니다. 그러나 게이트웨이 서브넷에 대해 /27 IP 주소 범위를 예약해야 합니다.

## <a name="next-steps"></a>다음 단계

[영역 중복 가상 네트워크 게이트웨이 만들기](create-zone-redundant-vnet-gateway.md)

---
title: 'Azure Virtual WAN: 허브의 네트워크 가상 어플라이언스에 대한 정보'
description: Virtual WAN 허브의 네트워크 가상 어플라이언스에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/02/2021
ms.author: scottnap
ms.openlocfilehash: 68e5216257fd32237f3d67f05f0e17a0b8e16dbd
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411911"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>Azure Virtual WAN 허브의 네트워크 가상 어플라이언스에 대한 정보

Azure Virtual WAN은 네트워킹 파트너들과 협력하여 네트워킹 파트너의 CPE(고객 프레미스 장비)를 가상 허브의 Azure VPN 게이트웨이에 쉽게 연결할 수 있도록 자동화를 구축했습니다. Azure는 고객이 타사 네트워크 가상 어플라이언스(NVA)를 가상 허브에 직접 배포할 수 있도록 엄선된 네트워킹 파트너들과 협력하고 있습니다. 이를 통해, 분기 CPE를 가상 허브의 동일한 브랜드 NVA에 연결하고자 하는 고객은 재산적 가치를 가지는 엔드투엔드 SD-WAN 기능을 활용할 수 있습니다.

Barracuda Networks 및 Cisco Systems는 Virtual WAN 허브에 직접 배포할 수 있는 NVA를 제공하는 최초의 파트너입니다.  각 제품 설명서는 [Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan), [다중 클라우드용 Cisco Cloud OnRamp](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 및 [VMware SD-WAN](https://kb.vmware.com/s/article/82746)을 참조하세요. Azure는 현재 더 많은 파트너들과 협력 중에 있으므로 앞으로 다른 여러 제품을 기대하시기 바랍니다.

> [!NOTE]
> Virtual WAN 허브에 배포할 수 있는 NVA 제품만 Virtual WAN 허브에 배포할 수 있습니다. Azure에 있는 임의의 가상 네트워크에는 배포할 수 없습니다.

## <a name="how-does-it-work"></a><a name="how"></a>작동 원리

Azure Virtual WAN 허브에 직접 배포할 수 있는 NVA는 가상 허브에서 사용하도록 엔지니어링되었습니다. NVA 제품은 Azure Marketplace에 관리형 애플리케이션으로 게시되며 고객은 Azure Marketplace에서 직접 제품을 배포하거나 Azure Portal을 통해 가상 허브에서 제품을 배포할 수 있습니다.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="프로세스 개요":::

각 파트너의 NVA 제품은 배포 요구 사항에 따라 다소 다른 환경과 기능을 갖게 됩니다. 그러나 Virtual WAN 허브에 있는 NVA에 대한 모든 파트너 제품에는 몇 가지 공통점이 있습니다.

* Azure Marketplace를 통해 제공되는 관리형 애플리케이션 환경.
* NVA 인프라 단위 기반 용량 및 청구.
* Azure Monitor를 통해 표시되는 상태 메트릭.

### <a name="managed-application"></a><a name="managed"></a>관리형 애플리케이션

Virtual WAN 허브에 배포할 수 있는 모든 NVA 제품은 Azure Marketplace에 있는 **관리형 애플리케이션** 을 갖게 됩니다. 관리형 애플리케이션을 통해 파트너는 다음을 수행할 수 있습니다.

* NVA에 대한 사용자 지정 배포 환경을 빌드합니다.
* Virtual WAN 허브에서 NVA를 직접 만들 수 있는 특수 Resource Manager 템플릿을 제공합니다.
* 소프트웨어 라이선스 비용을 직접 또는 Azure Marketplace를 통해 청구합니다.
* 사용자 지정 속성 및 리소스 미터를 표시합니다.

NVA 파트너는 어플라이언스 배포, 구성 라이선스 및 관리 요구 사항에 따라 다양한 리소스를 만들 수 있습니다. 고객이 모든 관리형 애플리케이션과 같이 Virtual WAN 허브에서 NVA를 만들 때 구독에 두 개의 리소스 그룹이 생성됩니다.

* **고객 리소스 그룹** - 관리형 애플리케이션에 대한 애플리케이션 자리 표시자를 포함합니다. 파트너는 이를 사용하여 이 곳에서 선택하는 모든 고객 속성을 노출할 수 있습니다.
* **관리되는 리소스 그룹** - 관리형 애플리케이션의 게시자가 컨트롤하므로 고객이 직접 이 리소스 그룹의 리소스를 구성하거나 변경할 수 없습니다. 관리되는 리소스 그룹에는 **NetworkVirtualAppliances** 리소스가 포함됩니다.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="관리형 애플리케이션 리소스 그룹":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 인프라 단위

Virtual WAN 허브에서 NVA를 만들 때 배포하려는 NVA 인프라 단위의 수를 선택해야 합니다. **NVA 인프라 단위** 는 Virtual WAN 허브의 NVA에 대한 집계 대역폭 용량의 단위입니다. 용량 및 크기 조정을 생각하는 방식 면에서 **NVA 인프라 단위** 는 VPN [배율 단위](pricing-concepts.md#scale-unit)와 비슷합니다.

* 1 NVA 인프라 단위는 NVA로 들어오는 모든 분기 사이트 연결에 대한 500Mbps의 집계 대역폭에 해당하며 비용은 시간당 0.25달러입니다.
* Azure는 지정된 NVA 가상 허브 배포에 대해 1-80 NVA 인프라 단위를 지원합니다.
* 각 파트너는 지원되는 모든 NVA 인프라 단위 구성의 하위 집합인 여러 NVA 인프라 단위 번들을 제공할 수 있습니다.

*1 인프라 단위 = 500Mbps* 를 선택하는 경우 중복성을 위해 두 개의 인스턴스가 생성되고 각각은 최대 500Mbps의 처리량을 갖게 된다는 점에서 VPN 배율 단위와 유사합니다. 예를 들어, 각각 10Mbps를 수행하는 5개의 분기가 있는 경우 헤드 끝에서 50Mbps로 집계되어야 합니다. 허브에 대한 분기 수를 지원하는 데 필요한 용량을 평가한 후 NVA의 집계 용량 계획을 수립해야 합니다.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>네트워크 가상 어플라이언스 구성 프로세스

파트너들은 배포 프로세스의 일부로 NVA를 자동으로 구성하는 환경을 제공하기 위해 노력했습니다. NVA가 가상 허브로 프로비전되면 NVA에 필요할 수 있는 추가 구성은 NVA 파트너 포털 또는 관리 애플리케이션을 통해 수행해야 합니다. NVA에 대한 직접 액세스 권한은 제공되지 않습니다.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>NVA를 포함하는 사이트 및 연결 리소스

Azure VPN Gateway 구성과 달리 **사이트** 리소스, **사이트 간 연결** 리소스 또는 **지점 및 사이트 간 연결** 리소스를 만들어 Virtual WAN 허브의 NVA에 분기 사이트를 연결할 필요가 없습니다. 이것은 모두 NVA 파트너를 통해 관리됩니다.

Virtual WAN 허브를 Azure 가상 네트워크에 연결할 때도 허브 및 VNet 간 연결을 만들어야 합니다.

## <a name="supported-regions"></a><a name="regions"></a>지원되는 지역

가상 허브의 NVA는 다음 지역에서 제공됩니다.

|지역 | Azure 지역|
|---|---|
| 북아메리카| 캐나다 중부, 캐나다 동부, 미국 중부, 미국 동부, 미국 동부 2, 미국 중남부, 미국 북부, 미국 중서부, 미국 서부, 미국 서부 2 |
| 남아메리카 | 브라질 남부, 브라질 남동부 |
| 유럽 | 프랑스 중부, 프랑스 남부, 독일 북부, 독일 중서부, 북유럽, 노르웨이 동부, 노르웨이 서부, 스위스 북부, 스위스 서부, 영국 남부, 영국 서부, 서유럽|
|  중동 | 아랍에미리트 북부 |
| 아시아 |  동아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 아시아 남동부 | 
| 오스트레일리아 | 오스트레일리아 남동부, 오스트레일리아 동부, 오스트레일리아 중부, 오스트레일리아 중부 2|
| 아프리카 | 남아프리카 북부 |
| 인도 | 인도 남부, 인도 서부, 인도 중부 | 

## <a name="nva-faq"></a>NVA FAQ

[!INCLUDE [NVA FAQ](../../includes/virtual-wan-nva-hub-faq.md)]

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대해 자세히 알아보려면 [Virtual WAN 개요](virtual-wan-about.md) 문서를 참조하세요.

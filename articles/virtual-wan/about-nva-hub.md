---
title: 'Azure 가상 WAN: 허브의 네트워크 가상 어플라이언스 정보'
description: 이 문서에서는 가상 WAN 허브의 네트워크 가상 어플라이언스에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313776"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Azure 가상 WAN 허브 (미리 보기)의 네트워크 가상 어플라이언스 정보

Azure 가상 WAN은 네트워킹 파트너와 협력 하 여 고객의 고객 프레미스 장비 (CPE)를 가상 허브의 Azure VPN gateway에 쉽게 연결할 수 있도록 하는 자동화를 구축 했습니다. Azure는 고객이 타사 네트워크 가상 어플라이언스 (NVA)를 가상 허브에 직접 배포할 수 있도록 하는 선택 네트워킹 파트너와 협력 하 고 있습니다. 이렇게 하면 전용 종단 간 SD WAN 기능을 활용할 수 있도록 분기 CPE를 가상 허브의 동일한 브랜드 NVA에 연결할 수 있습니다.

Barracuda Networks는 [Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan) 제품을 사용 하 여 가상 WAN 허브에 직접 배포할 수 있는 nva 제품을 제공 하는 최초의 파트너입니다. Azure에서 더 많은 파트너와 협력 하 여 다른 제품을 확인 해야 합니다.

> [!NOTE]
> 가상 WAN 허브에 배포할 수 있는 NVA 제안은 가상 WAN 허브에 배포할 수 있습니다. Azure의 임의의 가상 네트워크에 배포할 수 없습니다.

## <a name="how-does-it-work"></a><a name="how"></a>작동 원리

Azure 가상 WAN 허브에 직접 배포할 수 있는 Nva는 가상 허브에서 사용 하도록 설계 되었습니다. NVA 제품은 Azure Marketplace 관리 되는 응용 프로그램으로 게시 되며, 고객은 Azure Marketplace에서 직접 제품을 배포 하거나 Azure Portal를 통해 가상 허브에서 제품을 배포할 수 있습니다.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="프로세스 개요":::

각 파트너의 NVA 제품은 배포 요구 사항에 따라 약간 다른 환경과 기능을 제공 합니다. 그러나 가상 WAN 허브에서 NVA에 대 한 모든 파트너 제품에 공통적인 몇 가지 사항이 있습니다.

* Azure Marketplace를 통해 제공 되는 관리 되는 응용 프로그램 환경입니다.
* NVA 인프라 단위 기반 용량 및 청구.
* 상태 메트릭은 Azure Monitor를 통해 표시 됩니다.

### <a name="managed-application"></a><a name="managed"></a>관리형 애플리케이션

가상 WAN 허브에 배포할 수 있는 모든 NVA 제품에는 Azure Marketplace에서 사용할 수 있는 **관리 되는 응용 프로그램이** 있습니다. 관리 되는 응용 프로그램에서 파트너는 다음을 수행할 수 있습니다.

* NVA에 대 한 사용자 지정 배포 환경을 구축 합니다.
* 가상 WAN 허브에서 NVA를 직접 만들 수 있도록 하는 특수 한 리소스 관리자 템플릿을 제공 합니다.
* 소프트웨어 라이선스 비용을 직접 청구 하거나 Azure Marketplace를 통해 청구 합니다.
* 사용자 지정 속성 및 리소스 측정기를 표시 합니다.

NVA 파트너는 어플라이언스 배포, 구성 라이선스 및 관리 요구 사항에 따라 다른 리소스를 만들 수 있습니다. 고객이 모든 관리 되는 응용 프로그램과 같이 가상 WAN 허브에서 NVA를 만들 때 구독에 두 개의 리소스 그룹이 생성 됩니다.

* **Customer 리소스 그룹** -관리 되는 응용 프로그램에 대 한 응용 프로그램 자리 표시자를 포함 합니다. 파트너는이를 사용 하 여 여기에서 선택 하는 모든 고객 속성을 노출할 수 있습니다.
* **관리 되는 리소스 그룹** -이 리소스 그룹의 리소스를 직접 구성 하거나 변경할 수 없습니다 .이는 관리 되는 응용 프로그램의 게시자가 제어 하기 때문입니다. 이 리소스 그룹은 **Networkvirtualappliances** 리소스를 포함 합니다.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="프로세스 개요":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 인프라 단위

가상 WAN 허브에서 NVA를 만들 때 배포 하려는 NVA 인프라 단위의 수를 선택 해야 합니다. **Nva 인프라 단위** 는 가상 WAN 허브의 nva에 대 한 집계 대역폭 용량 단위입니다. **Nva 인프라 단위** 는 용량 및 크기 조정에 대해 생각 하는 방법 측면에서 VPN [배율 단위](pricing-concepts.md#scale-unit) 와 비슷합니다.

* 1 NVA 인프라 단위는이 NVA로 들어오는 모든 지점 사이트 연결에 대 한 500 Mbps의 집계 대역폭을 $0.25/시간 단위로 나타냅니다.
* Azure는 지정 된 NVA 가상 허브 배포에 대해 1-80 NVA 인프라 단위를 지원 합니다.
* 각 파트너는 지원 되는 모든 NVA 인프라 단위 구성의 하위 집합인 다른 NVA 인프라 단위 번들을 제공할 수 있습니다.

VPN 배율 단위와 유사 하 게 *1 NVA Infrastructure Unit = 500 Mbps*를 선택 하는 경우 중복성에 대 한 두 개의 인스턴스가 생성 되며 각각은 최대 500 Mbps의 처리량이 있습니다. 예를 들어, 각각 10Mbps를 수행하는 5개의 분기가 있는 경우 헤드 끝에서 50Mbps로 집계되어야 합니다. 허브에 대 한 분기 수를 지 원하는 데 필요한 용량을 평가한 후 NVA의 집계 용량 계획을 수행 해야 합니다.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>네트워크 가상 어플라이언스 구성 프로세스

파트너는 배포 프로세스의 일부로 NVA를 자동으로 구성 하는 환경을 제공 하기 위해 노력 했습니다. NVA가 가상 허브로 프로 비전 되 면 nva에 필요할 수 있는 추가 구성은 NVA 파트너 포털 또는 관리 응용 프로그램을 통해 수행 해야 합니다. NVA에 대 한 직접 액세스를 사용할 수 없습니다.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Nva를 사용 하는 사이트 및 연결 리소스

Azure VPN Gateway 구성과 달리 **사이트** 리소스, **사이트 간 연결** 리소스 또는 **지점 및 사이트 간 연결** 리소스를 만들어 가상 WAN 허브의 nva에 분기 사이트를 연결할 필요가 없습니다. 이는 모두 NVA 파트너를 통해 관리 됩니다.

가상 WAN 허브를 Azure 가상 네트워크에 연결 하려면 허브-VNet 연결을 만들어야 합니다.

## <a name="supported-regions"></a><a name="regions"></a>지원되는 지역

가상 허브의 NVA는 다음 지역에서 미리 보기로 제공 됩니다.

|지역 | Azure 지역|
|---|---|
| 북아메리카| 미국 서 부, 미국 중부, 미국 동부 2   |
| 남아메리카 | 브라질 남부 |
| 유럽 | 유럽 서부, 영국 남부|
|  중동 | 아랍에미리트 북부 |
| 아시아 | 일본 동부 |
| 오스트레일리아 | 오스트레일리아 동부 |

## <a name="faq"></a>FAQ

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>네트워크 어플라이언스 파트너 이며 허브에서 NVA를 받고 싶습니다.  이 파트너 프로그램에 가입할 수 있나요?

아쉽게도이 시점에는 새로운 파트너 제품을 온보드 할 수 있는 용량이 없습니다. 11 월에 다시 문의 하세요.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Azure Marketplace에서 가상 WAN 허브로 NVA를 배포할 수 있나요?

아니요. 이번에는 [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer) 만 가상 WAN 허브에 배포할 수 있습니다.

### <a name="what-is-the-cost-of-the-nva"></a>NVA의 비용은 얼마 인가요?

Barracuda에서 Barracuda CloudGen WAN NVA에 대 한 라이선스를 구입 해야 합니다. 라이선스에 대 한 자세한 내용은 [Barracuda의 CloudGen WAN 페이지](https://www.barracuda.com/products/cloudgenwan)를 참조 하세요. 또한 사용 하는 NVA 인프라 단위와 사용 하는 기타 리소스에 대 한 Microsoft의 요금이 부과 됩니다. 자세한 내용은 [가격 책정 개념](pricing-concepts.md)을 참조 하세요.

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>NVA를 기본 허브에 배포할 수 있나요?

아니요. NVA를 배포 하려는 경우 표준 허브를 사용 해야 합니다.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>NVA를 보안 허브에 배포할 수 있나요?

예. Barracuda CloudGen WAN은 Azure 방화벽을 사용 하 여 허브에 배포할 수 있습니다.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>허브에서 Barracuda CloudGen WAN NVA에 지점의 모든 CPE 장치를 연결할 수 있나요?

아니요. Barracuda CloudGen WAN은 Barracuda CPE 장치와만 호환 됩니다. CloudGen WAN 요구 사항에 대해 자세히 알아보려면 [Barracuda의 CloudGen wan 페이지](https://www.barracuda.com/products/cloudgenwan)를 참조 하세요.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>허브에서 NVA로 지원 되는 라우팅 시나리오는 무엇입니까?

가상 WAN에서 지원 되는 모든 라우팅 시나리오는 허브의 Nva에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 Wan 개요](virtual-wan-about.md) 문서를 참조 하세요.
---
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/02/2021
ms.author: cherylmc
ms.openlocfilehash: cf11d4614cd4b6c3c387ef83b63da506a1702afc
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411924"
---
### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub-can-i-join-this-partner-program"></a>저는 네트워크 어플라이언스 파트너이고 허브에서 NVA를 받고 싶습니다. 이 파트너 프로그램에 가입할 수 있나요?

아쉽게도 현재는 새로운 파트너 제품을 온보드할 용량이 없습니다. 나중에 다시 확인하세요!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Azure Marketplace에서 Virtual WAN 허브로 NVA를 배포할 수 있나요?

현재로서는 [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer), [Cisco Cloud vWAN 애플리케이션](https://azuremarketplace.microsoft.com/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) 및 [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink)만 Virtual WAN 허브에 배포할 수 있습니다.

### <a name="what-is-the-cost-of-the-nva"></a>NVA 비용은 얼마인가요?

NVA 라이선스는 NVA 공급업체에서 구입해야 합니다. Barracuda 라이선스의 Barracuda CloudGen WAN NVA는 [Barracuda의 CloudGen WAN 페이지](https://www.barracuda.com/products/cloudgenwan)를 참조하세요. Cisco는 현재 Cisco로부터 직접 확보해야 하는 BYOL(사용자 라이선스 필요) 라이선스 모델만 제공합니다. 아울러, 사용하는 NVA 인프라 단위와 사용하는 기타 리소스에 대한 요금이 Microsoft로부터 부과됩니다. 자세한 내용은 [가격 책정 개념](../articles/virtual-wan/pricing-concepts.md)을 참조하세요.

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>NVA를 기본 허브에 배포할 수 있나요?

아니요. NVA를 배포하려는 경우 표준 허브를 사용해야 합니다.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>NVA를 보안 허브에 배포할 수 있나요?

예. 파트너 NVA는 Azure Firewall을 사용하여 허브에 배포할 수 있습니다.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>지점에 있는 임의의 CPE 디바이스를 허브의 Barracuda CloudGen WAN NVA에 연결할 수 있나요?

아니요. Barracuda CloudGen WAN은 Barracuda CPE 디바이스만 호환됩니다. 자세한 CloudGen WAN 요구 사항은 [Barracuda의 CloudGen WAN 페이지](https://www.barracuda.com/products/cloudgenwan)를 참조하세요. Cisco의 경우 호환되는 여러 SD-WAN CPE 디바이스가 있습니다. 호환되는 CPE는 [다중 클라우드를 위한 Cisco Cloud OnRamp](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 설명서를 참조하세요.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>허브의 NVA로 지원되는 라우팅 시나리오는 무엇입니까?

Virtual WAN에서 지원하는 모든 라우팅 시나리오가 허브의 NVA로 지원됩니다.

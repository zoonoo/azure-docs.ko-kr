---
title: ExpressRoute 및 Azure VPN에 대한 Azure Route Server(미리 보기) 지원
description: Azure Route Server가 ExpressRoute 및 Azure VPN 게이트웨이와 상호작용하는 방법에 대하여 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679969"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>ExpressRoute 및 Azure VPN에 대한 Azure Route Server(미리 보기) 지원

Azure Route Server는 Azure에서 실행되는 타사 NVA(네트워크 가상 어플라이언스)를 지원할 뿐 아니라 ExpressRoute 및 Azure VPN 게이트웨이와도 원활하게 통합합니다. 게이트웨이와 Azure Route Server 간의 BGP 피어링을 구성하거나 관리할 필요가 없습니다. 간단한 [구성 변경](quickstart-configure-route-server-powershell.md#route-exchange)을 통하여 게이트웨이와 Azure Route Server 사이의 경로 교환을 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-it-work"></a>작동 원리

가상 네트워크에서 Azure Route Server를 ExpressRoute 게이트웨이 및 NVA와 함께 배포하는 경우, Azure Route Server는 NVA에서 수신한 경로를 ExpressRoute 게이트웨이에 전파하지 않고, ExpressRoute 게이트웨이에서 수신한 경로를 NVA에 전파하지 않는 것이 기본 설정입니다. 경로 교환을 사용하도록 설정하면 ExpressRoute와 NVA는 상대측의 경로를 확인합니다.

다음 다이어그램을 통하여 일례를 살펴봅시다.

* SDWAN 어플라이언스는 Azure Route Server에서 “On-prem 2”로부터의 경로를 수신하며, 해당 경로는 가상 네트워크 경로와 함께 ExpressRoute에 연결됩니다.

* ExpressRoute 게이트웨이는 “On-prem 1”로부터의 경로를 수신하는데, 이는 Azure Route Server의 가상 네트워크 경로와 함께 SDWAN 어플라이언스에도 연결되는 것입니다.

    ![Route Server로 구성된 ExpressRoute를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-with-route-server.png)

SDWAN 어플라이언스를 Azure VPN 게이트웨이로 대체할 수도 있습니다. Azure VPN 게이트웨이와 ExpressRoute 모두 완전 관리형이므로, 양쪽의 온-프레미스 네트워크가 서로 소통하도록 경로 교환을 설정하기만 하면 됩니다.

> [!IMPORTANT] 
> Azure VPN 게이트웨이는 반드시 [**활성-활성**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) 모드로 구성되어야 합니다.
>

![Route Server로 구성된 ExpressRoute와 VPN 게이트웨이를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>다음 단계

- [Azure Route Server](route-server-faq.md)에 대한 자세한 정보 알아보기
- [Azure Route Server 구성](quickstart-configure-route-server-powershell.md) 방법 알아보기
- [Azure ExpressRoute 및 Azure VPN 공존](../expressroute/expressroute-howto-coexist-resource-manager.md)에 대하여 자세한 정보 알아보기

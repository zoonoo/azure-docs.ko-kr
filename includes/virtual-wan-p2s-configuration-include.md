---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048263"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. **모든 리소스** 로 이동하고 생성한 가상 WAN을 선택한 다음, 왼쪽 메뉴에서 **사용자 VPN 구성** 을 선택합니다.
1. **사용자 VPN 구성** 페이지에서 페이지 상단의 **+사용자 VPN 구성 만들기** 를 선택하여 **새 사용자 VPN 구성 만들기** 페이지를 엽니다.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="사용자 VPN 구성 페이지의 스크린샷.":::

1. **기본** 탭의 **인스턴스 세부 정보** 에서 VPN 구성에 할당할 **이름** 을 입력합니다.
1. **터널 유형** 의 드롭다운 목록에서 원하는 터널 유형을 선택합니다. 터널 유형 옵션에는 **IKEv2 VPN**, **OpenVPN**, **OpenVpn 및 IkeV2** 가 있습니다.
1. 선택한 터널 유형에 해당하는 다음 단계를 사용합니다. 모든 값을 지정한 후 **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭하여 구성을 만듭니다.

   **IKEv2 VPN**

   * **요구 사항:** **IKEv2** 터널 유형을 선택하면 인증 방법을 선택하도록 지시하는 메시지가 표시됩니다. IKEv2의 경우 인증 방법을 하나만 지정할 수 있습니다. Azure 인증서, Azure Active Directory 또는 RADIUS 기반 인증을 선택할 수 있습니다.

   * **IPSec 사용자 지정 매개 변수:** IKE 1단계 및 IKE 2단계의 매개 변수를 사용자 정의하려면 IPsec 스위치를 **사용자 정의** 로 전환하고 매개 변수 값을 선택합니다. 사용자 지정 가능한 매개 변수에 대한 자세한 내용은 [사용자 지정 IPsec](../articles/virtual-wan/point-to-site-ipsec.md) 문서를 참조하세요.

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="사용자 지정으로 전환한 IPsec 스위치의 스크린샷.":::

   * **인증:** 페이지 하단의 **다음** 을 클릭하여 인증 방법으로 이동하거나 페이지 상단에서 적절한 탭을 클릭하여 사용하려는 인증 메커니즘으로 이동합니다. 방법을 선택하려면 스위치를 **예** 로 전환합니다.

     이 예에서는 RADIUS 인증을 선택합니다. RADIUS 기반 인증의 경우 보조 RADIUS 서버 IP 주소와 서버 비밀을 제공할 수 있습니다.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="IKE의 스크린샷.":::

   **OpenVPN**

   * **요구 사항:** **OpenVPN** 터널 유형을 선택하면 인증 메커니즘을 선택하도록 지시하는 메시지가 표시됩니다. OpenVPN을 터널 유형으로 선택한 경우 여러 인증 방법을 지정할 수 있습니다. Azure 인증서, Azure Active Directory 또는 RADIUS 기반 인증의 하위 집합을 선택할 수 있습니다. RADIUS 기반 인증의 경우 보조 RADIUS 서버 IP 주소와 서버 비밀을 제공할 수 있습니다.

   * **인증:** 페이지 하단의 **다음** 을 클릭하여 인증 방법으로 이동하거나 페이지 상단에서 적절한 탭을 클릭하여 사용하려는 인증 방법으로 이동합니다.
   선택하려는 각 방법에서 **예** 로 전환하고 적절한 값을 입력합니다.

     이 예제에서는 Azure Active Directory를 선택합니다.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="OpenVPN 페이지의 스크린샷.":::

---
title: 'Azure 가상 WAN: 허브에서 NVA (네트워크 가상 어플라이언스) 만들기'
description: 이 자습서에서는 가상 WAN 허브에 네트워크 가상 어플라이언스를 배포 하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 83267b1bebd501871277ea3e40b7fa9ba38f33cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313759"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Azure 가상 WAN 허브 (미리 보기)에서 네트워크 가상 어플라이언스를 만드는 방법

이 자습서에서는 Azure에서 nva ( **네트워크 가상 어플라이언스** )를 통해 가상 WAN을 사용 하 여 azure의 리소스에 연결 하는 방법을 보여 줍니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 문서의 단계를 통해 가상 WAN 허브에 **Barracuda CloudGen WAN** 네트워크 가상 어플라이언스를 만들 수 있습니다. 이 연습을 완료 하려면 시작 하기 전에 허브에 배포 하는 Barracuda CloudGen WAN 어플라이언스에 대 한 Barracuda 클라우드 프레미스 장치 (CPE) 및 라이선스가 있어야 합니다.

Azure 가상 WAN 내에서 **CISCO SD-WAN** 에 대 한 배포 설명서는 다음 전자 메일 주소에서 cisco로 전자 메일을 보내 주세요 vwan_public_preview@cisco.com .


## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Barracuda CloudGen WAN 게이트웨이에 대 한 라이선스를 얻습니다. 이 작업을 수행 하는 방법에 대해 자세히 알아보려면 [Barracuda CloudGen WAN 설명서](https://www.barracuda.com/products/cloudgenwan) 를 참조 하세요.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtual WAN 만들기

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>허브 만들기

허브는 사이트 간, Express 경로, 지점 및 사이트 간 또는 네트워크 가상 어플라이언스 기능을 위한 게이트웨이를 포함할 수 있는 가상 네트워크입니다. 허브가 생성되면 사이트를 연결하지 않아도 허브 요금이 청구됩니다. 사이트 간 VPN gateway를 만들도록 선택 하는 경우 가상 허브에서 사이트 간 VPN 게이트웨이를 만드는 데 30 분이 걸립니다. 사이트 간, Express 경로 또는 지점 및 사이트 간 연결과는 달리 허브 VNet에 네트워크 가상 어플라이언스를 배포 하기 전에 먼저 허브를 만들어야 합니다.

1. 생성한 Virtual WAN을 찾습니다. **가상 WAN** 페이지의 **연결** 섹션에서 **허브**를 선택 합니다.
1. **허브** 페이지에서 + 새 허브를 선택 하 여 **가상 허브 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="기본 사항":::
1. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   **프로젝트 세부 정보**

   * 지역(이전에는 위치라고 했음)
   * Name
   * 허브 프라이빗 주소 공간. 허브를 만들기 위한 최소 주소 공간은 /24이며 이것은 /25부터 /32까지의 모든 범위가 생성 중에 오류가 발생함을 의미합니다. Microsoft에서 관리 하는 서비스인 Azure 가상 WAN은 다른 게이트웨이/서비스에 대해 가상 허브에 적절 한 서브넷을 만듭니다. (예: 네트워크 가상 어플라이언스, VPN gateway, Express 경로 게이트웨이, 사용자 VPN/지점 및 사이트 간 게이트웨이, 방화벽, 라우팅 등). Microsoft는이를 서비스의 일부로 사용 하기 때문에 사용자가 가상 허브의 서비스에 대 한 서브넷 주소 공간을 명시적으로 계획할 필요가 없습니다.
1. **검토 + 만들기**를 선택하여 유효한지 확인합니다.
1. **만들기**를 선택하여 허브를 만듭니다.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>허브에서 네트워크 가상 어플라이언스 만들기

이 단계에서는 허브에 네트워크 가상 어플라이언스를 만듭니다. 각 NVA에 대 한 절차는 NVA 파트너의 제품 마다 다릅니다. 이 예에서는 Barracuda CloudGen WAN 게이트웨이를 만듭니다.

1. 이전 단계에서 만든 가상 WAN 허브를 찾아 엽니다.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="가상 허브":::
1. 네트워크 가상 어플라이언스 타일을 찾고 **만들기** 링크를 선택 합니다.
1. **네트워크 가상 어플라이언스** 블레이드에서 **BARRACUDA cloudgen WAN**을 선택 하 고 **만들기** 단추를 선택 합니다.

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA를 선택 합니다.":::
1. 그러면 Barracuda CloudGen WAN 게이트웨이의 Azure Marketplace 제품으로 이동 됩니다. 용어를 읽은 다음 준비가 되 면 **만들기** 단추를 선택 합니다.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA 기본 사항":::
1. **기본 사항** 페이지에서 다음 정보를 제공 해야 합니다.

   * **구독** -가상 WAN 및 허브를 배포 하는 데 사용 하는 구독을 선택 합니다.
   * **리소스 그룹** -가상 WAN 및 허브를 배포 하는 데 사용한 것과 동일한 리소스 그룹을 선택 합니다.
   * **지역** -가상 허브 리소스가 있는 동일한 지역을 선택 합니다.
   * **응용 프로그램 이름** -Barracuda NEXTGEN WAN은 관리 되는 응용 프로그램입니다. 이 리소스를 쉽게 식별할 수 있도록 하는 이름을 선택 합니다 .이는 구독에 표시 될 때 호출 됩니다.
   * **관리 되는 리소스 그룹** -Barracuda에서 관리 하는 리소스를 배포 하는 관리 되는 리소스 그룹의 이름입니다. 이에 대 한 이름은 미리 채워야 합니다.
1. **다음: CloudGen WAN 게이트웨이** 단추를 선택 합니다.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN 게이트웨이":::
1. 여기에서 다음 정보를 제공 합니다.

   * **가상 Wan 허브** -이 nva를 배포 하려는 가상 wan 허브입니다.
   * **Nva 인프라 단위** -이 nva를 배포 하려는 Nva 인프라 단위의 수를 표시 합니다. 이 NVA를 통해이 허브에 연결 되는 모든 분기 사이트에서 제공할 집계 대역폭 용량 크기를 선택 합니다.
   * **Token** Barracuda는이 제품의 등록 된 사용자로 서 사용자를 식별 하기 위해 여기에 인증 토큰을 제공 해야 합니다. Barracuda에서이를 가져와야 합니다.
1. **검토 및 만들기** 단추를 선택 하 여 계속 합니다.
1. 이 페이지에서는 공동 관리자 액세스 계약의 약관에 동의 하 라는 메시지가 표시 됩니다. 게시자가이 배포의 일부 리소스에 액세스할 수 있는 관리 되는 응용 프로그램의 표준입니다. 위의 사용 **약관에 동의 함** 확인란을 선택 하 고 **만들기**를 선택 합니다.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>허브에 VNet 연결

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>다음 단계

* 가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
* 가상 WAN 허브의 Nva에 대해 자세히 알아보려면 [가상 wan 허브 (미리 보기)의 네트워크 가상 어플라이언스 정보](about-nva-hub.md)를 참조 하세요.

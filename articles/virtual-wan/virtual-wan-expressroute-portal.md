---
title: Azure Virtual WAN을 사용하여 Azure 및 온-프레미스 환경에 대한 ExpressRoute 연결 생성 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure 및 온-프레미스 환경에 대한 ExpressRoute 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 8ad86280eab3041667bf9d1713ae2b4bc82a4c9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491525"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용한 ExpressRoute 연결 만들기

이 자습서에서는 Virtual WAN을 사용하여 ExpressRoute 회로를 통해 Azure에 있는 리소스에 연결하는 방법을 보여줍니다. Virtual WAN 및 Virtual WAN 리소스에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 만들기
> * 허브 및 게이트웨이 만들기
> * 허브에 VNet 연결
> * 허브 게이트웨이에 회로 연결
> * 연결 테스트
> * 게이트웨이 크기 변경
> * 기본 경로 알리기

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="openvwan"></a>Virtual WAN 만들기

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

1. Virtual WAN 페이지로 이동합니다. 포털에서 **+리소스 만들기**를 클릭합니다. 검색 상자에 **Virtual WAN**을 입력하고 Enter를 선택합니다.
2. 결과에서 **Virtual WAN**을 선택합니다. Virtual WAN 페이지에서 **만들기**를 클릭하여 WAN 만들기 페이지를 엽니다.
3. **WAN 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 입력합니다.

   ![WAN 만들기](./media/virtual-wan-expressroute-portal/createwan.png)

   * **구독** - 사용할 Azure 구독을 선택합니다.
   * **리소스 그룹** - 새로 만들거나 기존 항목을 사용합니다.
   * **리소스 그룹 위치** - 드롭다운에서 리소스 위치를 선택합니다. WAN은 전역 리소스이며 특정 지역에 상주하지 않습니다. 하지만 만든 WAN 리소스를 보다 쉽게 관리하고 찾으려면 지역을 선택해야 합니다.
   * **이름** - WAN을 호출할 이름을 입력합니다.
   * **유형** - **표준**을 선택합니다. 기본 SKU를 사용해서는 ExpressRoute 게이트웨이를 만들 수 없습니다.
4. 필드 작성을 완료한 후 **검토 + 만들기**를 선택합니다.
5. 유효성 검사를 통과하면 **만들기**를 선택하여 Virtual WAN을 만듭니다.

## <a name="hub"></a>가상 허브 및 게이트웨이 만들기

가상 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 여기에는 VPN, ExpressRoute와 같은 다양한 게이트웨이가 포함될 수 있습니다. 이 섹션에서는 가상 허브를 위한 ExpressRoute 게이트웨이를 만듭니다. [새 가상 허브 만들기](#newhub)를 할 때 게이트웨이를 만들거나 [기존 허브](#existinghub)를 편집하여 게이트웨이를 만들 수 있습니다. 

ExpressRoute 게이트웨이는 2Gbps 단위로 프로비저닝됩니다. 1 배율 단위는 2Gbps이며 최대 10개의 배율 단위인 20Gbps까지 지원됩니다. 가상 허브와 게이트웨이를 완전히 만드는 데 30분 정도 걸립니다.

### <a name="newhub"></a>새 가상 허브 및 게이트웨이를 만들려면

새 가상 허브를 만듭니다. 허브가 생성되면 사이트를 연결하지 않아도 허브 요금이 청구됩니다.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>기존 허브에서 게이트웨이를 만들려면

기존 허브를 편집해서도 게이트웨이를 만들 수 있습니다.

1. 편집하려는 가상 허브로 이동하여 선택합니다.
2. **가상 허브 편집** 페이지에서 **ExpressRoute 게이트웨이 포함** 확인란을 선택합니다.
3. **확인**을 선택하여 변경 내용을 확인합니다. 허브와 허브 리소스를 완전히 만드는 데 30분 정도 걸립니다.

   ![기존 허브](./media/virtual-wan-expressroute-portal/edithub.png "허브 편집")

### <a name="to-view-a-gateway"></a>게이트웨이를 보려면

ExpressRoute 게이트웨이를 만들었으면 게이트웨이 세부 정보를 볼 수 있습니다. 허브로 이동하여 **ExpressRoute**를 선택하고 게이트웨이를 봅니다.

![게이트웨이 보기](./media/virtual-wan-expressroute-portal/viewgw.png "게이트웨이 보기")

## <a name="connectvnet"></a>VNet을 허브에 연결

이 섹션에서는 허브와 VNet 간의 피어링 연결을 만듭니다. 연결하려는 각 VNet에 대해 이 단계를 반복합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존 가상 네트워크 게이트웨이(VPN 또는 ExpressRoute)가 있을 수 없습니다.

## <a name="connectcircuit"></a>회로를 허브 게이트웨이에 연결

게이트웨이가 생성되면 [ExpressRoute 회로](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)에 연결할 수 있습니다. ExpressRoute Global Reach를 지원하는 위치에 있는 ExpressRoute 프리미엄 회로는 Virtual WAN ExpressRoute 게이트웨이에 연결할 수 있습니다.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>회로를 허브 게이트웨이에 연결하려면

포털에서 **가상 허브 -> 연결 -> ExpressRoute** 페이지로 이동합니다. ExpressRoute 회로에 대한 구독에 액세스할 수 있으면 회로 목록에서 사용할 회로를 볼 수 있습니다. 회로를 볼 수 없어도 인증 키와 피어 회로 URI가 있으면 회로를 사용하고 연결할 수 있습니다. [인증 키를 사용하여 연결하려면](#authkey)을 참조하세요.

1. 회로를 선택합니다.
2. **회로 연결**을 선택합니다.

   ![회로 연결](./media/virtual-wan-expressroute-portal/cktconnect.png "회로 연결")

### <a name="authkey">인증 키를 사용하여 연결하려면</a>

연결하기 위해 제공된 인증 키와 회로 URI를 사용합니다.

1. ExpressRoute 페이지에서 **+인증 키 사용**을 클릭합니다.

   ![사용](./media/virtual-wan-expressroute-portal/redeem.png "사용")
2. 인증 키 사용 페이지에 값을 입력합니다.

   ![키 값 사용](./media/virtual-wan-expressroute-portal/redeemkey2.png "키 값 사용")
3. **추가**를 선택하여 키를 추가합니다.
4. 회로를 봅니다. 사용된 회로는 사용자의 구독과 다른 구독에 있으므로 유형, 공급자, 기타 정보 없이 이름만 표시됩니다.

## <a name="to-test-connectivity"></a>연결 테스트

회로에 연결된 후 허브 연결 상태는 ‘이 허브’로 표시됩니다. 이는 허브 ExpressRoute 게이트웨이에 연결되었음을 나타냅니다. ExpressRoute 회로 뒤의 클라이언트, 예를 들어 앞서 만든 VNet의 VM에서의 연결을 테스트 하기 전에 5분 정도 기다립니다.

ExpressRoute 게이트웨이와 동일한 허브의 Virtual WAN VPN Gateway에 연결된 사이트가 있다면 VPN과 ExpressRoute 엔드포인트 간의 양방향 연결을 사용할 수 있습니다. 동적 라우팅(BGP)이 지원됩니다. 허브에 있는 게이트웨이의 ASN은 고정되어 있어 지금은 편집할 수 없습니다.

## <a name="to-change-the-size-of-a-gateway"></a>게이트웨이의 크기를 변경하려면

ExpressRoute 게이트웨이의 크기를 변경하려면 허브 내에서 ExpressRoute 게이트웨이를 찾고 드롭다운에서 배율 단위를 선택합니다. 변경 내용을 저장합니다. 허브 게이트웨이를 업데이트하는 데 30분 정도 걸립니다.

![게이트웨이 크기 변경](./media/virtual-wan-expressroute-portal/changescale.png "게이트웨이 크기 변경")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>엔드포인트에 기본 경로 0.0.0.0/0을 알리려면

Azure 가상 허브가 기본 경로 0.0.0.0/0을 ExpressRoute 엔드포인트에 알리게 하려면 ‘기본 경로 전파’를 사용하도록 설정해야 합니다.

1. **회로 ->…-> 연결 편집**을 선택합니다.

   ![연결 편집](./media/virtual-wan-expressroute-portal/defaultroute1.png "연결 편집")

2. **사용**을 선택하여 기본 경로를 전파하도록 합니다.

   ![기본 경로 전파](./media/virtual-wan-expressroute-portal/defaultroute2.png "기본 경로 전파")

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
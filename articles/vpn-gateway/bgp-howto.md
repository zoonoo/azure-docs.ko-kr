---
title: 'VPN Gateway에 대 한 BGP 구성: 포털'
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 PowerShell을 사용 하 여 Azure VPN Gateway로 BGP를 구성 하는 단계를 안내 합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: f52d684d1e6ef63fdf4287c610608061f30395f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996868"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Azure VPN gateway에서 BGP를 구성 하는 방법

이 문서에서는 Azure Portal를 사용 하 여 크로스-프레미스 S2S (사이트 간) VPN 연결 및 VNet 간 연결에서 BGP를 사용 하도록 설정 하는 단계를 안내 합니다.

## <a name="about-bgp"></a><a name="about"></a>BGP 정보

BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. BGP를 통해 Azure VPN gateway 및 온-프레미스 VPN 장치 (BGP 피어 또는 인접 이라고 함)는 관련 된 게이트웨이 또는 라우터를 통과 하는 해당 접두사에 대 한 가용성 및 연결 가능성에 대 한 게이트웨이를 모두 알려 주는 "경로"를 교환할 수 있습니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여 BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다.

Bgp의 이점에 대 한 자세한 내용 및 BGP 사용의 기술 요구 사항 및 고려 사항에 대 한 자세한 내용은 [AZURE VPN gateway를 사용 하는 Bgp 개요](vpn-gateway-bgp-overview.md)를 참조 하세요.

## <a name="getting-started"></a>시작

이 문서의 각 부분에서는 네트워크 연결에서 BGP를 사용 하도록 설정 하기 위한 기본 구성 요소를 구성 하는 방법을 설명 합니다. 세 부분을 모두 완료 하는 경우 다이어그램 1에 표시 된 대로 토폴로지를 빌드합니다.

**다이어그램 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램" border="false":::

파트를 결합하여 필요에 따라 더 복잡한 다중 홉 전송 네트워크를 빌드할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>1 부: 가상 네트워크 게이트웨이에서 BGP 구성

이 섹션에서는 가상 네트워크를 만들고 구성 하 고, BGP 매개 변수를 사용 하 여 가상 네트워크 게이트웨이를 만들고 구성 하 고, Azure BGP 피어 IP 주소를 가져옵니다. 다이어그램 2는이 섹션의 단계를 수행할 때 사용할 구성 설정을 보여 줍니다.

**다이어그램 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. TestVNet1 만들기 및 구성

이 단계에서는 TestVNet1을 만들고 구성 합니다. [게이트웨이 만들기 자습서](vpn-gateway-tutorial-create-gateway-powershell.md) 의 단계를 사용 하 여 Azure 가상 네트워크 및 VPN gateway를 만들고 구성 합니다. 아래 스크린샷에서 참조 설정을 사용 합니다.

* 가상 네트워크:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

* 서브넷:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. BGP 매개 변수를 사용 하 여 TestVNet1에 대 한 VPN 게이트웨이 만들기

이 단계에서는 해당 BGP 매개 변수를 사용 하 여 VPN gateway를 만듭니다.

1. Azure Portal에서 Marketplace의 **Virtual Network 게이트웨이** 리소스로 이동 하 고 **만들기**를 선택 합니다.

1. 아래와 같이 매개 변수를 입력 합니다.

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

1. 페이지의 선택 된 **BGP 구성** 섹션에서 다음 설정을 구성 합니다.

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

   * Bgp **Configure BGP**  -  구성 섹션을 표시 하려면 bgp**사용** 구성을 선택 합니다.

   * ASN (자치 시스템 번호)을 입력 합니다.

   * **AZURE APIPA BGP IP 주소** 필드는 선택 사항입니다. 온-프레미스 VPN 장치에서 BGP에 대해 APIPA 주소를 사용 하는 경우 VPN에 대 한 Azure 예약 APIPA 주소 범위 ( **169.254.21.0** 에서 **169.254.22.255**로)의 주소를 선택 해야 합니다. 이 예제에서는 169.254.21.11를 사용 합니다.

   * 활성-활성 VPN gateway를 만드는 경우 BGP 섹션에 추가 **두 번째 사용자 지정 AZURE APIPA BGP IP 주소가**표시 됩니다. 허용 되는 APIPA 범위 (**169.254.21.0** ~ **169.254.22.255**)의 다른 주소를 지정 합니다.

   > [!IMPORTANT]
   >
   > * 기본적으로 Azure는 Azure VPN gateway에서 Azure BGP IP 주소로 자동으로 게이트웨이 서브넷 접두사 범위에서 개인 IP 주소를 할당 합니다. 온-프레미스 VPN 장치에서 BGP IP로 APIPA 주소 (169.254.0.1 to 169.254.255.254)를 사용 하는 경우 사용자 지정 Azure APIPA BGP 주소가 필요 합니다. 해당 로컬 네트워크 게이트웨이 리소스 (온-프레미스 네트워크)에 BGP 피어 IP로 APIPA 주소가 있으면 Azure VPN Gateway에서 사용자 지정 APIPA 주소를 선택 합니다. 로컬 네트워크 게이트웨이에서 APIPA가 아닌 일반 IP 주소를 사용 하는 경우 Azure VPN Gateway은 (는) 게이트웨이 서브넷 범위에서 개인 IP 주소로 돌아갑니다.
   >
   > * APIPA BGP 주소는 온-프레미스 VPN 장치와 연결 된 모든 Azure VPN gateway 간에 겹치지 않아야 합니다.
   >

1. **검토 + 만들기** 를 선택 하 여 유효성 검사를 실행 합니다. 유효성 검사가 통과 되 면 **만들기** 를 선택 하 여 VPN gateway를 배포 합니다. 게이트웨이는 전체를 만들고 배포 하는 데 최대 45 분이 걸릴 수 있습니다. 게이트웨이의 개요 페이지에서 배포 상태를 확인할 수 있습니다.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Azure BGP 피어 IP 주소 가져오기

게이트웨이가 만들어지면 Azure VPN gateway에서 BGP 피어 IP 주소를 가져올 수 있습니다. 이러한 주소는 Azure VPN gateway를 사용 하 여 BGP 세션을 설정 하도록 온-프레미스 VPN 장치를 구성 하는 데 필요 합니다.

1. 가상 네트워크 게이트웨이 리소스로 이동 하 고 **구성** 페이지를 선택 하 여 다음 스크린샷에 표시 된 대로 BGP 구성 정보를 확인 합니다. 이 페이지에서는 azure VPN gateway의 모든 BGP 구성 정보를 볼 수 있습니다. ASN, 공용 IP 주소 및 Azure 측 (기본 및 APIPA)의 해당 BGP 피어 IP 주소입니다.

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

1. **구성** 페이지에서 다음과 같이 구성을 변경할 수 있습니다.

   * 필요한 경우 ASN 또는 APIPA BGP IP 주소를 업데이트할 수 있습니다.
   * 활성-활성 VPN 게이트웨이가 있는 경우이 페이지에는 두 번째 Azure VPN gateway 인스턴스의 공용 IP 주소, 기본 및 APIPA BGP IP 주소가 표시 됩니다.

1. 변경한 경우 **저장** 을 선택 하 여 Azure VPN gateway에 변경 내용을 커밋합니다.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>2 부: 크로스-프레미스 S2S 연결에서 BGP 구성

크로스-프레미스 연결을 설정 하려면 [사이트 간 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)에 설명 된 대로 온-프레미스 vpn 장치를 나타내는 *로컬 네트워크 게이트웨이와* 로컬 네트워크 게이트웨이와 VPN gateway를 연결 하는 *연결* 을 만들어야 합니다. 이 문서에는 BGP 구성 매개 변수를 지정 하는 데 필요한 추가 속성이 포함 되어 있습니다.

**다이어그램 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. 로컬 네트워크 게이트웨이에서 BGP를 구성 합니다.

이 단계에서는 로컬 네트워크 게이트웨이에서 BGP를 구성 합니다. 다음 스크린샷을 예로 사용 합니다. 스크린샷에는 다이어그램 3에 지정 된 매개 변수를 사용 하 여 로컬 네트워크 게이트웨이 (Site5)가 표시 됩니다.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

#### <a name="important-configuration-considerations"></a>중요 구성 고려 사항

* ASN 및 BGP 피어 IP 주소는 온-프레미스 VPN 라우터 구성과 일치 해야 합니다.
* BGP를 사용 하 여이 네트워크에 연결 하는 경우에만 **주소 공간** 을 비워 둘 수 있습니다. Azure VPN gateway는 내부적으로 BGP 피어 IP 주소의 경로를 해당 하는 IPsec 터널에 추가 합니다. Azure VPN 게이트웨이와이 특정 네트워크 간에 BGP를 사용 **하지 않는** 경우 **주소 공간**에 대 한 유효한 주소 접두사 목록을 제공 **해야 합니다** .
* 필요한 경우 필요에 따라 **APIPA ip 주소** (169.254)를 온-프레미스 BGP 피어 ip로 사용할 수 있습니다. 그러나 Azure VPN gateway에 대 한이 문서의 앞부분에서 설명한 대로 APIPA IP 주소를 지정 해야 합니다. 그렇지 않으면 BGP 세션에서이 연결에 대해 설정할 수 없습니다.
* 로컬 네트워크 게이트웨이를 만드는 동안 BGP 구성 정보를 입력 하거나, 로컬 네트워크 게이트웨이 리소스의 **구성** 페이지에서 bgp 구성을 추가 하거나 변경할 수 있습니다.

**예제**

이 예제에서는 169.254.100.1 (APIPA 주소)를 온-프레미스 BGP 피어 IP 주소로 사용 합니다.

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. BGP를 사용 하 여 S2S 연결 구성

이 단계에서는 BGP를 사용 하도록 설정 된 새 연결을 만듭니다. 이미 연결이 있고 BGP를 사용 하도록 설정 하려는 경우 [기존 연결을 업데이트할](#update)수 있습니다.

#### <a name="to-create-a-connection-with-bgp-enabled"></a>BGP를 사용 하 여 연결을 만들려면

BGP를 사용 하 여 새 연결을 만들려면 **연결 추가** 페이지에서 값을 입력 한 다음 **bgp 사용** 옵션을 선택 하 여이 연결에 대해 bgp를 사용 하도록 설정 합니다. **확인**을 선택하여 연결을 만듭니다.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>기존 연결을 업데이트 하려면

연결에서 BGP 옵션을 변경 하려면 연결 리소스의 **구성** 페이지로 이동한 다음 다음 예제에서 강조 표시 된 대로 **bgp** 옵션을 설정/해제 합니다. **저장**을 선택하여 변경 내용을 저장합니다.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>3 부: VNet 간 연결에서 BGP 구성

VNet 간 연결에서 BGP를 사용 하거나 사용 하지 않도록 설정 하는 단계는 [2 부](#crosspremises)의 S2S 단계와 동일 합니다. 연결을 만들 때 BGP를 사용 하도록 설정 하거나 기존 VNet 간 연결에 대 한 구성을 업데이트할 수 있습니다.

>[!NOTE] 
>BGP를 사용 하지 않는 VNet 간 연결에서는 두 개의 연결 된 Vnet만 통신을 제한 합니다. BGP를 사용 하도록 설정 하 여이 두 Vnet의 다른 S2S 또는 VNet 간 연결에 대 한 전송 라우팅 기능을 허용 합니다.
>

TestVNet2와 TestVNet1 간에 BGP를 사용 하지 않도록 **설정 하는**경우 컨텍스트의 경우 TestVNet2는 온-프레미스 네트워크에 대 한 경로를 알 수 없으므로 Site5에 대해 알 수 없으므로 사이트 5와 통신할 수 없습니다. 다이어그램 4와 같이 BGP를 사용 하도록 설정 하면 세 네트워크는 모두 IPsec 및 VNet 간 연결을 통해 통신할 수 있습니다.

**다이어그램 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램" border="false":::

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.

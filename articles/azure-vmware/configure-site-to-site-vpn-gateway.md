---
title: vWAN에서 Azure VMware Solution에 대한 사이트 간 VPN 구성
description: Azure VMware 솔루션에 VPN(IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: f3fbd3d9507e0203bc58494c2c1a748f1be7e585
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021419"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>vWAN에서 Azure VMware Solution에 대한 사이트 간 VPN 구성

이 문서에서는 Microsoft Azure Virtual WAN 허브에서 VPN(IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정하는 단계를 진행합니다. 해당 허브에는 Azure VMware Solution ExpressRoute 게이트웨이 및 사이트 간 VPN 게이트웨이가 포함됩니다. 이는 온-프레미스 VPN 디바이스를 Azure VMware Solution 엔드포인트와 연결합니다.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN 사이트 간 터널 아키텍처를 보여 주는 다이어그램." border="false":::

이 방법 가이드에서는 다음을 수행하게 됩니다.

- 연결된 공용 IP 주소를 사용하여 Azure Virtual WAN 허브와 VPN 게이트웨이를 만듭니다. 

- Azure ExpressRoute 게이트웨이를 만들고 Azure VMware Solution 엔드포인트를 설정합니다. 

- 정책 기반 VPN 온-프레미스 설정을 사용하도록 설정합니다. 

## <a name="prerequisites"></a>사전 요구 사항
온-프레미스 VPN 디바이스에서 종료되는 공용 IP 주소가 있어야 합니다.

## <a name="step-1-create-an-azure-virtual-wan"></a>1단계. Azure Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>2단계. Virtual WAN 허브 및 게이트웨이 만들기

>[!TIP]
>[기존 허브에서도 게이트웨이를 만들 수](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub) 있습니다.

1. 이전 단계에서 만든 Virtual WAN을 선택합니다.

1. **가상 허브 만들기** 를 선택하고 필수 필드를 입력한 후 **다음: 사이트 간** 을 선택합니다. 

   `/24`(최소)를 사용하여 서브넷을 입력합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="가상 허브 만들기 페이지를 보여 주는 스크린샷.":::

4. **사이트 간** 탭을 선택하고 **게이트웨이 배율 단위** 드롭다운에서 집계 처리량을 설정하여 사이트 간 게이트웨이를 정의합니다. 

   >[!TIP]
   >배율 단위는 중복도를 제공하기 위해 쌍으로 존재하며, 각각 500Mbps(배율 단위 1개 = 500Mbps)를 지원합니다. 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="사이트 간 세부 정보를 보여 주는 스크린샷.":::

5. **ExpressRoute** 탭을 선택하여 ExpressRoute 게이트웨이를 만듭니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="ExpressRoute 설정의 스크린샷.":::

   >[!TIP]
   >배율 단위 값은 2Gbps입니다. 

    각 허브를 만드는 데 약 30분이 걸립니다. 

## <a name="step-3-create-a-site-to-site-vpn"></a>3단계. 사이트 간 VPN 만들기

1. Azure Portal에서 이전에 만든 가상 WAN을 선택합니다.

2. 가상 허브의 **개요** 에서 **연결** > **VPN(사이트 간)**  > **새 VPN 사이트 만들기** 를 선택합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="VPN(사이트 간) 및 새 VPN 사이트 만들기가 선택된, 가상 허브에 대한 개요 페이지의 스크린샷.":::  
 
3. **기본** 탭에서 필수 필드를 입력합니다. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="새 VPN 사이트에 대한 기본 탭의 스크린샷." lightbox="media/create-ipsec-tunnel/create-vpn-site-basics2.png":::  

   1. 목록에서 **지역** 을 선택합니다.

   1. 사이트 간 VPN의 **이름** 을 제공합니다.

   1. 온-프레미스 VPN 디바이스의 **디바이스 공급업체**(예: Cisco)를 제공합니다.
   
   1. **프라이빗 주소 공간** 을 제공합니다. 온-프레미스 CIDR 블록을 사용하여 터널을 통해 온-프레미스에 바인딩된 모든 트래픽을 라우팅합니다. CIDR 블록은 [Azure VPN Gateway에서 BGP(Border Gateway Protocol)를 구성](../vpn-gateway/bgp-howto.md)하지 않는 경우에만 필요합니다.

1. **다음: 링크** 를 선택하고 필수 필드 입력을 완료합니다. 링크 및 공급자 이름을 지정하면 허브의 일부로 생성되는 게이트웨이의 수를 구분할 수 있습니다.  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 및 ASN(익명 시스템 번호)은 조직 내에서 고유해야 합니다. BGP는 Azure VMware Solution과 온-프레미스 서버 모두가 터널을 통해 경로를 보급하도록 합니다. 사용하지 않도록 설정하면 보급되어야 하는 서브넷을 수동으로 유지 관리해야 합니다. 서브넷이 누락되면 HCX에서 서비스 메시를 구성하지 못합니다. 
 
   >[!IMPORTANT]
   >기본적으로 Azure는 Azure VPN 게이트웨이에서 Azure BGP IP 주소로 자동으로 게이트웨이 서브넷 접두사 범위에서 개인 IP 주소를 할당합니다. 온-프레미스 VPN 디바이스에서 BGP IP로 APIPA 주소(169.254.0.1 ~ 169.254.255.254)를 사용하는 경우 사용자 지정 Azure APIPA BGP 주소가 필요합니다. 해당 로컬 네트워크 게이트웨이 리소스(온-프레미스 네트워크)에 BGP 피어 IP로 APIPA 주소가 있으면 Azure VPN Gateway에서 사용자 지정 APIPA 주소를 선택합니다. 로컬 네트워크 게이트웨이에서 APIPA가 아닌 일반 IP 주소를 사용하는 경우 Azure VPN Gateway는 게이트웨이 서브넷 범위에서 개인 IP 주소로 돌아갑니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="링크 세부 정보를 보여 주는 스크린샷." lightbox="media/create-ipsec-tunnel/create-vpn-site-links.png":::

1. **검토 + 만들기** 를 선택합니다. 

1. 원하는 가상 허브로 이동하고 **Hub 연결** 을 선택 취소하여 VPN 사이트를 허브에 연결합니다.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="미리 공유한 키 및 관련 설정에 대해 준비된 Virtual HUB의 연결된 사이트 창을 보여 주는 스크린샷.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>4단계. (선택 사항)정책 기반 VPN 사이트 간 터널 만들기

>[!IMPORTANT]
>이 단계는 선택 사항이며 정책 기반 VPN에만 적용됩니다. 

정책 기반 VPN을 설정하려면 허브 범위를 포함하여 온-프레미스 및 Azure VMware Solution 네트워크를 지정해야 합니다.  해당 허브 범위는 정책 기반 VPN 터널 온-프레미스 엔드포인트의 암호화 도메인을 지정합니다.  정책 기반 트래픽 선택기 표시기 사용 설정이 필요한 쪽은 Azure VMware Solution 쪽뿐입니다. 

1. Azure Portal에서 Virtual WAN 허브 사이트로 이동합니다. **연결** 에서 **VPN(사이트 간)** 을 선택합니다.

2. VPN 사이트 이름, 맨 오른쪽에 있는 줄임표(...)를 선택한 다음 **이 허브에 대한 VPN 연결을 편집** 합니다.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="이 허브에 대한 VPN 연결 편집에 액세스하도록 줄임표가 선택된 것을 보여 주는 Virtual WAN 허브 사이트에 대한 Azure 페이지의 스크린샷." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN 사이트와 허브 간의 연결을 편집하고 **저장** 을 선택합니다.

   - IPSec(인터넷 프로토콜 보안), **사용자 지정** 을 선택합니다.

   - 정책 기반 트래픽 선택기를 사용하고 **사용** 을 선택합니다.

   - **IKE 1단계** 및 **IKE 2단계(ipsec)** 에 대한 세부 정보를 지정합니다. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="VPN 연결 편집 페이지의 스크린샷."::: 
 
   정책 기반 암호화 도메인에 속하는 트래픽 선택기 또는 서브넷은 다음과 같아야 합니다.
    
   - Virtual WAN 허브 `/24`

   - Azure VMware Solution 프라이빗 클라우드 `/22`

   - 연결된 Azure 가상 네트워크(있는 경우)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>5단계. 허브에 VPN 사이트 연결

1. VPN 사이트 이름을 선택하고 **VPN 사이트 연결** 을 선택합니다. 

1. **미리 공유한 키** 필드에서 온-프레미스 엔드포인트에 대해 이전에 정의한 키를 입력합니다. 

   >[!TIP]
   >이전에 정의한 키가 없는 경우 이 필드를 비워둘 수 있습니다. 자동으로 키가 생성됩니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="미리 공유한 키 및 관련 설정에 대해 준비된 Virtual HUB의 연결된 사이트 창을 보여 주는 스크린샷."::: 

1. 허브에 방화벽을 배포 중이고 다음 홉인 경우, **기본 경로 전파** 옵션을 **사용** 으로 설정합니다. 

   사용하도록 설정하면 Virtual WAN 허브는 허브에 방화벽을 배포할 때 허브에서 기본 경로를 이미 학습한 경우나 연결된 다른 사이트에서 강제로 터널링을 사용한 경우에만 연결에 전파합니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다.  

1. **연결** 을 선택합니다. 몇 분 후에 사이트에 연결 및 연결 상태가 표시됩니다.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="사이트 간 연결 및 연결 상태를 보여 주는 스크린샷." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. 온-프레미스 엔드포인트에 대한 [VPN 구성 파일을 다운로드](../virtual-wan/virtual-wan-site-to-site-portal.md#device)합니다.  

3. Virtual WAN 허브에서 Azure VMware Solution ExpressRoute를 패치합니다. 

   >[!IMPORTANT]
   >플랫폼을 패치하려면 먼저 프라이빗 클라우드를 만들어야 합니다. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Virtual WAN 허브에서 Azure VMware Solution과 VPN 게이트웨이를 함께 연결합니다. 이전 단계의 권한 부여 키 및 ExpressRoute ID(피어 회로 URI)를 사용하게 됩니다.

   1. ExpressRoute 게이트웨이를 선택하고 **권한 부여 키 사용** 을 선택합니다.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="권한 부여 키 사용이 선택된 프라이빗 클라우드의 ExpressRoute 페이지 스크린샷.":::

   1. **권한 부여 키** 필드에 권한 부여 키를 붙여넣습니다.

   1. ExpressRoute ID를 **피어 회로 URI** 필드에 붙여넣습니다. 

   1. **이 ExpressRoute 회로를 자동으로 허브와 연결합니다** 확인란을 선택합니다. 

   1. **추가** 를 선택하여 링크를 설정합니다. 

5. [NSX-T 세그먼트를 만들고](./tutorial-nsx-t-network-segment.md) 네트워크에서 VM을 프로비저닝하여 연결을 테스트합니다. 온-프레미스 및 Azure VMware Solution 엔드포인트를 모두 ping합니다.

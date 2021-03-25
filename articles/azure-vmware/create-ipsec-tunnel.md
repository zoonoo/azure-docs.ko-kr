---
title: Azure VMware 솔루션에 대 한 IPSec 터널 만들기
description: Azure VMware 솔루션에 VPN (IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027044"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 IPSec 터널 만들기

이 문서에서는 Microsoft Azure 가상 WAN 허브에서 VPN (IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정 하는 단계를 진행 합니다. 허브에는 Azure VMware Solution Express 경로 게이트웨이 및 사이트 간 VPN gateway가 포함 됩니다. 온-프레미스 VPN 장치를 Azure VMware 솔루션 끝점과 연결 합니다.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN 사이트 간 터널 아키텍처를 보여 주는 다이어그램" border="false":::

이 방법에서 다음을 수행 합니다.
- 연결 된 공용 IP 주소를 사용 하 여 Azure 가상 WAN 허브 및 VPN gateway를 만듭니다. 
- Azure Express 경로 게이트웨이를 만들고 Azure VMware 솔루션 끝점을 설정 합니다. 
- 정책 기반 VPN 온-프레미스 설치를 사용 하도록 설정 합니다. 

## <a name="prerequisites"></a>사전 요구 사항
온-프레미스 VPN 장치에서 종료 되는 공용 IP 주소가 있어야 합니다.

## <a name="step-1-create-an-azure-virtual-wan"></a>1단계. Azure 가상 WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>2단계. 가상 WAN 허브 및 게이트웨이 만들기

>[!TIP]
>[기존 허브에서 게이트웨이를 만들](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)수도 있습니다.

1. 이전 단계에서 만든 가상 WAN을 선택 합니다.

1. **가상 허브 만들기** 를 선택 하 고 필수 필드를 입력 한 후 **다음: 사이트 간을** 선택 합니다. 

   (최소)를 사용 하 여 서브넷을 입력 `/24` 합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="가상 허브 만들기 페이지를 보여 주는 스크린샷":::

4. **사이트 간** 탭을 선택 하 고 **게이트웨이 배율 단위** 드롭다운에서 집계 처리량을 설정 하 여 사이트 간 게이트웨이를 정의 합니다. 

   >[!TIP]
   >배율 단위는 중복성을 위해 쌍으로, 각각 500 Mbps (scale unit = 500 Mbps)를 지원 합니다. 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="사이트-사이트 세부 정보를 보여 주는 스크린샷":::

5. **Express** 경로 탭을 선택 하 여 express 경로 게이트웨이를 만듭니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Express 경로 설정의 스크린샷":::

   >[!TIP]
   >배율 단위 값은 2gbps입니다. 

    각 허브를 만드는 데 약 30 분이 걸립니다. 

## <a name="step-3-create-a-site-to-site-vpn"></a>3단계. 사이트 간 VPN 만들기

1. Azure Portal에서 이전에 만든 가상 WAN을 선택 합니다.

2. 가상 허브의 **개요** 에서 **연결**  >  **VPN (사이트 간)**  >  **새 VPN 사이트 만들기** 를 선택 합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="VPN (사이트 간) 및 새 VPN 사이트 만들기가 선택 된 가상 허브에 대 한 개요 페이지의 스크린샷":::  
 
3. **기본 사항** 탭에서 필수 필드를 입력 합니다. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="새 VPN 사이트에 대 한 기본 사항 탭의 스크린샷":::  

   1. **Border Gateway Protocol** 을 **사용** 으로 설정 합니다.  사용 하도록 설정 하면 Azure VMware 솔루션과 온-프레미스 서버가 모두 터널을 통해 해당 경로를 보급 합니다. 사용 하지 않도록 설정 된 경우 보급 해야 하는 서브넷은 수동으로 유지 관리 해야 합니다. 서브넷이 누락 되 면 HCX에서 서비스 메시를 구성 하지 못합니다. 자세한 내용은  [Azure VPN Gateway를 사용 하 여 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md)를 참조 하세요.
   
   1. **개인 주소 공간** 에 대해 온-프레미스 CIDR 블록을 입력 합니다. 터널을 통해 온-프레미스에 대해 바인딩된 모든 트래픽을 라우팅하는 데 사용 됩니다. CIDR 블록은 BGP를 사용 하도록 설정 하지 않은 경우에만 필요 합니다.

1. **다음: 링크** 를 선택 하 고 필수 필드를 완료 합니다. 링크 및 공급자 이름을 지정 하면 결국 허브의 일부로 생성 될 수 있는 게이트웨이의 수를 구분할 수 있습니다. BGP 및 ASN (자치 시스템 번호)은 조직 내에서 고유 해야 합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="링크 세부 정보를 표시 하는 스크린샷":::

1. **검토 + 만들기** 를 선택합니다. 

1. 원하는 가상 허브로 이동하고 **Hub 연결** 을 선택 취소하여 VPN 사이트를 허브에 연결합니다.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="미리 공유한 키 및 연결 된 설정에 대해 준비 된 가상 허브에 대 한 연결 된 사이트 창을 보여 주는 스크린샷":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>4단계. 필드 정책 기반 VPN 사이트 간 터널 만들기

>[!IMPORTANT]
>이 단계는 선택적 단계 이며 정책 기반 Vpn에만 적용 됩니다. 

정책 기반 VPN을 설정 하려면 허브 범위를 포함 하 여 온-프레미스 및 Azure VMware Solution network를 지정 해야 합니다.  이러한 허브 범위는 온-프레미스 끝점의 정책 기반 VPN 터널의 암호화 도메인을 지정 합니다.  Azure VMware 솔루션 쪽에만 정책 기반 트래픽 선택기 표시기를 사용 하도록 설정 해야 합니다. 

1. Azure Portal에서 가상 WAN 허브 사이트로 이동 합니다. **연결** 에서 **VPN (사이트 간)** 을 선택 합니다.

2. VPN 사이트 이름, 맨 오른쪽에 있는 줄임표 (...)를 선택한 다음 **이 허브에 대 한 vpn 연결을 편집** 합니다.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="이 허브에 대 한 VPN 연결 편집에 액세스 하도록 선택 된 줄임표를 표시 하는 가상 WAN 허브 사이트에 대 한 Azure 페이지의 스크린샷" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN 사이트와 허브 간의 연결을 편집 하 고 **저장** 을 선택 합니다.
   - IPSec (인터넷 프로토콜 보안), **사용자 지정** 을 선택 합니다.
   - 정책 기반 트래픽 선택기를 사용 하 고 **사용** 을 선택 합니다.
   - **Ike 1 단계** 및 **ike 2 단계 (ipsec)** 에 대 한 세부 정보를 지정 합니다. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="VPN 연결 편집 페이지의 스크린샷"::: 
 
   정책 기반 암호화 도메인에 속하는 트래픽 선택기 또는 서브넷은 다음과 같아야 합니다.
    
   - 가상 WAN 허브 `/24`
   - Azure VMware 솔루션 사설 클라우드 `/22`
   - 연결 된 Azure virtual network (있는 경우)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>5단계. VPN 사이트를 허브에 연결

1. VPN 사이트 이름을 선택 하 고 **vpn 사이트 연결** 을 선택 합니다. 

1. **미리 공유한 키** 필드에서 온-프레미스 끝점에 대해 이전에 정의 된 키를 입력 합니다. 

   >[!TIP]
   >이전에 정의 된 키가 없는 경우에는이 필드를 비워 둘 수 있습니다. 자동으로 키가 생성 됩니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="미리 공유한 키 및 연결 된 설정에 대해 준비 된 가상 허브에 대 한 연결 된 사이트 창을 보여 주는 스크린샷 "::: 

1. 허브에 방화벽을 배포 하 고 다음 홉 인 경우 **기본 경로 전파** 옵션을 **사용** 으로 설정 합니다. 

   사용 하도록 설정 하면 허브에서 방화벽을 배포할 때 또는 다른 연결 된 사이트에서 강제 터널링을 사용 하는 경우 허브에서 기본 경로를 이미 학습 한 경우에만 가상 WAN 허브가 연결에 전파 됩니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다.  

1. **연결** 을 선택합니다. 몇 분 후에 사이트에서 연결 및 연결 상태를 표시 합니다.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="사이트 간 연결 및 연결 상태를 보여 주는 스크린샷" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. 온-프레미스 끝점에 대 한 [VPN 구성 파일을 다운로드](../virtual-wan/virtual-wan-site-to-site-portal.md#device) 합니다.  

3. 가상 WAN 허브에서 Azure VMware Solution Express 경로를 패치 합니다. 

   >[!IMPORTANT]
   >플랫폼을 패치 하려면 먼저 사설 클라우드를 만들어야 합니다. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. 가상 WAN 허브에서 Azure VMware 솔루션과 VPN gateway를 함께 연결 합니다. 이전 단계에서 권한 부여 키 및 Express 경로 ID (피어 회로 URI)를 사용 합니다.

   1. Express 경로 게이트웨이를 선택 하 고 **권한 부여 키** 를 선택 합니다.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="인증 인증 키가 선택 된 사설 클라우드의 Express 경로 페이지 스크린샷":::

   1. 권한 **부여 키 필드에** 권한 부여 키를 붙여넣습니다.
   1. Express 경로 ID를 **피어 회로 URI** 필드에 붙여넣습니다. 
   1. **이 express 경로 회로를 허브와 자동으로 연결** 확인란을 선택 합니다. 
   1. **추가** 를 선택 하 여 링크를 설정 합니다. 

5. [NSX-T 세그먼트를 만들고](./tutorial-nsx-t-network-segment.md) 네트워크에서 VM을 프로 비전 하 여 연결을 테스트 합니다. 온-프레미스 및 Azure VMware 솔루션 끝점을 모두 Ping 합니다.

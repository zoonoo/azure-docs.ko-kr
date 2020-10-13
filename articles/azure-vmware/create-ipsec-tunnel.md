---
title: Azure VMware 솔루션에 대 한 IPSec 터널 만들기
description: 가상 WAN 허브를 만들어 Azure VMware 솔루션으로의 IPSec 터널을 설정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 74cc31abf432954008cbb20bf64825d199732dab
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951131"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 IPSec 터널 만들기

이 문서에서는 Microsoft Azure 가상 WAN 허브에서 VPN (IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정 하는 단계를 진행 합니다. Azure 가상 WAN 허브와 공용 IP 주소를 연결 하는 VPN gateway를 만듭니다. 그런 다음 Azure Express 경로 게이트웨이를 만들고 Azure VMware 솔루션 끝점을 설정 합니다. 또한 정책 기반 VPN 온-프레미스 설정 사용에 대 한 세부 정보를 살펴봅니다. 

## <a name="topology"></a>토폴로지

![VPN 사이트 간 터널 아키텍처.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure 가상 허브는 Azure VMware 솔루션 Express 경로 게이트웨이 및 사이트 간 VPN 게이트웨이를 포함 합니다. 온-프레미스 VPN 장치를 Azure VMware 솔루션 끝점과 연결 합니다.

## <a name="before-you-begin"></a>시작하기 전에

사이트 간 VPN 터널을 만들려면 온-프레미스 VPN 장치에서 종료 되는 공용 IP 주소를 만들어야 합니다.

## <a name="create-a-virtual-wan-hub"></a>Virtual WAN 허브 만들기

1. Azure Portal에서 **가상 wan**을 검색 합니다. **+추가**를 선택합니다. WAN 만들기 페이지가 열립니다.  

2. **WAN 만들기** 페이지에서 필수 필드를 입력 한 다음 **검토 + 만들기**를 선택 합니다.
   
   | 필드 | 값 |
   | --- | --- |
   | **구독** | 값은 리소스 그룹에 속하는 구독으로 미리 채워집니다. |
   | **리소스 그룹** | 가상 WAN은 글로벌 리소스 이며 특정 지역으로 제한 되지 않습니다.  |
   | **리소스 그룹 위치** | 가상 WAN 허브를 만들려면 리소스 그룹의 위치를 설정 해야 합니다.  |
   | **이름** |   |
   | **형식** | VPN 게이트웨이 트래픽만 허용 하는 **표준**을 선택 합니다.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="WAN을 만듭니다.":::

3. Azure Portal에서 이전 단계에서 만든 가상 WAN을 선택 하 고, **가상 허브 만들기**를 선택 하 고, 필요한 필드를 입력 한 후 **다음: 사이트 간을**선택 합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **지역** | 관리 관점에서 지역 선택이 필요 합니다.  |
   | **이름** |    |
   | **허브 개인 주소 공간** | (최소)를 사용 하 여 서브넷을 입력 `/24` 합니다.  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="WAN을 만듭니다.":::

4. **사이트 간** 탭에서 **게이트웨이 배율 단위** 드롭다운에서 집계 처리량을 설정 하 여 사이트 간 게이트웨이를 정의 합니다. 

   >[!TIP]
   >한 배율 단위 = 500 Mbps 배율 단위는 중복성을 위해 쌍으로, 각각 500 Mbps를 지원 합니다.
  
5. **Express** 경로 탭에서 express 경로 게이트웨이를 만듭니다. 

   >[!TIP]
   >배율 단위 값은 2gbps입니다. 

    각 허브를 만드는 데 약 30 분이 걸립니다. 

## <a name="create-a-vpn-site"></a>VPN 사이트 만들기 

1. Azure Portal의 **최근 리소스** 에서 이전 섹션에서 만든 가상 WAN을 선택 합니다.

2. 가상 허브의 **개요** 에서 **연결**  >  **VPN (사이트 간)** 을 선택 하 고 **새 VPN 사이트 만들기**를 선택 합니다.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="WAN을 만듭니다.":::  
 
3. **기본 사항** 탭에서 필수 필드를 입력 하 고 **다음: 링크**를 선택 합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **지역** | 이전 섹션에서 지정한 것과 동일한 영역입니다.  |
   | **이름** |  |
   | **디바이스 공급 업체** |  |
   | **Border Gateway Protocol** | Azure VMware 솔루션과 온-프레미스 서버가 모두 터널을 통해 해당 경로를 보급 하도록 하려면 **사용** 을 설정으로 설정 합니다. 사용 하지 않도록 설정 된 경우 보급 해야 하는 서브넷은 수동으로 유지 관리 해야 합니다. 서브넷이 누락 되 면 HCX에서 서비스 메시를 구성 하지 못합니다. 자세한 내용은  [Azure VPN Gateway를 사용 하 여 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md)를 참조 하세요. |
   | **개인 주소 공간**  | 온-프레미스 CIDR 블록을 입력 합니다.  터널을 통해 온-프레미스에 대해 바인딩된 모든 트래픽을 라우팅하는 데 사용 됩니다.  CIDR 블록은 BGP를 사용 하도록 설정 하지 않은 경우에만 필요 합니다. |
   | **연결 대상** |   |

4. **링크** 탭에서 필수 필드를 입력 하 고 **검토 + 만들기**를 선택 합니다. 링크 및 공급자 이름을 지정 하면 결국 허브의 일부로 생성 될 수 있는 게이트웨이의 수를 구분할 수 있습니다. BGP 및 ASN (자치 시스템 번호)은 조직 내에서 고유 해야 합니다.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>필드 정책 기반 VPN 사이트 간 터널에 대 한 VPN 사이트 정의

이 섹션은 정책 기반 Vpn에만 적용 됩니다. 정책 기반 (또는 정적, 경로 기반) VPN 설정은 대부분의 경우 온-프레미스 VPN 장치 기능에 의해 결정 됩니다. 온-프레미스 및 Azure VMware 솔루션 네트워크를 지정 해야 합니다. Azure 가상 WAN 허브를 사용 하는 Azure VMware 솔루션의 *경우 네트워크를* 선택할 수 없습니다. 대신 모든 관련 온-프레미스 및 Azure VMware 솔루션 가상 WAN 허브 범위를 지정 해야 합니다. 이러한 허브 범위는 정책 기반 VPN 터널 온-프레미스 끝점의 암호화 도메인을 지정 하는 데 사용 됩니다. Azure VMware 솔루션 쪽에만 정책 기반 트래픽 선택기 표시기를 사용 하도록 설정 해야 합니다. 

1. Azure Portal에서 가상 WAN 허브 사이트로 이동 합니다. **연결**에서 **VPN (사이트 간)** 을 선택 합니다.

2. VPN 사이트 이름을 선택 하 고 오른쪽 끝에 있는 줄임표 (...)를 선택 합니다. 그런 다음 **이 허브에 대 한 VPN 섹션 편집을**선택 합니다.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="WAN을 만듭니다." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN 사이트와 허브 간의 연결을 편집 하 고 **저장**을 선택 합니다.
   - IPSec (인터넷 프로토콜 보안), **사용자 지정**을 선택 합니다.
   - 정책 기반 트래픽 선택기를 사용 하 고 **사용** 을 선택 합니다.
   - **Ike 1 단계** 및 **ike 2 단계 (ipsec)** 에 대 한 세부 정보를 지정 합니다. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="WAN을 만듭니다."::: 
 
    정책 기반 암호화 도메인에 속하는 트래픽 선택기 또는 서브넷은 다음과 같아야 합니다.
    
    - 가상 WAN 허브/24
    - Azure VMware 솔루션 사설 클라우드/22
    - 연결 된 Azure virtual network (있는 경우)

## <a name="connect-your-vpn-site-to-the-hub"></a>VPN 사이트를 허브에 연결

1. VPN 사이트 이름 옆의 확인란을 선택 하 고 (이전 **Vpn 사이트에서 사이트** 스크린샷 참조) **vpn 사이트 연결**을 선택 합니다. **미리 공유한 키** 필드에서 온-프레미스 끝점에 대해 이전에 정의 된 키를 입력 합니다. 이전에 정의 된 키가 없는 경우에는이 필드를 비워 둘 수 있습니다. 그러면 키가 자동으로 생성 됩니다. 
 
    허브에 방화벽을 배포 하 고 해당 터널을 통한 연결의 다음 홉 인 경우에만 **기본 경로 전파** 를 사용 하도록 설정 합니다.

    **연결**을 선택합니다. 연결 상태 화면에 터널 생성 상태가 표시 됩니다.

2. 가상 WAN 개요로 이동 합니다. VPN 사이트 페이지를 열고 VPN 구성 파일을 다운로드 하 여 온-프레미스 끝점에 적용 합니다.  

3. 이제 가상 WAN 허브로 Azure VMware Solution Express 경로를 패치 합니다. 이 단계를 수행 하려면 먼저 사설 클라우드를 만들어야 합니다.

    Azure VMware 솔루션 사설 클라우드의 **연결** 섹션으로 이동 합니다. **Express** 경로 탭에서 **+ 인증 키 요청**을 선택 합니다. 이름을로 선택 하 고 **만들기**를 선택 합니다. 키를 만드는 데 30 초 정도 걸릴 수 있습니다. Express 경로 ID와 권한 부여 키를 복사 합니다. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="WAN을 만듭니다.":::

    > [!NOTE]
    > 권한 부여 키는 일정 시간 후에 사라지고 표시 되는 즉시 복사 합니다.

4. 다음으로, 가상 WAN 허브에서 Azure VMware 솔루션과 VPN 게이트웨이를 연결 합니다. Azure Portal에서 이전에 만든 가상 WAN을 엽니다. 만든 가상 WAN 허브를 선택한 다음 왼쪽 창에서 **express** 경로를 선택 합니다. **+ 권한 부여 키**를 선택 합니다.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="WAN을 만듭니다.":::

    권한 부여 키를 인증 키 필드에 붙여넣고 Express 경로 ID를 **피어 회로 URI** 필드에 붙여넣습니다. **이 express 경로 회로를 허브와 자동으로 연결** 을 선택 해야 합니다. **추가** 를 선택 하 여 링크를 설정 합니다. 

5. 연결을 테스트 하려면 [NSX-T 세그먼트를 만들고](./tutorial-nsx-t-network-segment.md) 네트워크에서 VM을 프로 비전 합니다. 온-프레미스 및 Azure VMware 솔루션 끝점을 모두 ping 하 여 테스트 합니다.
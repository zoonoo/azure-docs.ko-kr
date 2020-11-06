---
title: Azure VMware 솔루션에 대 한 DHCP 관리
description: Azure VMware 솔루션 사설 클라우드의 DHCP를 만들고 관리 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 9143a8544fe1b98262c3e990ccdf56f5d5f65957
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335991"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 DHCP 관리

사설 클라우드 환경에서 실행 되는 응용 프로그램 및 작업에는 IP 주소 할당을 위한 DHCP 서비스가 필요 합니다.  이 문서에서는 다음과 같은 두 가지 방법으로 Azure VMware 솔루션에서 DHCP를 만들고 관리 하는 방법을 보여 줍니다.

- NSX-T를 사용 하 여 DHCP 서버를 호스트 하는 경우 [dhcp 서버를 만들고](#create-a-dhcp-server) [해당 서버에 릴레이](#create-dhcp-relay-service)해야 합니다. DHCP 서버를 만들 때 네트워크 세그먼트도 추가 하 고 DHCP IP 주소 범위를 지정 합니다.   

- 네트워크에서 타사의 외부 DHCP 서버를 사용 하는 경우 [dhcp 릴레이 서비스를 만들어야](#create-dhcp-relay-service)합니다. Dhcp 서버에 대 한 릴레이를 만들 때 NSX 또는 타사를 사용 하 여 DHCP 서버를 호스트 하는지 여부에 관계 없이 dhcp IP 주소 범위를 지정 해야 합니다.

>[!IMPORTANT]
>Dhcp 서버가 온-프레미스 데이터 센터에 있는 경우 VMware HCX L2 스트레치 네트워크의 Vm (가상 컴퓨터)에 대해 DHCP가 작동 하지 않습니다.  NSX는 기본적으로 모든 DHCP 요청이 L2 스트레치를 통과 하지 못하도록 차단 합니다. 솔루션에 대 한 자세한 내용은 [온-프레미스 dhcp 서버에 dhcp 요청 보내기](#send-dhcp-requests-to-the-on-premises-dhcp-server) 절차를 참조 하세요.


## <a name="create-a-dhcp-server"></a>DHCP 서버 만들기

NSX-T를 사용 하 여 DHCP 서버를 호스트 하려는 경우 DHCP 서버를 만듭니다. 그런 다음 네트워크 세그먼트를 추가 하 고 DHCP IP 주소 범위를 지정 합니다.

1. NSX-T 관리자에서 **네트워킹**  >  **DHCP** 를 선택한 다음 **서버 추가** 를 선택 합니다.

1. **서버 유형에** 대해 **DHCP** 를 선택 하 고 서버 이름 및 IP 주소를 입력 한 다음, **저장** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP 서버 추가" border="true":::

1. **계층 1** 게이트웨이를 선택 하 고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="사용할 게이트웨이를 선택 합니다." border="true":::

1. 서브넷을 추가 하려면 **IP 할당 집합 없음** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="서브넷 추가" border="true":::

1. **유형** 에 대해 **DHCP 로컬 서버** 를 선택 합니다. 
   
1. **Dhcp 서버** 에 대해 **기본 dhcp** 를 선택한 다음 **저장** 을 선택 합니다.

1. **저장** 을 다시 선택 하 고 **편집 닫기** 를 선택 합니다.

### <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP 릴레이 서비스 만들기

타사의 외부 DHCP 서버를 사용 하려는 경우 DHCP 릴레이 서비스를 만들어야 합니다. 또한 NSX에서 DHCP IP 주소 범위를 지정 합니다. 

1. NSX-T 관리자에서 **네트워킹**  >  **DHCP** 를 선택한 다음 **서버 추가** 를 선택 합니다.

1. **서버 유형에** 대해 **DHCP 릴레이** 를 선택 하 고 서버 이름 및 IP 주소를 입력 한 다음, **저장** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="dhcp 릴레이 서비스 만들기" border="true":::

1. **계층 1** 게이트웨이를 선택 하 고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="계층 1 게이트웨이 편집" border="true":::

1. Ip **할당 설정 안 함** 을 선택 하 여 ip 주소 할당을 정의 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="ip 주소 할당 편집" border="true":::

1. **유형** 에 대해 **DHCP 서버** 를 선택 합니다. 
   
1. **Dhcp 서버** 에 대해 **dhcp Relay** 를 선택 하 고 **저장** 을 선택 합니다.

1. **저장** 을 다시 선택 하 고 **편집 닫기** 를 선택 합니다.


## <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP 주소 범위 지정

1. NSX-T 관리자에서 **네트워킹**  >  **세그먼트** 를 선택 합니다. 
   
1. 세그먼트 이름에서 세로 줄임표를 선택 하 고 **편집** 을 선택 합니다.
   
1. 서브넷 **설정** 을 선택 하 여 서브넷에 대 한 DHCP IP 주소를 지정 합니다. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="네트워크 세그먼트" border="true":::
      
1. 필요한 경우 게이트웨이 IP 주소를 수정 하 고 DHCP 범위 IP를 입력 합니다. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="서브넷 편집" border="true":::
      
1. **적용** 을 선택한 다음 **저장** 을 선택 합니다. 세그먼트에는 DHCP 서버 풀이 할당 됩니다.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="세그먼트에 할당 된 DHCP 서버 풀" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>온-프레미스 DHCP 서버에 DHCP 요청을 보냅니다.

L2 확장 세그먼트의 Azure VMware 솔루션 Vm에서 온-프레미스 DHCP 서버로 DHCP 요청을 전송 하려는 경우 보안 세그먼트 프로필을 만듭니다. 

1. 온-프레미스 vCenter에 로그인하고, **홈** 에서 **HCX** 를 선택합니다.

1. **서비스** 아래에서 **네트워크 확장** 을 선택 합니다.

1. Azure VMware 솔루션에서 온-프레미스로 DHCP 요청을 지원 하려는 네트워크 확장을 선택 합니다. 

1. 대상 네트워크 이름을 적어둡니다.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere Client에서 네트워크 확장의 스크린샷" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Azure VMware Solution NSX에서 **네트워킹**  >  **세그먼트**  >  **세그먼트 프로필** 을 선택 합니다. 

1. **세그먼트 프로필 추가** , **세그먼트 보안** 을 차례로 선택 합니다.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="NSX에서 세그먼트 프로필을 추가 하는 방법의 스크린샷" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 이름 및 태그를 제공한 다음 **Bpdu 필터** 를 켜기로 설정 하 고 모든 DHCP를 OFF로 설정 합니다.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="BPDU 필터를 설정 하 고 DHCP가 해제 된 상태로 표시 되는 스크린샷" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. **Bpdu 필터 허용 목록** 에서 모든 MAC 주소 (있는 경우)를 제거 합니다.  그런 다음 **저장** 을 선택합니다.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="BPDU 필터 허용 목록의 MAC 주소를 보여 주는 스크린샷":::

1. **네트워킹**  >  **세그먼트**  >  **세그먼트** 의 검색 영역에서 정의 네트워크 이름을 입력 합니다.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="네트워킹 > 세그먼트 필터 필드의 스크린샷":::

1. 세그먼트 이름에서 세로 줄임표를 선택 하 고 **편집** 을 선택 합니다.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="세그먼트에 대 한 편집 단추의 스크린샷" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. **세그먼트 보안** 을 이전에 만든 세그먼트 프로필로 변경 합니다.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="세그먼트 보안 필드의 스크린샷" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>다음 단계

[호스트 유지 관리 및 수명 주기 관리](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)에 대해 자세히 알아보세요.
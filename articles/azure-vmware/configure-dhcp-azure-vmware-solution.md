---
title: Azure VMware Solution에서 DHCP 구성 및 관리
description: Azure VMware Solution 프라이빗 클라우드를 위해 DHCP를 만들고 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 05/17/2021
ms.openlocfilehash: 28fb995cbc91d6e350e8d295c1f57d62c706e59e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104765"
---
# <a name="configure-and-manage-dhcp-in-azure-vmware-solution"></a>Azure VMware Solution에서 DHCP 구성 및 관리

프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에는 IP 주소 할당을 위한 DHCP 서비스가 필요합니다.  이 문서에서는 다음과 같은 두 가지 방법으로 Azure VMware Solution에서 DHCP를 만들과 관리하는 방법을 보여줍니다.

- NSX-T를 사용하여 DHCP 서버를 호스트하는 경우 [DHCP 서버를 생성](#create-a-dhcp-server)하고 [해당 서버에 릴레이](#create-dhcp-relay-service)해야 합니다. DHCP 서버를 만들 때 네트워크 세그먼트도 추가 하 고 DHCP IP 주소 범위를 지정합니다.   

- 네트워크에서 타사의 외부 DHCP 서버를 사용하는 경우 [DHCP 릴레이 서비스를 생성](#create-dhcp-relay-service)해야 합니다. DHCP 서버에 대한 릴레이를 만들 때 NSX-T 또는 타사를 사용하여 DHCP 서버를 호스트하는지 여부에 관계없이 DHCP IP 주소 범위를 지정해야 합니다.

>[!IMPORTANT]
>DHCP 서버가 온-프레미스 데이터 센터에 있는 경우 VMware HCX L2 스트레치 네트워크의 VM(가상 머신)에 대해 DHCP가 작동하지 않습니다.  NSX는 기본적으로 모든 DHCP 요청이 L2 스트레치를 통과하지 못하도록 차단합니다. 이 솔루션은 [L2 확장 VMware HCX 네트워크에서 DHCP 구성](configure-l2-stretched-vmware-hcx-networks.md) 절차를 참조하세요.


## <a name="create-a-dhcp-server"></a>DHCP 서버 만들기

NSX-T를 사용하여 DHCP 서버를 호스트하려는 경우 DHCP 서버를 만듭니다. 그런 다음 네트워크 세그먼트를 추가하고 DHCP IP 주소 범위를 지정합니다.

1. NSX-T 관리자에서 **네트워킹** > **DHCP** 를 선택한 다음, **서버 추가** 를 선택합니다.

1. **서버 유형** 에 **DHCP** 를 선택하고 서버 이름 및 IP 주소를 제공한 다음 **저장** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="및 DHCP 서버" border="true":::

1. **계층 1 게이트웨이** 를 선택하고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="사용할 게이트웨이 선택" border="true":::

1. 서브넷을 추가하려면 **IP 할당 집합 없음** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="서브넷 추가" border="true":::

1. **형식** 은 **DHCP 로컬 서버** 를 선택합니다. 
   
1. **DHCP 서버** 는 **기본 DHCP** 를 선택한 다음 **저장** 을 선택합니다.

1. 다시 **저장** 을 선택하고 **편집 닫기** 를 선택합니다.

### <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP 릴레이 서비스 만들기

타사의 외부 DHCP 서버를 사용하려는 경우 DHCP 릴레이 서비스를 만들어야 합니다. 또한 NSX-T Manager에서 DHCP IP 주소 범위를 지정합니다. 

1. NSX-T 관리자에서 **네트워킹** > **DHCP** 를 선택한 다음, **서버 추가** 를 선택합니다.

1. **서버 형식** 에 **DHCP 릴레이** 를 선택하고 서버 이름 및 IP 주소를 제공한 다음 **저장** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP 릴레이 서비스 만들기" border="true":::

1. **계층 1 게이트웨이** 를 선택하고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="계층 1 게이트웨이 편집" border="true":::

1. **IP 할당 집합 없음** 을 선택하여 IP 주소 할당을 정의합니다.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP 주소 할당 편집" border="true":::

1. **형식** 은 **DHCP 서버** 를 선택합니다. 
   
1. **DHCP 서버** 는 **DHCP 릴레이**, **저장** 을 차례로 선택합니다.

1. 다시 **저장** 을 선택하고 **편집 닫기** 를 선택합니다.


## <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP 주소 범위 지정

1. NSX-T Manager에서 **네트워킹** > **세그먼트** 를 선택합니다. 
   
1. 세그먼트 이름에서 세로 줄임표를 선택하고 **편집** 을 선택합니다.
   
1. **서브넷 설정** 을 선택하고 서브넷에 DHCP IP 주소를 지정합니다. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="네트워크 세그먼트" border="true":::
      
1. 필요한 경우 게이트웨이 IP 주소를 수정하고 DHCP 범위 IP를 입력합니다. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="서브넷 편집" border="true":::
      
1. **적용** 을 선택하고 **저장** 을 선택합니다. 세그먼트에는 DHCP 서버 풀이 할당됩니다.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="세그먼트에 DHCP 서버 풀 할당됨" border="true":::



## <a name="next-steps"></a>다음 단계

Azure VMware Solution VM에서 비 NSX-T DHCP 서버로 DHCP 요청을 보내려는 경우 새 보안 세그먼트 프로필을 만듭니다. 이 솔루션은 [L2 확장 VMware HCX 네트워크에서 DHCP 구성](configure-l2-stretched-vmware-hcx-networks.md) 절차를 참조하세요.


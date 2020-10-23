---
title: DHCP를 만들고 관리 하는 방법
description: 이 문서에서는 Azure VMware 솔루션에서 DHCP를 관리 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461059"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Azure VMware 솔루션에서 DHCP를 만들고 관리 하는 방법

NSX-T는 사설 클라우드에 대해 DHCP를 구성 하는 기능을 제공 합니다. NSX-T를 사용 하 여 DHCP 서버를 호스트 하는 경우 [dhcp 서버 만들기](#create-dhcp-server)를 참조 하세요. 그렇지 않고 네트워크에 타사 외부 DHCP 서버가 있는 경우 [dhcp 릴레이 서비스 만들기](#create-dhcp-relay-service)를 참조 하세요.

## <a name="create-dhcp-server"></a>DHCP 서버 만들기

NSX에서 DHCP 서버를 구성 하려면 다음 단계를 사용 합니다.

1. NSX manager에서 **네트워킹** 탭으로 이동 하 고 **DHCP**를 선택 합니다. 
1. **서버 추가** 를 선택 하 고 서버 이름 및 IP 주소를 제공 합니다. 
1. **저장**을 선택합니다.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP 서버 추가" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>계층 1 게이트웨이에 DHCP 서버를 연결 합니다.

1. **계층 1 게이트웨이**를 선택 하 고 목록에서 게이트웨이를 선택한 다음 **편집**을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="DHCP 서버 추가" border="true":::

1. 서브넷을 추가 하려면 **IP 할당 집합 없음** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="DHCP 서버 추가" border="true":::

1. **유형**으로 **DHCP 로컬 서버** 를 선택 합니다. 
1. **Dhcp 서버** 에 대 한 **기본 dhcp** 를 선택한 다음 **저장**을 선택 합니다.


1. **계층 1 게이트웨이** 창에서 **저장**을 선택 합니다. 
1. **편집 닫기** 를 선택 하 여 마칩니다.

### <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

DHCP 서버를 만들었으면 네트워크 세그먼트를 추가 해야 합니다.

1. NSX에서 **네트워킹** 탭을 선택 하 고 **연결**아래에서 **세그먼트** 를 선택 합니다. 
1. **세그먼트 추가** 를 선택 하 고 세그먼트 이름 및 계층 1 게이트웨이에 대 한 연결을 선택 합니다. 
1. **서브넷 설정** 을 선택 하 여 새 서브넷을 구성 합니다. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="DHCP 서버 추가" border="true":::

1. **서브넷 설정** 창에서 **서브넷 추가**를 선택 합니다. 
1. 게이트웨이 IP 주소와 DHCP 범위를 입력 하 고 **추가** 를 선택한 다음 **적용** 을 선택 합니다.

1. **저장** 을 선택 하 여 새 네트워크 세그먼트를 추가 합니다.

## <a name="create-dhcp-relay-service"></a>DHCP 릴레이 서비스 만들기

1. **네트워킹** 탭을 선택 하 고 **IP 관리**에서 **DHCP**를 선택 합니다. 
1. **서버 추가**를 선택 합니다. 
1. **서버 유형에** 대해 DHCP relay를 선택 하 고 릴레이 서버에 대 한 서버 이름 및 IP 주소를 입력 합니다. 
1. **저장**을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP 서버 추가" border="true":::

1. **연결**아래에서 **계층 1 게이트웨이를** 선택 합니다. 
1. 계층 1 게이트웨이에서 세로 줄임표를 선택 하 고 **편집**을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="DHCP 서버 추가" border="true":::

1. Ip **할당 설정 안 함** 을 선택 하 여 ip 주소 할당을 정의 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="DHCP 서버 추가" border="true":::

1. **유형**으로 **DHCP 릴레이 서버** 를 선택 합니다.
1. Dhcp **릴레이**를 위한 dhcp 릴레이 서버를 선택 합니다. 
1. **저장**을 선택합니다.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>세그먼트에서 DHCP 범위 IP 지정

> [!NOTE]
> 이 구성은 DHCP 클라이언트 세그먼트에서 DHCP 릴레이 기능을 인식 하는 데 필요 합니다. 

1. **연결**에서 **세그먼트**를 선택 합니다. 
1. 세로 줄임표를 선택 하 고 **편집**을 선택 합니다. 

   >[!TIP]
   >새 세그먼트를 추가 하려면 **세그먼트 추가**를 선택 합니다.

1. 세그먼트에 대 한 세부 정보를 추가 합니다. 
1. 서브넷 **설정** 아래에서 서브넷을 추가 하거나 수정 하려면 값을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP 서버 추가" border="true":::

1. 세로 줄임표를 선택 하 고 **편집**을 선택 합니다. 새 서브넷을 만들어야 하는 경우 **서브넷 추가** 를 선택 하 여 게이트웨이를 만들고 DHCP 범위를 구성 합니다. 
1. IP 풀의 범위를 제공 하 고 **적용**을 선택한 다음, **저장** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="DHCP 서버 추가" border="true":::

   DHCP 서버 풀이 세그먼트에 할당 됩니다.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP 서버 추가" border="true":::

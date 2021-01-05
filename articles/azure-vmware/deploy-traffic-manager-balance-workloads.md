---
title: Traffic Manager를 배포 하 여 Azure VMware 솔루션 워크 로드 균형 조정
description: Azure VMware 솔루션과 Traffic Manager를 통합 하 여 여러 지역의 여러 끝점에서 응용 프로그램 워크 로드의 균형을 조정 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: 6dbd58f17e29b045bd654bee90b6390f608803ab
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809737"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Traffic Manager를 배포 하 여 Azure VMware 솔루션 워크 로드 균형 조정

이 문서에서는 azure [Traffic Manager](../traffic-manager/traffic-manager-overview.md) 를 Azure VMware 솔루션과 통합 하는 방법에 대 한 단계를 안내 합니다. 통합은 여러 끝점에서 응용 프로그램 워크 로드를 분산 합니다. 또한이 문서에서는 여러 Azure VMware 솔루션 지역에 걸쳐 있는 세 개의 [Azure 애플리케이션 게이트웨이](../application-gateway/overview.md) 간에 트래픽을 전달 하도록 Traffic Manager를 구성 하는 방법의 단계를 안내 합니다. 

게이트웨이에는 들어오는 계층 7 요청의 부하를 분산 하기 위해 백 엔드 풀 멤버로 구성 된 Azure VMware 솔루션 Vm (가상 머신)이 있습니다. 자세한 내용은 [Azure 애플리케이션 Gateway를 사용 하 여 Azure VMware 솔루션에서 웹 앱 보호](protect-azure-vmware-solution-with-application-gateway.md) 를 참조 하세요.

이 다이어그램에서는 Traffic Manager 지역 끝점 간의 DNS 수준에서 응용 프로그램에 대 한 부하 분산을 제공 하는 방법을 보여 줍니다. 게이트웨이에는 IIS 서버로 구성 된 백 엔드 풀 멤버가 있으며 Azure VMware 솔루션 외부 끝점으로 참조 됩니다. 두 사설 클라우드 지역 간에 가상 네트워크를 통해 연결할 때 Express 경로 게이트웨이를 사용 합니다.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Azure VMware 솔루션과 Traffic Manager 통합의 아키텍처 다이어그램" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

시작 하기 전에 먼저 [필수 구성 요소](#prerequisites) 를 검토 하 고 다음 절차를 수행 합니다.

> [!div class="checklist"]
> * 응용 프로그램 게이트웨이 및 NSX 세그먼트의 구성 확인
> * Traffic Manager 프로필 만들기
> * Traffic Manager 프로필에 외부 끝점 추가

## <a name="prerequisites"></a>필수 구성 요소

- 서로 다른 Azure VMware 솔루션 지역에서 실행 되는 Microsoft IIS 서버로 구성 된 세 개의 Vm: 
   - 미국 서부
   - 서유럽
   - 미국 동부 (온-프레미스) 

- 위에서 언급 한 Azure VMware 솔루션 지역에서 외부 끝점을 사용 하는 응용 프로그램 게이트웨이입니다.

- 확인을 위해 인터넷에 연결 된 호스트입니다. 

- [Azure VMware 솔루션에서 만든 NSX-T 네트워크 세그먼트](tutorial-nsx-t-network-segment.md)입니다.

## <a name="verify-your-application-gateways-configuration"></a>응용 프로그램 게이트웨이 구성 확인

다음 단계는 응용 프로그램 게이트웨이의 구성을 확인 합니다.

1. Azure Portal에서 **응용 프로그램 게이트웨이** 를 선택 하 여 현재 응용 프로그램 게이트웨이의 목록을 봅니다.

   - GW-WUS
   - GW-EUS (온-프레미스)
   - GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="구성 된 응용 프로그램 게이트웨이의 목록을 보여 주는 응용 프로그램 게이트웨이 페이지의 스크린샷" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 이전에 배포 된 응용 프로그램 게이트웨이 중 하나를 선택 합니다. 

   응용 프로그램 게이트웨이에 대 한 다양 한 정보가 표시 된 창이 열립니다. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="선택한 응용 프로그램 게이트웨이의 세부 정보를 표시 하는 응용 프로그램 게이트웨이 페이지의 스크린샷" lightbox="media/traffic-manager/backend-pool-config.png":::

1. 백 엔드 풀 중 하나의 구성을 확인 하려면 **백 엔드 풀** 을 선택 합니다. 172.29.1.10 IP 주소를 사용 하 여 웹 서버로 구성 된 VM 백 엔드 풀 멤버가 하나 표시 됩니다.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="대상 IP 주소가 강조 표시 된 백 엔드 풀 편집 페이지의 스크린샷":::

1. 다른 응용 프로그램 게이트웨이 및 백 엔드 풀 멤버의 구성을 확인 합니다. 

## <a name="verify-the-nsx-t-segment-configuration"></a>NSX-T 세그먼트 구성 확인

다음 단계는 Azure VMware 솔루션 환경에서 NSX-T 세그먼트의 구성을 확인 합니다.

1. **세그먼트** 를 선택 하 여 구성 된 세그먼트를 표시 합니다.  Segment1에 연결 된 contoso-T01 게이트웨이, 계층 1의 유연한 라우터를 볼 수 있습니다.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="NSX-T 관리자의 세그먼트 프로필을 보여 주는 스크린샷" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. **계층 1** 게이트웨이를 선택 하 여 연결 된 세그먼트 수를 포함 하는 계층 1 게이트웨이의 목록을 표시 합니다. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="선택한 세그먼트의 게이트웨이 주소를 보여 주는 스크린샷":::    

1. Contoso-T01에 연결 된 세그먼트를 선택 합니다. 계층 01 라우터에 구성 된 논리 인터페이스를 표시 하는 창이 열립니다. 이는 세그먼트에 연결 된 백 엔드 풀 구성원 VM에 대 한 게이트웨이로 사용 됩니다.

1. VSphere 클라이언트에서 VM을 선택 하 여 세부 정보를 확인 합니다. 

   >[!NOTE]
   >해당 IP 주소는 이전 섹션인 172.29.1.10에서 웹 서버로 구성 된 VM 백 엔드 풀 구성원과 일치 합니다.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="VSphere 클라이언트에서 VM 세부 정보를 보여 주는 스크린샷" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. VM을 선택한 다음, 작업을 선택 하 **> 설정 편집** 을 선택 하 여 NSX 세그먼트에 대 한 연결을 확인 합니다.

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. [Azure Portal](https://rc.portal.azure.com/#home)에 로그인합니다. **Azure 서비스 > 네트워킹** 에서 **Traffic Manager 프로필** 을 선택 합니다.

2. **+ 추가** 를 선택 하 여 새 Traffic Manager 프로필을 만듭니다.
 
3. 다음 정보를 입력 한 다음 **만들기** 를 선택 합니다.

   - 프로필 이름
   - 라우팅 메서드 ( [가중치](../traffic-manager/traffic-manager-routing-methods.md) 사용
   - 구독
   - 리소스 그룹

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager 프로필에 외부 끝점 추가

1. 검색 결과 창에서 Traffic Manager 프로필을 선택 하 고 **끝점** 을 선택한 다음 **+ 추가** 를 선택 합니다.

1. 다른 지역의 각 외부 끝점에 대해 필요한 세부 정보를 입력 하 고 **추가** 를 선택 합니다. 
   - Type
   - Name
   - FQDN (정규화 된 도메인 이름) 또는 IP
   - 가중치 (각 끝점에 가중치 1을 할당) 

   만든 후에는 세 가지 모두 Traffic Manager 프로필에 표시 됩니다. 세 가지 모니터 상태 모두 **온라인** 상태 여야 합니다.

3. **개요** 를 선택 하 고 **DNS 이름** 아래에서 URL을 복사 합니다.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS 이름이 강조 표시 된 Traffic Manager 끝점 개요를 보여 주는 스크린샷" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. DNS 이름 URL을 브라우저에 붙여 넣습니다. 스크린샷 유럽 서부 영역으로 전송 되는 트래픽을 보여 줍니다.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="유럽 서부로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷"::: 

5. 브라우저를 새로 고칩니다. 스크린샷에는 미국 서 부 지역에서 다른 백 엔드 풀 구성원 집합으로 전송 되는 트래픽이 표시 됩니다.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="미국 서 부로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷."::: 

6. 브라우저를 다시 새로 고칩니다. 스크린샷에서는 온-프레미스 백 엔드 풀 구성원 집합으로 전송 되는 트래픽을 보여 줍니다.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="온-프레미스로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷":::

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure VMware 솔루션에서 Azure 애플리케이션 게이트웨이 사용](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)
- [Azure에서 부하 분산 서비스 결합](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager 성능 측정](../traffic-manager/traffic-manager-performance-considerations.md)

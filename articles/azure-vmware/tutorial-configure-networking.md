---
title: 자습서 - Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성
description: Azure에서 프라이빗 클라우드를 배포하는 데 필요한 네트워킹을 만들고 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 6aff39284f3ea786080055552ac001ac5dd7b394
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578356"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성

Azure VMware Solution 프라이빗 클라우드에는 Azure Virtual Network가 필요합니다. Azure VMware Solution가 온-프레미스 vCenter를 지원하지 않으므로 온-프레미스 환경과 통합하기 위한 추가 단계가 필요합니다. ExpressRoute 회로와 가상 네트워크 게이트웨이도 설정해야 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

## <a name="prerequisites"></a>필수 구성 요소 
[Azure VMware Solution 프라이빗 클라우드](tutorial-create-private-cloud.md)를 만든 가상 네트워크 

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [프라이빗 클라이드 만들기 자습서](tutorial-create-private-cloud.md)에서 만든 리소스 그룹으로 이동하고 **+ 추가**를 선택하여 새 리소스를 정의합니다. 

1. **Marketplace 검색** 텍스트 상자에 **Virtual Network**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크** 페이지에서 **만들기**를 선택하여 프라이빗 클라우드에 대한 가상 네트워크를 설정합니다.

1. **Virtual Network 만들기** 페이지에서 가상 네트워크에 대한 세부 정보를 입력합니다.

1. **기본** 탭에서 가상 네트워크 이름을 입력하고, 적절한 지역을 선택하고, **다음: IP 주소**를 선택합니다.

1. **IP 주소** 탭의 **IPv4 주소 공간** 아래에서 이전 자습서에서 만든 주소 공간을 입력합니다.

   > [!IMPORTANT]
   > 이전 자습서에서 프라이빗 클라우드를 만들 때 사용한 주소 공간과 **겹치지 않는** 주소 공간을 사용해야 합니다.

1. **+ 서브넷 추가**를 선택하고, **서브넷 추가** 페이지에서 이름과 적절한 주소 범위를 서브넷에 지정합니다. 마쳤으면 **추가**를 선택합니다.

1. **검토 + 만들기**를 선택합니다.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="검토 + 만들기를 선택합니다." border="true":::

1. 정보를 확인하고 **만들기**를 선택합니다. 배포가 완료되면 리소스 그룹에 가상 네트워크가 표시됩니다.

## <a name="create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

가상 네트워크를 만들었으므로 이제 가상 네트워크 게이트웨이를 만듭니다.

1. 리소스 그룹에서 **+ 추가**를 선택하여 새 리소스를 추가합니다.

1. **Marketplace 검색** 텍스트 상자에서 **가상 네트워크 게이트웨이**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크 게이트웨이** 페이지에서 **만들기**를 선택합니다.

1. **가상 네트워크 게이트웨이 만들기** 페이지의 기본 탭에서 필드 값을 제공한 다음, **검토 + 만들기**를 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 리소스 그룹이 속하는 구독으로 미리 채워진 값입니다. |
   | **리소스 그룹** | 현재 리소스 그룹에 대해 이미 채워진 값입니다. 값은 이전 테스트에서 만든 리소스 그룹이어야 합니다. |
   | **이름** | 가상 네트워크 게이트웨이에 대한 고유한 이름을 입력합니다. |
   | **지역** | 가상 네트워크 게이트웨이의 지리적 위치를 선택합니다. |
   | **게이트웨이 유형** | **ExpressRoute**를 선택합니다. |
   | **SKU** | 기본값(**표준**)을 그대로 둡니다. |
   | **가상 네트워크** | 이전에 만든 가상 네트워크를 선택합니다. 가상 네트워크가 표시되지 않으면 게이트웨이의 지역이 가상 네트워크의 지역과 일치하는지 확인합니다. |
   | **게이트웨이 서브넷 주소 범위** | 이 값은 가상 네트워크를 선택할 때 채워집니다. 기본값을 변경하지 마세요. |
   | **공용 IP 주소** | **새로 만들기**를 선택합니다. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="검토 + 만들기를 선택합니다." border="true":::

1. 세부 정보가 올바른지 확인하고 **만들기**를 선택하여 가상 네트워크 게이트웨이의 배포를 시작합니다. 
1. 배포가 완료되면 다음 섹션으로 이동하여 ExpressRoute 연결을 Azure VMware Solution 프라이빗 클라우드가 포함된 가상 네트워크 게이트웨이에 연결합니다.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이에 ExpressRoute 연결

가상 네트워크 게이트웨이를 배포했으므로 이제 게이트웨이와 Azure VMware Solution 프라이빗 클라우드 간에 연결을 추가합니다.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>vCenter 및 NSX 관리자 URL 찾기

vCenter 및 NSX 관리자에 로그인하려면 vCenter 웹 클라이언트 및 NSX-T 관리자 사이트에 대한 URL이 필요합니다. 

Azure VMware Solution 프라이빗 클라우드로 이동하여 **관리** 아래에서 **ID**를 선택합니다. 여기서 필요한 정보를 찾을 수 있습니다.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="검토 + 만들기를 선택합니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

다음 자습서를 계속 진행하여 vCenter에서 VM에 사용되는 NSX-T 네트워크 세그먼트를 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [NSX-T 네트워크 세그먼트 만들기](tutorial-nsx-t-network-segment.md)
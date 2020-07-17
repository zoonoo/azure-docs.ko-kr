---
title: 자습서 - Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성
description: Azure에서 프라이빗 클라우드를 배포하는 데 필요한 네트워킹을 만들고 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6cac420fb77526746dbdbfef5a88b071c007d555
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148109"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성

AVS(Azure VMware 솔루션) 프라이빗 클라우드에는 가상 네트워크가 필요합니다. 미리 보기 중에는 AVS에서 온-프레미스 vCenter를 지원하지 않으므로 온-프레미스 환경과 통합하기 위한 추가 단계가 필요합니다. ExpressRoute 회로와 가상 네트워크 게이트웨이도 설정해야 하며 이 자습서에서 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Virtual Network 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[이전 자습서](tutorial-create-private-cloud.md)에서 만든 리소스 그룹으로 이동하고, **+ 추가**를 선택하여 새 리소스를 정의합니다.

**Marketplace 검색** 텍스트 상자에서 **가상 네트워크**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

[가상 네트워크] 페이지에서 **만들기**를 선택하여 프라이빗 클라우드에 대한 가상 네트워크를 설정합니다.

**가상 네트워크 만들기** 페이지에서 가상 네트워크에 대한 관련 세부 정보를 입력합니다. 다음 표에는 속성에 대한 설명이 나와 있습니다.

> [!IMPORTANT]
> 이전 자습서에서 프라이빗 클라우드를 만들 때 사용한 주소 공간과 **겹치지 않는** 주소 공간을 사용해야 합니다.

**기본** 탭에서 가상 네트워크 이름을 입력하고, 적절한 지역을 선택하고, **다음: IP 주소**를 선택합니다.

**IP 주소** 탭의 **IPv4 주소 공간** 아래에서 이전 자습서에서 만든 주소 공간을 입력합니다.

**+ 서브넷 추가**를 선택하고, **서브넷 추가** 페이지에서 이름과 적절한 주소 범위를 서브넷에 지정합니다. 마쳤으면 **추가**를 선택합니다.

**검토 및 만들기**를 선택합니다.

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="가상 네트워크 만들기" border="true":::

정보를 확인하고 **만들기**를 선택합니다. 배포가 완료되면 리소스 그룹에 가상 네트워크가 표시됩니다.

## <a name="create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

이전 섹션에서 가상 네트워크를 만들었으므로 이제 가상 네트워크 게이트웨이를 만듭니다.

리소스 그룹에서 **+ 추가**를 선택하여 새 리소스를 추가합니다.

**Marketplace 검색** 텍스트 상자에서 **가상 네트워크 게이트웨이**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

**가상 네트워크 게이트웨이** 페이지에서 **만들기**를 선택합니다.

**가상 네트워크 게이트웨이 만들기** 페이지의 [기본] 탭에서 필드 값을 입력합니다. 다음 표에는 필드에 대한 설명이 나와 있습니다.

| 필드 | 값 |
| --- | --- |
| **구독** | 이 값은 이미 리소스 그룹이 속한 구독으로 채워져 있습니다. |
| **리소스 그룹** | 이 값은 현재 리소스 그룹에 대해 이미 채워져 있습니다. 이전 테스트에서 만든 리소스 그룹입니다. |
| **이름** | 가상 네트워크 게이트웨이에 대한 고유한 이름을 입력합니다. |
| **지역** | 가상 네트워크 게이트웨이의 지리적 위치를 선택합니다. |
| **게이트웨이 유형** | **ExpressRoute**를 선택합니다. |
| **VPN 유형** | **경로 기반**을 선택합니다. |
| **SKU** | 기본값(**표준**)을 그대로 둡니다. |
| **가상 네트워크** | 이전에 만든 가상 네트워크를 선택합니다. 가상 네트워크가 표시되지 않으면 게이트웨이의 지역이 가상 네트워크의 지역과 일치하는지 확인합니다. |
| **게이트웨이 서브넷 주소 범위** | 이 값은 가상 네트워크를 선택할 때 채워집니다. 기본값을 변경하지 마세요. |
| **공용 IP 주소** | **새로 만들기**를 선택합니다. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="게이트웨이 만들기" border="true":::

**검토 + 만들기**를 선택하고, 다음 페이지에서 세부 정보가 올바른지 확인하고, **만들기**를 선택하여 가상 네트워크 게이트웨이의 배포를 시작합니다. 배포가 완료되면 이 자습서의 다음 섹션으로 이동하여 ExpressRoute 연결을 프라이빗 클라우드가 포함된 가상 네트워크에 연결합니다.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이에 ExpressRoute 연결

이 섹션에서는 AVS 프라이빗 클라우드와 사용자가 만든 가상 네트워크 게이트웨이 간의 연결을 추가하는 과정을 안내합니다.

이전 자습서에서 만든 프라이빗 클라우드로 이동하여 **관리** 아래에서 **연결**을 선택하고, **ExpressRoute** 탭을 선택합니다.

인증 키를 복사합니다. 인증 키가 없는 경우 해당 키를 만들어야 합니다. 이렇게 하려면 **+ 인증 키 요청**을 선택합니다.

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="인증 키 요청" border="true":::

이전 단계에서 만든 가상 네트워크 게이트웨이로 이동하여 **설정** 아래에서 **연결**을 선택합니다. **연결** 페이지에서 **+ 추가**를 선택합니다.

**연결 추가** 페이지에서 필드 값을 입력합니다. 다음 표에는 필드에 대한 설명이 나와 있습니다.

| 필드 | 값 |
| --- | --- |
| **이름**  | 연결의 이름을 입력합니다.  |
| **연결 형식**  | **ExpressRoute**를 선택합니다.  |
| **인증 사용**  | 이 확인란이 선택되어 있는지 확인합니다.  |
| **가상 네트워크 게이트웨이** | 이전에 만든 가상 네트워크 게이트웨이입니다.  |
| **인증 키**  | 리소스 그룹에 대한 ExpressRoute 탭에서 인증 키를 복사하여 붙여넣습니다. |
| **피어 회로 URI**  | 리소스 그룹에 대한 ExpressRoute 탭에서 ExpressRoute ID를 복사하여 붙여넣습니다.  |

**확인**을 선택합니다. 그러면 ExpressRoute 회로 및 가상 네트워크 간의 연결이 만들어집니다.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="연결 추가" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>vCenter 및 NSX 관리자 URL 찾기

vVenter 및 NSX 관리자에 로그인하려면 vCenter 웹 클라이언트 및 NSX-T 관리자 사이트에 대한 URL이 필요합니다. URL을 찾으려면 다음을 수행합니다.

AVS 프라이빗 클라우드로 이동하여 **관리** 아래에서 **ID**를 선택합니다. 여기서 필요한 정보를 찾을 수 있습니다.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="vCenter URL 찾기" border="true":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Virtual Network 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

프라이빗 클라우드를 로컬로 관리할 수 있도록 환경에 연결하는 데 사용되는 Jumpbox를 만드는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [프라이빗 클라우드 액세스](tutorial-access-private-cloud.md)

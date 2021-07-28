---
title: 가상 허브 라우팅을 구성하는 방법
titleSuffix: Azure Virtual WAN
description: Virtual WAN 가상 허브 라우팅을 구성하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 1211f7adf9555014316dd8160c9493b5541e4a9f
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163412"
---
# <a name="how-to-configure-virtual-hub-routing"></a>가상 허브 라우팅을 구성하는 방법

가상 허브는 사이트 간 VPN 게이트웨이, ExpressRoute 게이트웨이, 지점 및 사이트 간 게이트웨이, Azure 방화벽과 같은 여러 게이트웨이를 포함할 수 있습니다. 가상 허브의 라우팅 기능은 BGP(Border Gateway Protocol)를 사용하는 게이트웨이 간에 전송 라우팅을 비롯한 모든 라우팅을 관리하는 라우터가 제공합니다. 또한 이 라우터는 가상 허브에 연결하는 가상 네트워크 간의 전송 연결을 제공하며 50Gbps의 최대 집계 처리량을 지원할 수 있습니다. 이러한 라우팅 기능은 표준 가상 WAN 고객에게 적용됩니다.

자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="create-a-route-table"></a><a name="create-table"></a>경로 테이블 만들기

1. Azure Portal에서 가상 허브로 이동합니다.
2. **연결** 에서 **라우팅** 을 선택합니다. 라우팅 페이지에 **기본** 및 **없음** 경로 테이블이 표시됩니다.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="라우팅 페이지":::
3. **+경로 테이블 만들기** 를 선택하여 **경로 테이블 만들기** 페이지를 엽니다.
4. 경로 테이블 만들기 페이지의 **기본 사항** 탭에서 다음 필드를 입력합니다.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="기본 사항 탭":::

   * **이름**
   * **경로**
   * **경로 이름**
   * **대상 유형**
   * **대상 접두사**: 접두사를 집계할 수 있습니다. 예를 들어 VNet 1: 10.1.0.0/24 및 VNet 2: 10.1.1.0/24는 10.1.0.0/16으로 집계할 수 있습니다. **분기** 경로는 연결된 모든 VPN 사이트, ExpressRoute 회로 및 사용자 VPN 연결에 적용됩니다.
   * **다음 홉**: 가상 네트워크 연결 또는 Azure 방화벽의 목록입니다.

     가상 네트워크 연결을 선택하는 경우 **고정 경로 구성** 이 표시됩니다. 이는 선택적 구성 설정입니다. 자세한 내용은 [고정 경로 구성](about-virtual-hub-routing.md#static)을 참조하세요.

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="다음 홉":::

5. **레이블** 탭을 선택하여 레이블 이름을 구성합니다. 레이블은 경로 테이블을 논리적으로 그룹화하는 메커니즘을 제공합니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="레이블 이름 구성":::

6. **연결** 탭을 선택하여 연결을 경로 테이블에 연결합니다.
**분기**, **가상 네트워크** 및 연결의 **현재 설정** 이 표시됩니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="연결을 경로 테이블에 연결":::

7. **전파** 탭을 선택하여 연결의 경로를 경로 테이블에 전파합니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="경로 전파":::

8. **만들기** 를 선택하여 경로 테이블을 만듭니다.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>경로 테이블을 편집하려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. 경로 테이블을 선택하여 모든 정보를 편집합니다.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>경로 테이블을 삭제하려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. 기본 또는 없음 경로 테이블은 삭제할 수 없습니다. 그러나 모든 사용자 지정 경로 테이블은 삭제할 수 있습니다. **"…"** 를 클릭한 다음, **삭제** 를 선택합니다.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>유효 경로를 보려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. **"..."** 를 클릭하고 **유효 경로** 를 선택하여 선택한 경로 테이블에 의해 학습된 경로를 확인합니다. 연결에서 경로 테이블로 전파된 경로는 경로 테이블의 **유효 경로** 에 자동으로 채워집니다. 자세한 내용은 [유효 경로 정보](effective-routes-virtual-hub.md)를 참조하세요.

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="유효 경로 보기" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>가상 네트워크 연결에 대한 라우팅 구성을 설정하려면

1. Azure Portal에서 가상 WAN으로 이동하고 **연결** 에서 **가상 네트워크 연결** 을 선택합니다.
1. **+연결 추가** 를 선택합니다.
1. 드롭다운에서 가상 네트워크를 선택합니다.
1. 경로 테이블에 연결할 라우팅 구성을 설정합니다. **경로 테이블 연결** 의 경우 드롭다운에서 경로 테이블을 선택합니다.
1. 1개 또는 많은 경로 테이블에 전파하도록 라우팅 구성을 설정합니다. **경로 테이블에 전파** 의 경우 드롭다운에서 선택합니다.
1. **고정 경로** 의 경우 네트워크 가상 어플라이언스에 대한 고정 경로를 구성합니다(해당하는 경우). 가상 WAN은 가상 네트워크 연결의 고정 경로에 대해 다음 홉 IP를 1개 지원합니다. 예를 들어 수신 및 송신 트래픽 흐름에 대해 별도의 가상 어플라이언스가 있는 경우 별도의 VNET에 가상 어플라이언스를 추가하고 VNET을 가상 허브에 연결하는 것이 가장 좋습니다.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="라우팅 구성 설정" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>다음 단계

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.

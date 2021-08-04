---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b47f21ba6597380ca79bfce03feb8edd62aa5ae
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439733"
---
1. **검색 리소스, 서비스 및 문서(G +/)** 에서 **가상 네트워크 게이트웨이** 를 입력합니다. 검색 결과에서 **가상 네트워크 게이트웨이** 를 찾아서 항목을 선택합니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/search.png" alt-text="검색 필드의 스크린샷" lightbox="./media/vpn-gateway-add-gw-portal/search-expand.png":::

1. **가상 네트워크 게이트웨이** 페이지에서 **+ 만들기** 를 선택합니다. 그러면 **가상 네트워크 게이트웨이 만들기** 페이지가 열립니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/create.png" alt-text="만들기가 강조 표시된 가상 네트워크 게이트웨이 페이지의 스크린샷":::
1. **기본** 탭에서 **프로젝트 세부 정보** 및 **인스턴스 세부 정보** 에 대한 값을 입력합니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/instance-details.png" alt-text="인스턴스 필드의 스크린샷":::

   * **구독**: 드롭다운 목록에서 사용하려는 구독을 선택합니다.
   * **리소스 그룹**: 이 페이지에서 가상 네트워크를 선택하면 이 설정이 자동으로 채워집니다.
   * **이름**: 게이트웨이 이름을 지정합니다. 게이트웨이 이름 지정은 게이트웨이 서브넷 이름 지정과 동일하지 않습니다. 만드는 게이트웨이 개체의 이름입니다.
   * **지역**: 리소스를 만들려는 지역을 선택합니다. 게이트웨이에 대한 지역은 가상 네트워크와 동일해야 합니다.
   * **게이트웨이 유형**: **VPN** 을 선택합니다. VPN 게이트웨이는 가상 네트워크 게이트웨이 유형 **VPN** 을 사용합니다.
   * **VPN 유형**: 구성에 대해 지정된 VPN 유형을 선택합니다. 대부분의 구성에는 경로 기반 VPN 유형이 필요합니다.
   * **SKU**: 드롭다운에서 사용하려는 게이트웨이 SKU를 선택합니다. 드롭다운에 나열되는 SKU는 선택하는 VPN 유형에 따라 달라집니다. 사용하려는 기능을 지원하는 SKU를 선택해야 합니다. 게이트웨이 SKU에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.
   * **세대:** 사용하려는 세대를 선택합니다. 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.
   * **가상 네트워크**: 드롭다운에서 이 게이트웨이를 추가하려는 가상 네트워크를 선택합니다.
   * **게이트웨이 서브넷 주소 범위**: 이 필드는 VNet에 게이트웨이 서브넷이 없는 경우에만 표시됩니다. /27 이상(/26, /25 등)을 지정하는 것이 가장 좋습니다. 이렇게 하면 ExpressRoute 게이트웨이 추가와 같은 향후 변경에 충분한 IP 주소가 허용됩니다. /28보다 작은 범위는 만들지 않는 것이 좋습니다. 게이트웨이 서브넷이 이미 있는 경우 가상 네트워크로 이동하여 GatewaySubnet 세부 정보를 볼 수 있습니다. **서브넷** 을 클릭하여 범위를 확인합니다. 범위를 변경하려면 GatewaySubnet을 삭제하고 다시 만들 수 있습니다.
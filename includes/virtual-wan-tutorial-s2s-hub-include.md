---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 64470b42efeea49b7c778d6dffd88465b8445e36
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606367"
---
1. 생성한 Virtual WAN을 찾습니다. Virtual WAN 페이지의 **연결** 섹션에서 **허브**를 선택합니다.
2. 허브 페이지에서 **+새 허브**를 선택하여 **가상 허브 만들기** 페이지를 엽니다.

    ![스크린샷은 기본 탭이 선택된 가상 허브 만들기 창을 보여줍니다.](./media/virtual-wan-tutorial-hub-include/basics.png "기본 사항")
3. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

    **프로젝트 세부 정보**

   * 지역(이전에는 위치라고 했음)
   * Name
   * 허브 프라이빗 주소 공간. 허브를 만들기 위한 최소 주소 공간은 /24이며 이것은 /25부터 /32까지의 모든 범위가 생성 중에 오류가 발생함을 의미합니다. Microsoft에서 관리되는 서비스에 해당하는 Azure Virtual WAN은 다양한 게이트웨이/서비스(예: VPN 게이트웨이, ExpressRoute 게이트웨이, 사용자 VPN/지점 및 사이트 간 게이트웨이, 방화벽, 라우팅 등)에 대한 가상 허브에 적절한 서브넷을 만듭니다. Microsoft가 서비스의 일부로 이 작업을 수행하므로 사용자가 Virtual Hub의 서비스에 대한 서브넷 주소 공간을 명시적으로 계획할 필요가 없습니다.
4. 완료되면 **다음: 사이트 간**

    ![스크린샷은 사이트 간 선택된 가상 허브 만들기 창을 보여줍니다.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "사이트 간")

5. **사이트 간** 탭에서 다음 필드를 완료합니다.

   * **예**를 선택하여 사이트 간 VPN을 만듭니다.
   * AS 번호 필드는 현재는 상 허브에서 편집할 수 없습니다.
   * 드롭다운에서 **게이트웨이 배율 단위** 값을 선택합니다. 배율 단위를 사용하여 사이트를 연결할 가상 허브에서 만들 VPN Gateway의 집계 처리량을 선택할 수 있습니다. 1 배율 단위 = 500Mbps를 선택하는 경우 중복성을 위해 두 인스턴스가 생성되고 각각은 최대 500Mbps의 처리량을 갖게 됩니다. 예를 들어, 각각 10Mbps를 수행하는 5개의 분기가 있는 경우 헤드 끝에서 50Mbps로 집계되어야 합니다. 허브에 대한 분기 수를 지원하는 데 필요한 용량을 평가한 후 Azure VPN Gateway의 집계 용량 계획을 수립해야 합니다.
6. **검토 + 만들기**를 선택하여 유효한지 확인합니다.
7. **만들기**를 선택하여 허브를 만듭니다. 30분 후에 **새로 고침**을 선택하여 **허브** 페이지에서 허브를 봅니다. **리소스로 이동**을 선택하여 리소스로 이동합니다.

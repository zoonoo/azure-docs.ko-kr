---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: aacd6a780562476ea338b4c08795e14b6ba39760
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579170"
---
1. 사용자가 만든 가상 WAN을 찾습니다. 가상 WAN 페이지의 **연결** 섹션에서 **Hubs** 를 선택합니다.
1. **Hubs** 페이지에서 **+새 Hub** 를 선택하여 **가상 허브 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="스크린샷은 기본 탭이 선택된 가상 허브 만들기 창을 보여줍니다." border="false":::
1. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   * **지역**(이전에는 위치라고 했음)
   * **이름**
   * **Hub 프라이빗 주소 공간** - 허브를 만들기 위한 최소 주소 공간은 /24입니다. /25 ~/32 범위의 항목을 사용하는 경우 생성 중에 오류가 발생합니다. 가상 허브의 서비스에 대한 서브넷 주소 공간을 명시적으로 계획할 필요가 없습니다. Azure Virtual WAN은 관리형 서비스이므로 다양한 게이트웨이/서비스(예: VPN 게이트웨이, ExpressRoute 게이트웨이, 사용자 VPN 지점 및 사이트 간 게이트웨이, 방화벽, 라우팅 등)에 대한 가상 허브에 적절한 서브넷을 만듭니다.

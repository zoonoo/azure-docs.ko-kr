---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
1. 가상 네트워크 게이트웨이에 대한 페이지로 이동하여 엽니다. 여러 가지 방법으로 이동할 수 있습니다. 예제에서는 **TestVNet1 -> 개요 -> 연결된 장치 -> VNet1GW**로 이동하여 'VNet1GW' 게이트웨이로 이동합니다.
2. VNet1GW 페이지에서 **연결**을 클릭합니다. 연결 페이지의 맨 위에 있는 **+추가**를 클릭하여 **연결 추가** 페이지를 엽니다.

    ![사이트 간 연결 만들기](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. **연결 추가** 페이지에서 사용자 연결을 만드는 값을 채웁니다.

  - **이름:** 연결의 이름을 지정합니다. 예제에서 **VNet1toSite2**를 사용합니다.
  - **연결 형식:** **사이트 간(IPSec)**을 선택합니다.
  - **가상 네트워크 게이트웨이:** 이 게이트웨이에서 연결하기 때문에 값이 고정됩니다.
  - **로컬 네트워크 게이트웨이:** **로컬 네트워크 게이트웨이 선택**을 클릭하고 사용하려는 로컬 네트워크 게이트웨이를 선택합니다. 예제에서는 **Site2**를 사용합니다.
  - **공유 키:** 여기 값은 온-프레미스 로컬 VPN 장치에 사용하는 값과 일치해야 합니다. 이 예제에서는 'abc123'을 사용했지만 좀 더 복잡한 항목을 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 장치를 구성하는 경우 지정한 것과 동일한 값이어야 한다는 것입니다.
  - **구독**, **리소스 그룹** 및 **위치**에 대한 나머지 값이 고정됩니다.

4. **확인** 을 클릭하여 연결을 만듭니다. 화면에서 *연결 만들기* 플래시가 표시됩니다.
5. 가상 네트워크 게이트웨이의 **연결** 페이지에서 연결을 볼 수 있습니다. 상태는 *알 수 없음*에서 *연결 중*으로 변한 다음 *성공함*으로 변니다.
---
title: Azure 가상 WAN 글로벌 또는 허브 기반 VPN 프로필 다운로드 | 마이크로 소프트 문서
description: Virtual WAN의 자동화된 확장성 있는 지점 간 연결, 사용 가능한 지역 및 파트너에 대해 알아봅니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965230"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>사용자 VPN 클라이언트를 위한 글로벌 또는 허브 기반 프로필 다운로드

Azure Virtual WAN은 원격 사용자를 위한 두 가지 유형의 연결, 즉 글로벌 및 허브 기반을 제공합니다. 다음 섹션을 사용하여 프로필에 대해 알아보고 다운로드합니다.

## <a name="global-profile"></a>글로벌 프로필

프로필은 모든 활성 사용자 VPN 허브를 포함하는 로드 밸러저를 가리킵니다. 사용자는 사용자의 지리적 위치에 가장 가까운 허브로 이동합니다. 이러한 유형의 연결은 사용자가 다른 위치로 자주 이동할 때 유용합니다. **글로벌** 프로필을 다운로드하려면 다음 을 수행하십시오.

1. 가상 WAN 탭으로 이동합니다.
2. **사용자 VPN 구성을 클릭합니다.**
3. 프로필을 다운로드할 구성을 강조 표시합니다.
4. **가상 WAN 사용자 VPN 프로필 다운로드를**클릭합니다.

   ![글로벌 프로필](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>허브 기반 프로필

프로파일은 단일 허브를 가리킵니다. 사용자는 이 프로필을 사용하여 특정 허브에만 연결할 수 있습니다. **허브 기반** 프로필을 다운로드하려면 다음 을 수행하십시오.

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지에서 **허브를** 클릭합니다.

    ![허브 프로파일 1](./media/global-hub-profile/hub1.png)
3. **사용자 VPN(사이트 시점)을 클릭합니다.**
4. **가상 허브 사용자 VPN 프로필 다운로드를**클릭합니다.

   ![허브 프로파일 2](./media/global-hub-profile/hub2.png)
5. **EAPTLS를**확인하십시오.
6. **프로필 생성 및 다운로드를 클릭합니다.**

   ![허브 프로파일 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.

---
title: Azure Virtual WAN 전역 또는 허브 기반 VPN 프로필 다운로드 | Microsoft Docs
description: Azure Virtual WAN이 원격 사용자에게 두 가지 연결 형식을 제공하는 방법 및 프로필을 다운로드하는 방법을 알아봅니다.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313725"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>사용자 VPN 클라이언트용 전역 또는 허브 기반 프로필 다운로드

Azure Virtual WAN은 원격 사용자에게 전역 및 허브 기반인 두 가지 연결 형식을 제공합니다. 다음 섹션을 사용하여 프로필에 대해 알아보고 프로필을 다운로드할 수 있습니다. 

> [!IMPORTANT]
> RADIUS 인증은 허브 기반 프로필만 지원합니다.

## <a name="global-profile"></a>전역 프로필

이 프로필은 모든 활성 사용자 VPN 허브를 포함하는 부하 분산 장치를 가리킵니다. 사용자는 사용자의 지리적 위치에 가장 가까운 허브로 전송됩니다. 이 연결 형식은 사용자가 여러 위치로 자주 이동하는 경우에 유용합니다. **전역** 프로필을 다운로드하려면 다음을 수행합니다.

1. 가상 WAN 탭으로 이동합니다.
2. **사용자 VPN 구성** 을 클릭합니다.
3. 프로필을 다운로드하려는 구성을 강조 표시합니다.
4. **가상 WAN 사용자 VPN 프로필 다운로드** 를 클릭합니다.

   ![전역 프로필](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>허브 기반 프로필

이 프로필은 단일 허브를 가리킵니다. 사용자는 이 프로필을 사용하여 특정 허브에만 연결할 수 있습니다. **허브 기반** 프로필을 다운로드하려면 다음을 수행합니다.

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지에서 **허브** 를 클릭합니다.

    ![허브 프로필 1](./media/global-hub-profile/hub1.png)
3. **사용자 VPN(지점 및 사이트 간)** 을 클릭합니다.
4. **가상 허브 사용자 VPN 프로필 다운로드** 를 클릭합니다.

   ![허브 프로필 2](./media/global-hub-profile/hub2.png)
5. **EAPTLS** 를 선택합니다.
6. **프로필 생성 및 다운로드** 를 클릭합니다.

   ![허브 프로필 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.

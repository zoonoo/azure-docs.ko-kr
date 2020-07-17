---
title: Azure 가상 WAN 글로벌 또는 허브 기반 VPN 프로필 다운로드 | Microsoft Docs
description: Virtual WAN의 자동화된 확장성 있는 지점 간 연결, 사용 가능한 지역 및 파트너에 대해 알아봅니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: be2ea92ddbcce7c1e0ab5ba7d172cda0e05984fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753373"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>사용자 VPN 클라이언트용 글로벌 또는 허브 기반 프로필 다운로드

Azure 가상 WAN은 원격 사용자에 대해 글로벌 및 허브 기반의 두 가지 연결 유형을 제공 합니다. 다음 섹션을 사용 하 여 프로필에 대해 알아보고 프로필을 다운로드 합니다. 

> [!IMPORTANT]
> RADIUS 인증은 허브 기반 프로필만 지원 합니다.

## <a name="global-profile"></a>전역 프로필

프로필은 모든 활성 사용자 VPN hubs를 포함 하는 부하 분산 장치를 가리킵니다. 사용자는 사용자의 지리적 위치에 가장 가까운 허브로 전송 됩니다. 이 연결 유형은 사용자가 여러 위치로 이동 하는 경우에 유용 합니다. **전역** 프로필을 다운로드 하려면:

1. 가상 WAN 탭으로 이동합니다.
2. **사용자 VPN 구성**을 클릭 합니다.
3. 프로필을 다운로드 하려는 구성을 강조 표시 합니다.
4. **가상 WAN 사용자 VPN 프로필 다운로드**를 클릭 합니다.

   ![전역 프로필](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>허브 기반 프로필

프로필은 단일 허브를 가리킵니다. 사용자는이 프로필을 사용 하 여 특정 허브에만 연결할 수 있습니다. **허브 기반** 프로필을 다운로드 하려면:

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지에서 **허브** 를 클릭 합니다.

    ![허브 프로필 1](./media/global-hub-profile/hub1.png)
3. **사용자 VPN (지점 및 사이트 간)을**클릭 합니다.
4. **가상 허브 사용자 VPN 프로필 다운로드**를 클릭 합니다.

   ![허브 프로필 2](./media/global-hub-profile/hub2.png)
5. **Eaptls**를 확인 합니다.
6. **생성 및 프로필 다운로드**를 클릭 합니다.

   ![허브 프로필 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.

---
title: Windows 가상 데스크톱 인증-Azure
description: Windows 가상 데스크톱에 대 한 인증 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500303"
---
# <a name="supported-authentication-methods"></a>지원 되는 인증 방법

이 문서에서는 Windows 가상 데스크톱에서 사용할 수 있는 인증 종류에 대 한 간략 한 개요를 제공 합니다.

## <a name="session-host-authentication"></a>세션 호스트 인증

Windows 가상 데스크톱은 세션 호스트 인증을 위해 NTLM (NT LAN Manager) 및 Kerberos를 모두 지원 합니다. 그러나 Kerberos를 사용 하려면 클라이언트가 도메인 컨트롤러에서 실행 되는 키 배포 센터 (KDC) 서비스에서 Kerberos 보안 티켓을 가져와야 합니다. 티켓을 얻으려면 클라이언트는 도메인 컨트롤러에 대 한 직접적인 시야를 요구 합니다. 회사 네트워크를 사용 하 여 직접적인 시야를 얻을 수 있습니다. 회사 네트워크에 대 한 VPN 연결을 사용할 수도 있습니다.

다음은 현재 지원 되는 로그인 방법입니다.

- Windows 데스크톱 클라이언트
    - 사용자 이름 및 암호
    - 스마트 카드
    - Windows Hello
- Windows 스토어 클라이언트
    - 사용자 이름 및 암호
- 웹 클라이언트
    - 사용자 이름 및 암호
- Android
    - 사용자 이름 및 암호
- iOS
    - 사용자 이름 및 암호
- macOS
    - 사용자 이름 및 암호

>[!NOTE]
>스마트 카드 및 Windows Hello는 Kerberos를 사용 하 여 로그인 할 수 있습니다. Kerberos를 사용 하 여 로그인 하려면 도메인 컨트롤러에 대 한 시야를 사용 해야 합니다.

## <a name="hybrid-identity"></a>하이브리드 ID

Windows 가상 데스크톱은 Active Directory Federation Services (ADFS)를 사용 하 여 페더레이션된를 포함 하 여 Azure Active Directory (AD)를 통해 [하이브리드 id](../active-directory/hybrid/whatis-hybrid-identity.md) 를 지원 합니다. 사용자가 Azure AD를 통해 검색할 수 있어야 하기 때문에 Windows 가상 데스크톱은 ADFS를 사용 하 여 독립 실행형 Active Directory 배포를 지원 하지 않습니다.

## <a name="single-sign-on-sso"></a>SSO (Single sign-on)

Windows 가상 데스크톱은 현재 SSO에 대 한 Active Directory Federation Services (ADFS)를 지원 하지 않습니다.

세션 호스트에 대 한 자격 증명을 입력 하 라는 메시지가 표시 되는 것을 방지 하는 유일한 방법은 클라이언트에 저장 하는 것입니다. 다른 사용자가 리소스에 액세스 하는 것을 방지 하기 위해 보안 장치 에서만이 작업을 수행 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

배포를 안전 하 게 유지 하는 다른 방법에 대해 궁금 하십니까? [보안 모범 사례](security-guide.md)를 확인 하세요.

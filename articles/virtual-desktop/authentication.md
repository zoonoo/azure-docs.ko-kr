---
title: Windows Virtual Desktop 인증 - Azure
description: Windows Virtual Desktop에 대한 인증 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 32e113bf02a2cbd21b70805396c212a1f9467382
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448239"
---
# <a name="supported-authentication-methods"></a>지원되는 인증 방법

이 문서에서는 Windows Virtual Desktop에서 사용할 수 있는 인증 종류에 대한 간략한 개요를 제공합니다.

## <a name="session-host-authentication"></a>세션 호스트 인증

Windows Virtual Desktop은 세션 호스트 인증을 위해 NTLM(NT LAN Manager)과 Kerberos를 모두 지원합니다. 그러나 Kerberos를 사용하려면 클라이언트가 도메인 컨트롤러에서 실행되는 KDC(키 배포 센터) 서비스에서 Kerberos 보안 티켓을 가져와야 합니다. 티켓을 가져오려면 클라이언트는 도메인 컨트롤러에 대한 직접 가시권이 있어야 합니다. 회사 네트워크를 사용하여 직접 가시권을 가져올 수 있습니다. 회사 네트워크에 대한 VPN 연결을 사용하거나 [KDC 프록시 서버](key-distribution-center-proxy.md)를 설정할 수도 있습니다.

현재 지원되는 로그인 방법은 다음과 같습니다.

- Windows 데스크톱 클라이언트
    - 사용자 이름 및 암호
    - 스마트 카드
    - 비즈니스용 Windows Hello(인증서 신뢰 전용)
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
>스마트 카드 및 비즈니스용 Windows Hello는 Kerberos만 사용하여 로그인 할 수 있습니다. Kerberos를 사용하여 로그인하려면 도메인 컨트롤러 또는 [KDC 프록시 서버](key-distribution-center-proxy.md)에 대한 직접 가시권이 필요합니다.

## <a name="hybrid-identity"></a>하이브리드 ID

Windows Virtual Desktop은 ADFS(Active Directory Federation Services)를 사용하여 페더레이션된 ID를 포함하여 Azure AD(Active Directory)를 통해 [하이브리드 ID](../active-directory/hybrid/whatis-hybrid-identity.md)를 지원합니다. 사용자는 Azure AD를 통해 검색할 수 있어야 하므로 Windows Virtual Desktop ADFS를 사용하는 독립 실행형 Active Directory 배포를 지원하지 않습니다.

## <a name="single-sign-on-sso"></a>SSO(Single Sign-On)

Windows Virtual Desktop은 현재 SSO용 ADFS(Active Directory Federation Services)를 지원하지 않습니다.

세션 호스트에 대한 자격 증명을 입력하라는 메시지가 표시되지 않도록 하는 유일한 방법은 클라이언트에 저장하는 것입니다. 다른 사용자가 리소스에 액세스하지 못하도록 보안 디바이스에서만 이 작업을 수행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

배포를 안전하게 유지하는 다른 방법이 궁금하나요? [보안 모범 사례](security-guide.md)를 확인하세요.

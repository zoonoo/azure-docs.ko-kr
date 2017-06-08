---
title: "Azure AD Connect: 통과 인증 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 및 이 통과 인증을 통해 사용자의 암호를 온-프레미스 Active Directory에 대해 유효성 검사하여 Azure AD 로그인을 허용하는 방법을 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증으로 사용자 로그인 구성

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증이란?

사용자가 동일한 자격 증명(암호)을 사용하여 온-프레미스 리소스 및 클라우드 기반 서비스에 둘 다 로그인하도록 하면 사용자와 조직에 도움이 됩니다. 사용자는 기억할 암호가 하나 줄어들므로 사용자 환경이 개선됩니다. 또한 사용자는 로그인하는 방법을 잊어버릴 가능성도 줄어듭니다. 일반적으로 암호 관련 문제에 가장 큰 지원 리소스가 소비되므로 지원 센터 비용이 감소합니다.

많은 조직에서는 사용자에게 온-프레미스 리소스 및 클라우드 기반 서비스에 걸쳐 동일한 자격 증명을 제공하는 방법으로 온-프레미스 Active Directory에서 Azure AD(Azure Active Directory)로 사용자 암호 해시를 동기화하는 [Azure AD Connect](active-directory-aadconnect.md) 기능인 [Azure AD 암호 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)를 사용합니다. 하지만 다른 조직에서는 암호가(해시된 형태인 경우에도) 내부 조직 경계를 벗어나지 않도록 요구합니다.

Azure AD 통과 인증 기능은 이러한 조직을 위한 간단한 해결 방법을 제공합니다. 사용자가 Azure AD에 로그인하면 온-프레미스 Active Directory에 대해 직접 유효성 검사되도록 합니다. 이 기능은 다음과 같은 이점을 제공합니다.

- 용이성
  - 복잡한 온-프레미스 배포 또는 네트워크 구성이 없어도 암호 유효성 검사가 수행됩니다.
  - 간단한 온-프레미스 커넥터는 암호 유효성 검사 요청을 수신하고 이에 응답합니다.
  - 온-프레미스 커넥터는 기능 향상 및 버그 수정을 자동으로 받습니다.
  - [Azure AD Connect](active-directory-aadconnect.md)와 함께 구성할 수 있습니다. 간단한 온-프레미스 커넥터가 Azure AD Connect와 같은 서버에 설치됩니다.
- 보안
  - 온-프레미스 암호가 어떤 형태로든 클라우드에 저장되지 않습니다.
  - 간단한 온-프레미스 커넥터는 네트워크 내에서만 아웃바운드 연결을 만듭니다. 따라서 DMZ라고도 하는 경계 네트워크에 커넥터를 설치할 필요가 없습니다.
  - 통과 인증은 Azure Multi-Factor Authentication과 함께 원활하게 작동합니다.
- 안정성 및 확장성
  - 로그인 요청의 고가용성을 획득하기 위해 여러 서버에 간단한 온-프레미스 커넥터를 추가로 설치할 수 있습니다.

![Azure AD 통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

통과 인증을 [Seamless SSO(Single Sign-On)](active-directory-aadconnect-sso.md) 기능에 결합할 수 있습니다. 이러한 방식으로 사용자가 회사 네트워크 내의 회사 컴퓨터에 있을 때 Azure AD에 로그인하기 위해 암호를 입력할 필요가 없으므로 진정한 통합 환경이 구현되었다고 할 수 있습니다.

## <a name="whats-available-during-preview"></a>미리 보기 중에 무엇을 사용할 수 있나요?

>[!NOTE]
>Azure AD 통과 인증은 현재 미리 보기로 제공됩니다. 무료 기능이며 이 기능을 사용하는 데는 Azure AD 유료 버전이 필요하지 않습니다.

다음 시나리오는 미리 보기 중에 완전히 지원됩니다.

- 모든 웹 브라우저 기반 응용 프로그램
- [최신 인증](https://aka.ms/modernauthga)을 지원하는 Office 365 클라이언트 응용 프로그램

다음 시나리오는 미리 보기 중에 지원되지 _않습니다_.

- 레거시 Office 클라이언트 응용 프로그램 및 Exchange ActiveSync(모바일 장치의 네이티브 메일 응용 프로그램) <br>가능할 경우 조직은 최신 인증으로 전환하는 것이 좋습니다. 이렇게 하면 통과 인증 지원이 허용되지만 Multi-Factor Authentication과 같은 [조건부 액세스](../active-directory-conditional-access.md) 기능을 사용하여 ID를 보호할 수도 있습니다.
- Windows 10 장치에 대한 Azure AD 조인.

>[!IMPORTANT]
>현재 통과 인증 기능이 지원되지 않는 시나리오(레거시 Office 클라이언트 응용 프로그램, Windows 10 장치의 Exchange ActiveSync 및 Azure AD 조인)에 대한 해결 방법으로 통과 인증을 사용하도록 설정하면 기본적으로 암호 동기화도 사용하도록 설정됩니다. 암호 동기화는 이러한 특정 시나리오에서만 대체 방법으로 사용됩니다. 이 기능이 필요하지 않으면 Azure AD Connect 마법사의 [선택적 기능](active-directory-aadconnect-get-started-custom.md#optional-features) 페이지에서 암호 동기화를 끌 수 있습니다.

### <a name="prerequisites"></a>필수 조건

Azure AD 통과 인증을 사용하도록 설정하기 전에 다음 사항이 필요합니다.

- 전역 관리자 권한이 있는 Azure AD 테넌트입니다.

  >[!NOTE]
  >클라우드 전용 계정을 전역 관리자 계정으로 사용하는 것이 좋습니다. 이러한 방식으로 온-프레미스 서비스가 실패하거나 사용할 수 없게 될 때 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 전역 관리자 계정](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다.

- Azure AD Connect 버전 1.1.486.0 이상 [Azure AD Connect 최신 버전](https://www.microsoft.com/download/details.aspx?id=47594)을 사용하는 것이 좋습니다.
- Azure AD Connect를 실행할 Windows Server 2012 R2 이상을 실행하는 서버
  - 이 서버는 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버여야 합니다.
  - 통과 인증 커넥터는 Azure AD Connect와 같은 서버에 설치됩니다. 커넥터 버전이 1.5.58.0 이상인지 확인합니다.

    >[!NOTE]
    >AD 포리스트 간에 포리스트 트러스트가 있고 이름 접미사 라우팅이 제대로 구성된 경우 다중 포리스트 환경이 지원됩니다.

- 고가용성이 필요한 경우 독립 실행형 커넥터를 설치하려면 Windows Server 2012 R2 이상을 실행하는 추가 서버가 필요합니다. (커넥터 버전은 1.5.58.0 이상이어야 합니다.)
- 커넥터와 Azure AD 사이에 방화벽이 있는 경우
    - URL 필터링이 사용되는 경우 커넥터가 다음 URL과 통신할 수 있는지 확인합니다.
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - 커넥터가 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=41653)에 대한 직접적인 IP 연결을 생성합니다.
    - 커넥터가 클라이언트 인증서를 사용하여 Azure AD와 통신하므로 방화벽이 SSL 검사를 수행하지 않는지 확인합니다.
    - 커넥터가 포트 80 및 443을 통해 Azure AD에 대해 아웃바운드 요청을 수행할 수 있는지 확인합니다.
      - 방화벽이 원래 사용자에 따라 규칙을 적용하는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다.
      - 커넥터는 포트 80을 통해 HTTP 요청을 만들어 SSL 인증서 해지 목록을 다운로드합니다. 이는 자동 업데이트 기능이 제대로 작동하는 데도 필요합니다.
      - 커넥터는 기능 활성화 및 비활성화, 커넥터 등록, 커넥터 업데이트 다운로드 및 모든 사용자 로그인 요청 처리와 같은 기타 모든 작업을 위해 포트 443을 통해 HTTPS 요청을 만듭니다.

     >[!NOTE]
     >최근에 서비스와 통신하기 위해 커넥터에 필요한 포트 수를 줄여 이 기능을 개선했습니다. 이전 버전의 Azure AD Connect 및/또는 독립 실행형 커넥터를 실행 중인 경우 해당 추가 포트(5671, 8080, 9090, 9091, 9350, 9352, 10100-10120)를 계속 열어 두어야 합니다.

### <a name="enable-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증 사용

Azure AD 통과 인증은 Azure AD Connect를 통해 사용하도록 설정될 수 있습니다.

Azure AD Connect를 새로 설치할 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **통과 인증**을 선택합니다. 성공적으로 완료되면 통과 인증 커넥터가 Azure AD Connect와 동일한 서버에 설치됩니다. 또한 테넌트에서 통과 인증 기능이 사용하도록 설정됩니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect를 이미 설치한 경우, [빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로를 사용하여 설정하고, Azure AD Connect에서 **사용자 로그인 변경 페이지**를 선택하고 **다음**을 클릭합니다. 그다음에 **통과 인증**을 선택하여 통과 인증 커넥터를 Azure AD Connect와 같은 서버에 설치하고 테넌트에서 기능을 사용하도록 설정합니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD 통과 인증은 테넌트 수준 기능입니다. 이 기능은 테넌트의 모든 관리되는 도메인에서 사용자 로그인에 영향을 줍니다. 하지만 페더레이션된 도메인의 사용자는 ADFS(Active Directory Federation Services) 또는 이전에 구성한 기타 페더레이션 공급자를 사용하여 계속 로그인합니다. 도메인을 페더레이션된 도메인에서 관리되는 도메인으로 전환하면 해당 도메인의 모든 사용자가 자동으로 통과 인증을 사용하여 로그인하기 시작합니다. 클라우드 전용 사용자는 통과 인증의 영향을 받지 않습니다.

프로덕션 배포에서 통과 인증을 사용하려면 Azure AD Connect 및 첫 번째 커넥터를 실행하는 서버가 아닌 별도의 서버에 두 번째 커넥터를 설치하는 것이 좋습니다. 이렇게 하면 로그인 요청의 고가용성이 보장됩니다. 필요한 만큼 추가 커넥터를 설치할 수 있습니다. 커넥터 수는 테넌트가 처리하는 최대 및 평균 로그인 요청 수에 따라 달라집니다.

다음 지침에 따라 독립 실행형 커넥터를 배포합니다.

#### <a name="step-1-download-and-install-the-connector"></a>1단계: 커넥터 다운로드 및 설치

이 단계에서 서버에 커넥터 소프트웨어를 다운로드하고 설치합니다.

1.    최신 커넥터를 [다운로드](https://go.microsoft.com/fwlink/?linkid=837580)합니다. 커넥터 버전이 1.5.58.0 이상인지 확인합니다.
2.    관리자 권한으로 명령 프롬프트를 엽니다.
3.    다음 명령을 실행합니다. **/q** 옵션은 “자동 설치”를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다. `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>서버당 하나의 커넥터만 설치할 수 있습니다.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>2단계: 통과 인증을 위해 커넥터를 Azure AD에 등록

이 단계에서 서버에 설치된 커넥터를 서비스에 등록하고 로그인 요청을 받을 수 있도록 설정합니다.

1.    관리자 권한으로 PowerShell 창을 엽니다.
2.    **C:\Program Files\Microsoft AAD App Proxy Connector**로 이동하고 다음과 같이 스크립트를 실행합니다. `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    메시지가 표시되면 Azure AD 테넌트 전역 관리자 계정의 자격 증명을 입력합니다.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Azure AD 통과 인증 작동 방식

사용자가 Azure AD에 로그인하려고 하고 통과 인증이 테넌트에서 사용하도록 설정되어 있으면 다음 작업이 수행됩니다.

1. 사용자가 사용자 이름과 암호를 Azure AD 로그인 페이지에 입력합니다. 서비스에서 공개 키로 암호화된 사용자 이름 및 암호를 유효성 검사를 위해 큐에 넣습니다.
2. 사용 가능한 온-프레미스 커넥터 중 하나가 큐에 대한 아웃바운드 호출을 수행하고 사용자 이름과 암호를 검색합니다.
3. 그런 다음 커넥터가 표준 Windows API(ADFS에서 사용되는 것과 비슷한 메커니즘)를 사용하여 사용자 이름과 암호를 Active Directory에 대해 유효성 검사합니다. 사용자 이름은 온-프레미스 기본 사용자 이름(일반적으로 `userPrincipalName`) 또는 Azure AD Connect에 구성된 또 다른 특성(`Alternate ID`라고 함) 중 하나일 수 있습니다.
4. 온-프레미스 도메인 컨트롤러가 요청을 평가하고 커넥터에 응답(성공 또는 실패)을 반환합니다.
5. 그러면 커넥터가 이 응답을 다시 Azure AD로 반환합니다.
6. Azure AD가 응답을 평가하고 이에 따라 사용자에게 응답합니다. 예를 들어 응용 프로그램에 대해 다시 토큰을 실행하거나 Multi-Factor Authentication을 요청합니다.

다음 다이어그램에서는 다양한 단계를 보여 줍니다. 모든 요청 및 응답은 HTTPS 채널을 통해 수행됩니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>비밀번호 쓰기 저장

테넌트 및 특정 사용자에 대해 [암호 쓰기 저장](../active-directory-passwords-update-your-own-password.md)을 구성한 경우 통과 인증으로 로그인한 사용자는 이전과 같이 암호를 변경하거나 재설정할 수 있습니다. 암호는 예상대로 온-프레미스 Active Directory에 쓰기 저장됩니다.

하지만 암호 쓰기 저장이 테넌트에서 구성되지 않거나 사용자에게 유효한 Azure AD 라이선스가 할당되어 있지 않으면 클라우드에서 암호를 업데이트할 수 없습니다. 암호가 만료된 경우도 마찬가지입니다. 대신에 다음과 같은 메시지가 사용자에게 표시됩니다. "조직이 이 사이트에서 암호 업데이트를 허용하지 않습니다. 조직에서 권장하는 방법에 따라 암호를 업데이트하거나 도움이 필요한 경우 관리자에게 문의하세요."

## <a name="next-steps"></a>다음 단계
- 테넌트에 대해 [Azure AD seamless SSO](active-directory-aadconnect-sso.md) 기능을 사용하도록 설정하는 방법을 참조하세요.
- [문제 해결 가이드](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)를 읽고 Azure AD 통과 인증과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.

## <a name="feedback"></a>사용자 의견
Microsoft는 사용자의 의견을 소중하게 생각합니다. 문의 사항이 있으면 댓글 섹션을 사용하세요. 새로운 기능을 요청하려면 [UserVoice 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)을 사용하세요.



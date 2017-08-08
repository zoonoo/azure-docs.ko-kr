---
title: "Azure AD 통과 인증 - 빠른 시작 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증을 시작하는 방법을 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 통과 인증: 빠른 시작

Azure AD(Azure Active Directory) 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 응용 프로그램 둘 다에서 동일한 암호로 로그인할 수 있습니다. 온-프레미스 Active Directory와 직접 비교해서 암호의 유효성을 검사하여 사용자를 로그인합니다.

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증을 배포하는 방법

통과 인증을 배포하려면 다음 단계를 수행해야 합니다.
1. *필수 조건 확인*: 이 기능을 사용하도록 설정하기 전에 테넌트 및 온-프레미스 환경을 올바르게 설정합니다.
2. *기능 활성화*: 테넌트에서 통과 인증을 켜고 및 암호 유효성 검사 요청을 처리할 간단한 온-프레미스 에이전트를 설치합니다.
3. *기능 테스트*: 통과 인증을 사용하여 사용자 로그인을 테스트합니다.
4. *고가용성 보장*: 로그인 요청에 대해 고가용성을 제공하기 위해 두 번째 독립 실행형 에이전트를 설치합니다.

## <a name="step-1-check-prerequisites"></a>1단계: 필수 조건 확인

다음 필수 조건이 충족되었는지 확인합니다.

### <a name="on-the-azure-portal"></a>Azure Portal에서

1. Azure AD 테넌트에서 클라우드 전용 전역 관리자 계정을 만듭니다. 이러한 방식으로 온-프레미스 서비스가 실패하거나 사용할 수 없게 될 때 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 전역 관리자 계정](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다. 테넌트에서 잠기지 않도록 하려면 이 단계를 수행하는 것이 중요합니다.
2. Azure AD 테넌트에 [사용자 지정 도메인 이름](../active-directory-add-domain.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나를 사용하여 로그인합니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. Azure AD Connect를 실행할, Windows Server 2012 R2 이상이 실행되는 서버를 식별합니다. 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트에 서버를 추가합니다.
2. 2단계에서 식별한 서버에 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)를 설치합니다. Azure AD Connect가 이미 실행되고 있는 경우 버전이 1.1.486.0 이상인지 확인합니다.
3. 독립 실행형 인증 에이전트를 실행할, Windows Server 2012 R2 이상이 실행되는 추가 서버를 식별합니다. 인증 에이전트 버전이 1.5.58.0 이상이어야 합니다. 로그인 요청의 고가용성을 보장하려면 이 서버가 필요합니다. 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트에 서버를 추가합니다.
4. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성해야 합니다.
   - 포트 열기: 서버의 인증 에이전트가 포트 80 및 443을 통해 Azure AD에 아웃바운드 요청을 수행할 수 있는지 확인합니다. 방화벽이 원래 사용자에 따라 규칙을 적용하는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다.
   - Azure AD 끝점 허용: URL 필터링이 사용되는 경우 인증 에이전트가 **\*.msappproxy.net** 및 **\*.servicebus.windows.net**과 통신할 수 있는지 확인합니다.
   - 직접적인 IP 연결 확인: 서버의 인증 에이전트가 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=41653)에 대한 직접적인 IP 연결을 생성할 수 있는지 확인합니다.

## <a name="step-2-enable-the-feature"></a>2단계: 기능 활성화

[Azure AD Connect](active-directory-aadconnect.md)를 통해 통과 인증을 사용하도록 설정할 수 있습니다.

>[!IMPORTANT]
>Azure AD Connect 주 서버나 준비 서버에서 통과 인증을 사용하도록 설정할 수 있습니다. 주 서버에서 사용하도록 설정하는 것이 좋습니다.

Azure AD Connect를 처음 설치하는 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **통과 인증**을 로그온 방법으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치됩니다. 또한 테넌트에서 통과 인증 기능이 사용됩니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect를 이미 설치한 경우([빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로 사용), Azure AD Connect에서 **사용자 로그인 변경 페이지**를 선택하고 **다음**을 클릭합니다. 그런 다음 **통과 인증**을 로그온 방법으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치되고 테넌트에서 기능이 사용됩니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>통과 인증은 테넌트 수준 기능입니다. 기능을 켜면 테넌트의 _모든_ 관리되는 도메인에서 사용자의 로그인에 영향을 줍니다.

## <a name="step-3-test-the-feature"></a>3단계: 기능 테스트

2단계 후에는 테넌트에 있는 모든 관리되는 도메인의 사용자가 통과 인증을 사용하여 로그인합니다. 하지만 페더레이션된 도메인의 사용자는 AD FS(Active Directory Federation Services) 또는 이전에 구성한 기타 페더레이션 공급자를 사용하여 계속 로그인합니다. 도메인을 페더레이션된 도메인에서 관리되는 도메인으로 전환하면 해당 도메인의 모든 사용자가 자동으로 통과 인증을 사용하여 로그인하기 시작합니다. 클라우드 전용 사용자는 통과 인증 기능의 영향을 받지 않습니다.

## <a name="step-4-ensure-high-availability"></a>4단계: 고가용성 보장

프로덕션 환경에 통과 인증을 배포하려는 경우 독립 실행형 인증 에이전트를 설치해야 합니다. 이 두 번째 인증 에이전트는 Azure AD Connect 및 첫 번째 인증 에이전트를 실행하는 서버가 아닌 _다른_ 서버에 설치합니다. 이렇게 설치하면 로그인 요청의 고가용성이 제공됩니다. 다음 지침에 따라 독립 실행형 인증 에이전트를 배포합니다.

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>서버에 인증 에이전트 소프트웨어 다운로드 및 설치

1.  최신 인증 에이전트 소프트웨어를 [다운로드](https://go.microsoft.com/fwlink/?linkid=837580)합니다. 버전이 1.5.58.0 이상인지 확인합니다.
2.  관리자 권한으로 명령 프롬프트를 엽니다.
3.  다음 명령을 실행합니다. 여기서 **/q** 옵션은 "자동 설치"를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다. `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>서버당 하나의 인증 에이전트만 설치할 수 있습니다.

### <a name="register-the-authentication-agent-with-azure-ad"></a>Azure AD에 인증 에이전트 등록

1.  관리자 권한으로 PowerShell 창을 엽니다.
2.  **C:\Program Files\Microsoft AAD App Proxy Connector**로 이동하고 다음과 같이 스크립트를 실행합니다. `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  메시지가 표시되면 Azure AD 테넌트 전역 관리자 계정의 자격 증명을 입력합니다.

## <a name="next-steps"></a>다음 단계
- [**현재 제한 사항** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 이 기능은 현재 미리 보기로 제공됩니다. 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [**기술 심층 분석**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**FAQ(질문과 대답)** ](active-directory-aadconnect-pass-through-authentication-faq.md) -질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법을 알아봅니다.
- [**Azure AD 원활한 SSO**](active-directory-aadconnect-sso.md) - 이 보완 기능에 대해 자세히 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.


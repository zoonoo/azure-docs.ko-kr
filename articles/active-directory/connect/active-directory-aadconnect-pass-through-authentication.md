---
title: "Azure AD Connect: 통과 인증 | Microsoft 문서"
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
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0f54fb7d2d8cf010baf79409bc6a528d34982500
ms.lasthandoff: 04/22/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증으로 사용자 로그인 구성

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>Azure AD(Azure Active Directory) 통과 인증이란?

사용자가 동일한 자격 증명(암호)을 사용하여 온-프레미스 리소스 및 클라우드 기반 서비스에 둘 다 로그인하도록 하면 사용자와 조직에 도움이 됩니다. 사용자는 기억할 암호가 줄어듭니다. 이에 따라 사용자 환경이 향상되고 사용자가 로그인 방법을 잊을 가능성이 감소합니다. 일반적으로 암호 관련 문제에 가장 큰 지원 리소스가 소비되므로 결국 지원 센터 비용이 감소합니다.

많은 조직에서는 사용자에게 온-프레미스 리소스 및 클라우드 기반 서비스에 걸쳐 동일한 자격 증명을 제공하는 방법으로 온-프레미스 Active Directory에서 Azure AD로 사용자 암호를 동기화하는 [Azure AD Connect](active-directory-aadconnect.md) 기능인 [Azure AD 암호 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)를 사용합니다. 하지만 다른 조직에서는 암호가(해시된 형태인 경우에도) 내부 조직 경계를 벗어나지 않도록 요구합니다.

Azure AD 통과 인증은 이러한 조직을 위한 간단한 해결 방법을 제공합니다. 사용자가 Azure AD에 로그인하면 온-프레미스 Active Directory에 대해 직접 유효성 검사되도록 합니다. 이 기능은 다음과 같은 이점을 제공합니다.

- 용이성
  - 복잡한 온-프레미스 배포 또는 네트워크 구성이 없어도 암호 유효성 검사가 수행됩니다.
  - 암호 유효성 검사 요청을 수신하고 이에 응답하는 간단한 온-프레미스 커넥터만 이용합니다.
  - [Azure AD Connect](active-directory-aadconnect.md)와 함께 구성할 수 있습니다. 간단한 온-프레미스 커넥터가 Azure AD Connect와 같은 서버에 설치됩니다.
- 보안
  - 온-프레미스 암호가 어떤 형태로든 클라우드에 저장되지 않습니다.
  - 간단한 온-프레미스 커넥터는 네트워크 내에서만 아웃바운드 연결을 만듭니다. 따라서 DMZ에 커넥터를 설치할 필요가 없습니다.
  - 통과 인증은 Azure Multi-Factor Authentication과 함께 원활하게 작동합니다.
- 안정성 및 확장성
  - 로그인 요청의 고가용성을 획득하기 위해 여러 서버에 간단한 온-프레미스 커넥터를 추가로 설치할 수 있습니다.

![Azure AD 통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

[Seamless Single Sign-on](active-directory-aadconnect-sso.md) 기능과 결합하면 사용자가 회사 네트워크 내의 회사 컴퓨터(실제로 통합 환경)에 있을 때 Azure AD에 로그인하기 위해 암호를 입력할 필요가 없습니다.

## <a name="whats-available-during-preview"></a>미리 보기 중에 무엇을 사용할 수 있나요?

>[!NOTE]
>Azure AD 통과 인증은 현재 미리 보기로 제공됩니다. 무료 기능이며 이 기능을 사용하는 데는 Azure AD 유료 버전이 필요하지 않습니다. 

다음 시나리오는 미리 보기 중에 완전히 지원됩니다.

- 모든 웹 브라우저 기반 응용 프로그램.
- [modern authentication](https://aka.ms/modernauthga)(최신 인증)을 지원하는 Office 365 클라이언트 응용 프로그램.

다음 시나리오는 미리 보기 중에 지원되지 않습니다.

- 레거시 Office 클라이언트 응용 프로그램 및 Exchange ActiveSync(모바일 장치의 네이티브 메일 응용 프로그램).
  - 가능할 경우 조직은 최신 인증으로 전환하는 것이 좋습니다. 이렇게 하면 통과 인증 지원이 허용되지만 다단계 인증과 같은 [조건부 액세스](../active-directory-conditional-access.md) 기능을 사용하여 ID를 보호할 수도 있습니다.
- Windows 10 장치에 대한 Azure AD 조인.

>[!IMPORTANT]
>현재 통과 인증이 지원되지 않는 시나리오(레거시 Office 클라이언트 응용 프로그램, Windows 10 장치의 Exchange ActiveSync 및 Azure AD 조인)에 대한 해결 방법으로 통과 인증을 사용하도록 설정하면 기본적으로 암호 동기화도 사용하도록 설정됩니다. 암호 동기화는 이러한 특정 시나리오에서만 대체 방법으로 사용됩니다. 이 기능이 필요하지 않으면 Azure AD Connect의 [선택적 기능](active-directory-aadconnect-get-started-custom.md#optional-features) 페이지에서 암호 동기화를 끌 수 있습니다.

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증을 사용하도록 설정하는 방법

### <a name="pre-requisites"></a>필수 조건

Azure AD 통과 인증을 사용하도록 설정하기 전에 다음 필수 구성 요소가 충족되어야 합니다.

- 전역 관리자 권한이 있는 Azure AD 테넌트.

>[!NOTE]
>온-프레미스 서비스가 실패하거나 사용할 수 없는 경우 테넌트의 구성을 관리할 수 있도록 전역 관리자는 클라우드 전용 계정인 것이 좋습니다. [여기](../active-directory-users-create-azure-portal.md)에 표시된 것처럼 클라우드 전용 전역 관리자 계정을 추가할 수 있습니다.

- Azure AD Connect 버전 1.1.484.0 이상. [latest version of Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)(Azure AD Connect 최신 버전)를 사용하는 것이 좋습니다.
- Azure AD Connect를 실행할 Windows Server 2012 R2 이상을 실행하는 서버.
  - 이 서버는 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버여야 합니다.
  - 커넥터는 Azure AD Connect와 같은 서버에 설치됩니다.

>[!NOTE]
>AD 포리스트 간에 포리스트 트러스트가 있고 이름 접미사 라우팅이 제대로 구성된 경우 다중 포리스트 환경이 지원됩니다.

- 고가용성이 필요한 경우 독립 실행형 커넥터를 설치하려면 Windows Server 2012 R2 이상을 실행하는 추가 서버가 필요합니다.
- 커넥터와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.
    - URL 필터링이 사용되는 경우 커넥터가 다음 URL과 통신할 수 있는지 확인합니다.
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - 커넥터가 [Azure data center IP ranges](https://www.microsoft.com/en-us/download/details.aspx?id=41653)(Azure 데이터 센터 IP 범위)에 대한 직접적인 IP 연결을 생성합니다.
    - 커넥터가 클라이언트 인증서를 사용하여 Azure AD와 통신할 때 방화벽이 SSL 검사를 수행하지 않는지 확인합니다.
    - 커넥터가 포트 80 및 443을 통해 Azure AD에 대해 HTTPS(TCP) 요청을 수행할 수 있는지 확인합니다.
      - 방화벽이 원래 사용자에 따라 규칙을 적용하는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다.

>[!NOTE]
>최근에 서비스와 통신하기 위해 커넥터에 필요한 포트 수를 줄여 이 기능을 개선했습니다. 이전 버전의 Azure AD Connect 및/또는 독립 실행형 커넥터를 실행 중인 경우 해당 추가 포트(5671, 8080, 9090, 9091, 9350, 9352, 10100-10120)를 계속 열어 두어야 합니다.

### <a name="enabling-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증 사용

Azure AD 통과 인증은 Azure AD Connect를 통해 사용하도록 설정될 수 있습니다.

Azure AD Connect를 새로 설치할 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. "사용자 로그인" 페이지에서 "통과 인증"을 선택합니다. 성공적으로 완료되면 통과 인증 커넥터가 Azure AD Connect와 동일한 서버에 설치됩니다. 또한 테넌트에서 통과 인증 기능이 사용하도록 설정됩니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect가 [빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로를 사용하여 설치되어 있으면 Azure AD Connect에서 "사용자 로그인 변경 페이지"를 선택하고 "다음"을 클릭합니다. 그다음에 "통과 인증"을 선택하여 통과 인증 커넥터를 Azure AD Connect와 같은 서버에 설치하고 테넌트에서 기능을 사용하도록 설정합니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD 통과 인증은 테넌트 수준 기능입니다. 이 기능은 테넌트의 모든 관리되는 도메인에서 사용자 로그인에 영향을 줍니다. 하지만 페더레이션된 도메인의 사용자는 ADFS(Active Directory Federation Services) 또는 이전에 구성한 기타 페더레이션 공급자를 사용하여 계속 로그인합니다. 도메인을 페더레이션된 도메인에서 관리되는 도메인으로 전환하면 해당 도메인의 모든 사용자가 자동으로 통과 인증을 사용하여 로그인하기 시작합니다. 클라우드 전용 사용자는 통과 인증의 영향을 받지 않습니다.

### <a name="ensuring-high-availability"></a>고가용성 보장

프로덕션 배포에서 통과 인증을 사용하려면 로그인 요청의 고가용성을 보장하도록 Azure AD Connect 및 첫 번째 커넥터를 실행하는 서버가 아닌 별도의 서버에 두 번째 커넥터를 설치하는 것이 좋습니다. 필요한 만큼 추가 커넥터를 설치할 수 있습니다. 커넥터 수는 테넌트가 처리하는 최대 및 평균 로그인 요청 수에 따라 달라집니다.

아래 지침에 따라 독립 실행형 커넥터를 배포합니다.

#### <a name="step-1-download-and-install-the-connector"></a>1단계: 커넥터 다운로드 및 설치

이 단계에서 서버에 커넥터 소프트웨어를 다운로드하고 설치합니다.

1.    최신 커넥터를 [다운로드](https://go.microsoft.com/fwlink/?linkid=837580)합니다.
2.    관리자 권한으로 명령 프롬프트를 엽니다.
3.    다음 명령을 실행합니다. 여기서 /q는 자동 설치를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.

`
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
3. 그다음에 커넥터가 표준 Windows API(ADFS에서 사용되는 것과 비슷한 메커니즘)를 사용하여 사용자 이름과 암호를 Active Directory에 대해 유효성 검사합니다. 사용자 이름은 온-프레미스 기본 사용자 이름(일반적으로 "userPrincipalName") 또는 Azure AD Connect에 구성된 또 다른 특성("대체 ID"라고 함) 중 하나일 수 있습니다.
4. 온-프레미스 도메인 컨트롤러가 요청을 평가하고 커넥터에 응답(성공 또는 실패)을 반환합니다.
5. 그러면 커넥터가 이 응답을 다시 Azure AD로 반환합니다.
6. Azure AD가 응답을 평가하고 이에 따라 사용자에게 응답합니다. 예를 들어 응용 프로그램에 대해 다시 토큰을 실행하거나 다단계 인증을 요청합니다.

아래 다이어그램은 다양한 단계를 보여 줍니다. 모든 요청 및 응답은 HTTPS 채널을 통해 수행됩니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>암호 쓰기 저장에 대한 메모

테넌트 및 특정 사용자에 대해 [암호 쓰기 저장](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)을 구성한 경우 통과 인증으로 로그인한 사용자는 이전과 같이 암호를 변경하거나 재설정할 수 있습니다. 암호는 예상대로 온-프레미스 Active Directory에 쓰기 저장됩니다.

하지만 이러한 조건 중 하나가 충족되지 않으면(암호 쓰기 저장이 테넌트에서 구성되지 않거나 사용자에게 유효한 Azure AD 라이선스가 할당되어 있지 않으면) 사용자는 암호가 만료된 경우를 포함하여 클라우드에서 암호를 업데이트할 수 없습니다. 대신에 다음과 같은 메시지가 사용자에게 표시됩니다. "조직이 이 사이트에서 암호 업데이트를 허용하지 않습니다. 조직에서 권장하는 방법에 따라 암호를 업데이트하거나 도움이 필요한 경우 관리자에게 문의하세요."

## <a name="troubleshooting-pass-through-authentication"></a>통과 인증 문제 해결

이 섹션에서는 Azure AD Connect 또는 독립 실행형을 통해 통과 인증 커넥터를 설치, 등록 또는 제거하는 동안 발생하는 일반적인 일부 문제에 대한 문제 해결 정보를 찾을 수 있습니다. 테넌트에서 기능을 사용하도록 설정하고 작동하는 동안에 발생하는 문제도 포함됩니다.

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>커넥터 설치 중에 발생하는 문제(Azure AD Connect 또는 독립 실행형을 통해)

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD Application Proxy 커넥터가 이미 있음

통과 인증 커넥터는 [Azure AD 응용 프로그램 프록시](../../active-directory/active-directory-application-proxy-get-started.md) 커넥터와 같은 서버에 설치할 수 없습니다. 통과 인증 커넥터를 별도의 서버에 설치해야 합니다.

#### <a name="an-unexpected-error-occured"></a>예기치 않은 오류가 발생함

서버에서 [커넥터 로그를 수집](#how-to-collect-pass-through-authentication-connector-logs?)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

### <a name="issues-during-registration-of-connectors"></a>커넥터 등록 중에 발생하는 문제

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>차단된 포트로 인해 커넥터 등록에 실패함

커넥터가 설치된 서버가 [여기](#pre-requisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

#### <a name="an-unexpected-error-occurred"></a>예기치 않은 오류가 발생함

서버에서 [커넥터 로그를 수집](#how-to-collect-pass-through-authentication-connector-logs?)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

### <a name="issues-during-un-installation-of-connectors"></a>커넥터 제거 중에 발생하는 문제

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Azure AD Connect 제거 시 경고 메시지

테넌트에서 통과 인증이 사용하도록 설정되고 Azure AD Connect를 제거하려고 하면 다음 경고 메시지가 표시됩니다. "다른 통과 인증 에이전트가 다른 서버에 설치되어 있지 않으면 사용자가 Azure AD에 로그인할 수 없습니다.".

사용자 로그인 중단을 방지하려면 Azure AD Connect를 제거하기 전에 [고가용성](#ensuring-high-availability) 설정이 포함되어 있어야 합니다.

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>통과 인증 기능 사용과 관련된 문제

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>사용 가능한 커넥터가 없어서 기능을 사용하도록 설정하지 못함

테넌트에서 통과 인증을 사용하도록 설정하려면 하나 이상의 활성 커넥터 서버가 있어야 합니다. Azure AD Connect 또는 독립 실행형 커넥터를 설치하여 커넥터를 설치할 수 있습니다.

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>차단된 포트로 인해 기능을 사용하도록 설정하지 못함

Azure AD Connect가 설치된 서버가 [여기](#pre-requisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>통과 인증 기능 작동과 관련된 문제

#### <a name="user-facing-sign-in-errors"></a>사용자용 로그인 오류

이 기능이 Azure AD 로그인 화면에서 다음 사용자용 오류를 보고합니다. 이러한 오류는 적절한 해결 단계와 함께 아래에서 자세히 설명합니다.

|오류|설명|해결 방법
| --- | --- | ---
|AADSTS80001|Active Directory에 연결할 수 없음|커넥터 서버가 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버이고 Active Directory에 연결할 수 있는지 확인합니다.  
|AADSTS8002|Active Directory에 연결하는 동안 시간 초과 발생|Active Directory를 사용할 수 있고 커넥터의 요청에 응답하는지 확인합니다.
|AADSTS80004|커넥터에 전달된 사용자 이름이 유효하지 않음|사용자가 올바른 사용자 이름을 사용하여 로그인을 시도하는지 확인합니다.
|AADSTS80005|유효성 검사 중 예측할 수 없는 WebException 발생|일시적인 오류일 수 있습니다. 요청을 다시 시도하십시오. 계속 실패할 경우 Microsoft 지원에 문의하세요.
|AADSTS80007|Active Directory와 통신 중 오류 발생|커넥터 로그에서 자세한 내용을 확인하고 Active Directory가 예상대로 작동하는지 확인합니다.

### <a name="how-to-collect-pass-through-authentication-connector-logs"></a>통과 인증 커넥터 로그를 수집하는 방법

발생하는 문제 유형에 따라 다른 위치에서 통과 인증 커넥터 로그를 검색해야 합니다.

#### <a name="connector-event-logs"></a>커넥터 이벤트 로그

커넥터 관련 오류의 경우 서버에서 이벤트 뷰어 응용 프로그램을 열고 **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**에서 확인합니다.

자세한 분석 및 디버깅 로그를 보려면 "세션" 로그를 사용하도록 설정합니다. 정상적으로 작동하는 동안에는 이 로그를 사용하도록 설정한 채 커넥터를 실행하지 마세요. 이 로그는 문제 해결에만 사용하세요. 로그 콘텐츠는 로그가 다시 사용하지 않도록 설정된 후에만 표시됩니다.

#### <a name="detailed-trace-logs"></a>자세한 추적 로그

사용자 로그인 실패 문제를 해결하려면 **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**에서 추적 로그를 검색합니다. 이러한 로그에는 통과 인증 기능을 통해 특정 사용자 로그인이 실패한 이유가 포함됩니다. 로그 항목의 예는 다음과 같습니다.

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

명령 프롬프트를 열고 다음 명령을 실행하면 오류(위 예제의 경우 '1328')에 대한 자세한 설명을 볼 수 있습니다. 참고: '1328'을 로그에 표시되는 실제 오류 번호로 바꿔야 합니다.

`Net helpmsg 1328`

결과는 다음과 같이 표시됩니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

감사 로깅이 사용되는 경우에는 도메인 컨트롤러의 보안 로그에서도 추가 정보를 찾을 수 있습니다. 통과 인증 커넥터를 통해 전송된 로그인 요청을 쿼리하는 간단한 방법은 다음과 같습니다.

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>사용자 의견

Microsoft는 사용자의 의견을 소중하게 생각합니다. [aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com)으로 메일을 보낼 수 있습니다. 새로운 기능에 대한 요청이 있는 경우 [UserVoice forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)(UserVoice 포럼)을 사용하세요. 여러분의 의견을 환영합니다.


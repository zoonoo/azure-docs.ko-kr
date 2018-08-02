---
title: Azure AD 통과 인증 - 빠른 시작 | Microsoft Docs
description: 이 문서에서는 Azure AD(Azure Active Directory) 통과 인증을 시작하는 방법을 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 280d62f127c333ff195e921de380721170fd6a96
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214985"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 통과 인증: 빠른 시작

## <a name="deploy-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증 배포

Azure AD(Azure Active Directory) 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 응용 프로그램 둘 다에서 동일한 암호로 로그인할 수 있습니다. 통과 인증은 사용자의 암호를 온-프레미스 Active Directory와 직접 비교하여 유효성을 검사하고 사용자를 로그인합니다.

>[!IMPORTANT]
>AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 경우 [여기](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx)에 게시된 자세한 배포 가이드를 따르는 것이 좋습니다.

통과 인증을 테넌트에 배포하려면 다음 지침을 따릅니다.

## <a name="step-1-check-the-prerequisites"></a>1단계: 필수 구성 요소 확인

다음 필수 조건이 충족되는지 확인합니다.

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서

1. Azure AD 테넌트에서 클라우드 전용 전역 관리자 계정을 만듭니다. 이러한 방식으로 온-프레미스 서비스가 실패하거나 사용할 수 없게 될 때 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 전역 관리자 계정 추가](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다. 테넌트에 잠기지 않도록 이 단계를 완료하는 것이 중요합니다.
2. Azure AD 테넌트에 [사용자 지정 도메인 이름](../active-directory-domains-add-azure-portal.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나로 로그인할 수 있습니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. Azure AD Connect를 실행할 수 있도록 Windows Server 2012 R2 이상을 실행 중인 서버를 찾습니다. 암호의 유효성을 검사해야 하는 사용자와 동일한 Active Directory 포리스트에 서버를 추가합니다.
2. 이전 단계에서 찾은 서버에 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)를 설치합니다. Azure AD Connect가 이미 실행되고 있는 경우 버전이 1.1.644.0 이상인지 확인합니다.

    >[!NOTE]
    >Azure AD Connect 버전 1.1.557.0, 1.1.558.0, 1.1.561.0 및 1.1.614.0에는 암호 해시 동기화와 관련된 문제가 있습니다. 암호 해시 동기화를 통과 인증과 함께 사용하지 _않으려는_ 경우 [Azure AD Connect 릴리스 정보](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470)를 참조하세요.

3. 독립 실행형 인증 에이전트를 실행할 수 있도록 Windows Server 2012 R2 이상을 실행 중인 하나 이상의 추가 서버를 찾습니다. 이러한 추가 서버는 로그인 요청의 고가용성을 보장하기 위해 필요합니다. 암호의 유효성을 검사해야 하는 사용자와 동일한 Active Directory 포리스트에 서버를 추가합니다.

    >[!IMPORTANT]
    >프로덕션 환경의 테넌트에서 실행되는 최소 3개의 인증 에이전트를 확보하는 것이 좋습니다. 테넌트당 인증 에이전트 12개로 지정된 시스템 제한이 있습니다. 모범 사례로, 인증 에이전트를 실행하는 모든 서버를 계층 0 시스템으로 처리합니다([참조](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 항목 참조).

4. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성합니다.
   - 인증 에이전트가 다음 포트를 통해 Azure AD에 대한 *아웃바운드* 요청을 만들 수 있는지 확인합니다.
   
    | 포트 번호 | 사용 방법 |
    | --- | --- |
    | **80** | SSL 인증서의 유효성을 검사하는 동안 CRL(인증서 해지 목록) 다운로드 |
    | **443** | 서비스와의 모든 아웃바운드 통신 처리 |
   
    방화벽이 원래 사용자에 따라 규칙에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 이러한 포트를 엽니다.
   - 방화벽이나 프록시에서 DNS 허용 목록을 허용하면 **\*.msappproxy.net** 및 **\*.servicebus.windows.net**에 대한 연결을 허용 목록에 추가합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다.
   - 인증 에이전트는 초기 등록을 위해 **login.windows.net** 및 **login.microsoftonline.com**에 액세스해야 합니다. 이러한 URL에 대한 방화벽도 엽니다.
   - 인증서 유효성 검사를 위해 **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** 및 **www.microsoft.com:80** URL을 차단 해제합니다. 이러한 URL은 다른 Microsoft 제품과의 인증서 유효성 검사에 사용되므로 이러한 URL을 이미 차단 해제했을 수 있습니다.

## <a name="step-2-enable-the-feature"></a>2단계: 기능 활성화

[Azure AD Connect](active-directory-aadconnect.md)를 통해 통과 인증을 사용하도록 설정합니다.

>[!IMPORTANT]
>Azure AD Connect 주 서버나 준비 서버에서 통과 인증을 사용하도록 설정할 수 있습니다. 주 서버에서 사용하도록 설정하는 것이 좋습니다.

Azure AD Connect를 처음 설치하는 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **통과 인증**을 **로그온 방법**으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치됩니다. 또한 테넌트에서 통과 인증 기능이 사용됩니다.

![Azure AD Connect: 사용자 로그인](./media/active-directory-aadconnect-sso/sso3.png)

[빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로를 사용하여 Azure AD Connect를 이미 설치한 경우 Azure AD Connect에서 **사용자 로그인 변경** 작업을 선택하고 **다음**을 선택합니다. 그런 다음 **통과 인증**을 로그온 방법으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치되고 테넌트에서 기능이 사용됩니다.

![Azure AD Connect: 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>통과 인증은 테넌트 수준 기능입니다. 기능을 켜면 테넌트의 _모든_ 관리되는 도메인에서 사용자 로그인에 영향을 줍니다. AD FS(Active Directory Federation Services)에서 통과 인증으로 전환하는 경우 적어도 12시간 이상 기다린 후 AD FS 인프라를 종료해야 합니다. 이 대기 시간은 전환하는 동안 사용자가 Exchange ActiveSync에 계속 로그인할 수 있도록 유지하기 위한 시간입니다. AD FS에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 도움말은 [여기](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx)에 게시된 자세한 배포 가이드를 확인합니다.

## <a name="step-3-test-the-feature"></a>3단계: 기능 테스트

다음 지침에 따라 통과 인증을 올바르게 설정했는지 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**를 선택합니다.
4. **통과 인증** 기능이 **사용**으로 표시되는지 확인합니다.
5. **통과 인증**을 선택합니다. **통과 인증** 창에는 인증 에이전트가 설치된 서버가 나열됩니다.

![Azure Active Directory 관리 센터: Azure AD Connect 창](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 관리 센터 - 통과 인증 창](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

이 단계에서는 테넌트에 있는 모든 관리되는 도메인의 사용자가 통과 인증을 사용하여 로그인할 수 있습니다. 하지만 페더레이션된 도메인의 사용자는 AD FS 또는 이전에 구성한 다른 페더레이션 공급자를 사용하여 계속 로그인합니다. 도메인을 페더레이션된 도메인에서 관리되는 도메인으로 전환하면 해당 도메인의 모든 사용자가 자동으로 통과 인증을 사용하여 로그인하기 시작합니다. 통과 인증 기능은 클라우드 전용 사용자에게 영향을 주지 않습니다.

## <a name="step-4-ensure-high-availability"></a>4단계: 고가용성 보장

프로덕션 환경에 통과 인증을 배포하려는 경우 독립 실행형 인증 에이전트를 추가 설치해야 합니다. 이러한 인증 에이전트를 Azure AD Connect를 실행하는 서버 _이외의_ 서버에 설치합니다. 이렇게 설치하면 사용자 로그인 요청에 대해 고가용성이 제공됩니다.

>[!IMPORTANT]
>프로덕션 환경의 테넌트에서 실행되는 최소 3개의 인증 에이전트를 확보하는 것이 좋습니다. 테넌트당 인증 에이전트 12개로 지정된 시스템 제한이 있습니다. 모범 사례로, 인증 에이전트를 실행하는 모든 서버를 계층 0 시스템으로 처리합니다([참조](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 항목 참조).

다음 지침에 따라 인증 에이전트 소프트웨어를 다운로드합니다.

1. 인증 에이전트의 최신 버전(버전 1.5.193.0 이상)을 다운로드하려면 테넌트의 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**, **통과 인증**, **에이전트 다운로드**를 차례로 선택합니다.
4. **약관 동의 및 다운로드** 단추를 선택합니다.

![Azure Active Directory 관리 센터 - 인증 에이전트 다운로드 버튼](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory 관리 센터 - 에이전트 다운로드 창](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>[인증 에이전트 소프트웨어를 직접 다운로드](https://aka.ms/getauthagent)할 수도 있습니다. 설치하기 _전에_ 인증 에이전트의 [서비스 약관](https://aka.ms/authagenteula)을 검토하고 동의합니다.

독립 실행형 인증 에이전트를 배포하는 방법에는 다음 두 가지가 있습니다.

첫째, 다운로드한 인증 에이전트 실행 파일을 실행하고, 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 제공하여 대화형으로 수행할 수 있습니다.

둘째, 무인 배포 스크립트를 만든 후 실행할 수 있습니다. 한 번에 여러 인증 에이전트를 배포하거나 사용자 인터페이스가 사용되도록 설정되지 않았거나 원격 데스크톱에 액세스할 수 없는 Windows 서버에 인증 에이전트를 설치하려는 경우에 유용합니다. 다음은 이 방식을 사용하는 방법에 대한 지침입니다.

1. 다음 명령을 실행하여 인증 에이전트를 설치합니다. `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`
2. Windows PowerShell을 사용하여 서비스에 인증 에이전트를 등록할 수 있습니다. 테넌트에 대한 전역 관리자 사용자 이름 및 암호를 포함하는 PowerShell 자격 증명 개체 `$cred`를 만듭니다. 다음 명령에서 *\<username\>* 및 *\<암호\>* 를 바꿔서 실행합니다.
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. **C:\Program Files\Microsoft Azure AD Connect Authentication Agent**로 이동하여 사용자가 만든 `$cred` 개체를 사용하여 다음 스크립트를 실행합니다.
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>다음 단계
- [AD FS에서 통과 인증으로 마이그레이션](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호하는 방법을 알아봅니다.
- [현재 제한 사항](active-directory-aadconnect-pass-through-authentication-current-limitations.md): 현재 통과 인증이 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [기술 심층 분석](active-directory-aadconnect-pass-through-authentication-how-it-works.md): 통과 인증 기능이 작동하는 원리를 이해합니다.
- [질문과 대답](active-directory-aadconnect-pass-through-authentication-faq.md): 자주 하는 질문과 대답을 살펴봅니다.
- [문제 해결](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): 통과 인증 기능에 대한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](active-directory-aadconnect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.


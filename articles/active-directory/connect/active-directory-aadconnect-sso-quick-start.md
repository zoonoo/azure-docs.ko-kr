---
title: "Azure AD Connect: Seamless Single Sign-On - 빠른 시작 | Microsoft Docs"
description: "이 문서에서는 Azure Active Directory Seamless Single Sign-On을 시작하는 방법을 설명합니다."
services: active-directory
keywords: "Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
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
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 451d4fd24dc506fb4a659edb710ab67a66cbbde7
ms.contentlocale: ko-kr
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory Seamless Single Sign-On: 빠른 시작

Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 데스크톱에 있을 때 사용자를 자동으로 서명합니다. 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 응용 프로그램에 쉽게 액세스할 수 있습니다.

## <a name="how-to-deploy-azure-ad-seamless-sso"></a>Azure AD Seamless SSO를 배포하는 방법

Seamless SSO를 배포하려면 다음 단계를 수행해야 합니다.
1. *필수 조건 확인*: 이 기능을 활성화하기 전에 테넌트 및 온-프레미스 환경을 올바르게 설정합니다.
2. *기능 활성화*: Azure AD Connect를 사용하여 테넌트의 Seamless SSO를 사용하도록 설정합니다.
3. *기능 배포*: 그룹 정책을 사용하여 일부 또는 모든 사용자에게 기능을 배포합니다.
4. *기능 테스트*: Seamless SSO를 사용하여 사용자 로그인을 테스트합니다.

## <a name="step-1-check-prerequisites"></a>1단계: 필수 구성 요소 확인

다음 필수 조건이 충족되는지 확인합니다.

1. Azure AD Connect 서버 설정: 로그인 방법으로 [통과 인증](active-directory-aadconnect-pass-through-authentication.md)을 사용하는 경우 더 이상의 작업이 필요하지 않습니다. 로그인 방법으로 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)를 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.
- Azure AD Connect 버전 1.1.484.0 이상을 사용하고 있습니다.
- Azure AD Connect 서버에서 `*.msappproxy.net` URL 및 443 포트를 통해 통신할 수 있습니다. 이 필수 조건은 실제 사용자 로그인을 위해서가 아니라 해당 기능을 사용하도록 설정한 경우에만 적용할 수 있습니다.
- Azure AD Connect는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 직접적인 IP 연결을 할 수 있습니다. 다시금 이 필수 조건은 해당 기능을 사용하도록 설정한 경우에만 적용할 수 있습니다.
2. Azure AD Connect를 통해 Azure AD와 동기화하는 각 AD 포리스트에 대해, 그리고 Seamless SSO를 사용하도록 설정할 사용자에 대해 도메인 관리자 자격 증명이 필요합니다.

## <a name="step-2-enable-the-feature"></a>2단계: 기능 활성화

Azure AD Seamless SSO는 [Azure AD Connect](active-directory-aadconnect.md)를 통해 사용하도록 설정할 수 있습니다.

Azure AD Connect를 새로 설치하는 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. "사용자 로그인" 페이지에서 "Single Sign-On 사용" 옵션을 선택합니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso8.png)

Azure AD Connect가 이미 설치되어 있는 경우 Azure AD Connect에서 "사용자 로그인 변경 페이지"를 선택하고 "다음"을 클릭합니다. 그다음에 "Single Sign-On 사용" 옵션을 선택합니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

"Single Sign-On 사용" 페이지에 도달할 때까지 마법사를 계속 진행합니다. Azure AD Connect를 통해 Azure AD와 동기화하는 각 AD 포리스트에 대해, 그리고 Seamless SSO를 사용하도록 설정할 사용자에 대해 도메인 관리자 자격 증명을 제공합니다. 

마법사를 완료하면 테넌트에서 SSO Seamless를 사용하도록 설정됩니다.

>[!NOTE]
> 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않지만 이 기능을 사용하도록 설정하는 데에만 사용됩니다.

## <a name="step-3-roll-out-the-feature"></a>3단계: 기능 배포

이 기능을 사용자에게 배포하려면 Active Directory의 그룹 정책을 통해 사용자의 인트라넷 영역 설정에 두 개의 Azure AD URL(https://autologon.microsoftazuread-sso.com 및 https://aadg.windows.net.nsatc.net)을 추가해야 합니다.

>[!NOTE]
> 다음 지침은 Windows의 Internet Explorer 및 Google Chrome(Internet Explorer와 신뢰할 수 있는 사이트 URL 집합을 공유하는 경우)에서만 작동합니다. Mac에서 Mozilla Firefox 및 Chrome을 설정하는 지침은 다음 섹션을 참조하세요.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>사용자의 인트라넷 영역 설정을 수정해야 하는 이유

기본적으로 브라우저는 URL에서 올바른 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 http://contoso/는 인트라넷 영역에 매핑되지만, http://intranet.contoso.com/은 인터넷 영역에 매핑됩니다(URL에 마침표가 포함되어 있기 때문). 브라우저는 URL이 브라우저의 인트라넷 영역에 명시적으로 추가되지 않는 한 클라우드 끝점(예: 두 개의 Azure AD URL)에 Kerberos 티켓을 보내지 않습니다.

### <a name="detailed-steps"></a>자세한 단계

1. 그룹 정책 관리 도구를 엽니다.
2. 일부 또는 모든 사용자에게 적용되는 그룹 정책을 편집합니다. 이 예에서는 **기본 도메인 정책**을 사용합니다.
3. **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page**로 이동하고 **영역에 대한 사이트 할당 목록**을 선택합니다.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)  
4. 정책을 활성화하고 대화 상자에 다음 값(Kerberos 티켓이 전달되는 Azure AD URL) 및 데이터(*1*은 인트라넷 영역을 나타냄)를 입력합니다.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> 일부 사용자가 공유 키오스크에 로그인하는 경우와 같이 이러한 사용자가 Seamless SSO를 사용하지 못하게 하려면 이전 값을 *4*로 설정합니다. 이 작업은 Azure AD URL을 [제한된 영역]에 추가하고 Seamless SSO가 항상 실패하게 됩니다.

5. **확인**을 클릭하고 **확인**을 다시 클릭합니다.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>브라우저 고려 사항

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox는 Kerberos 인증을 자동으로 수행하지 않습니다. 각 사용자는 다음 단계에 따라 Firefox 설정에 Azure AD URL을 수동으로 추가해야 합니다.
1. Firefox를 실행하고 주소 표시줄에 `about:config`를 입력합니다. 표시되는 모든 알림을 해제합니다.
2. **network.negotiate-auth.trusted-uris** 기본 설정을 검색합니다. 이 기본 설정은 Firefox의 신뢰할 수 있는 Kerberos 인증 사이트를 나열합니다.
3. 마우스 오른쪽 단추로 클릭하고 "수정"을 선택합니다.
4. 필드에서 "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net"을 입력합니다.
5. "확인"을 클릭하고 브라우저를 다시 엽니다.

>[!NOTE]
>Firefox의 개인 검색 모드에서는 Seamless SSO가 작동하지 않습니다.

#### <a name="google-chrome-on-mac"></a>Mac의 Google Chrome

Mac 및 기타 비Windows 플랫폼에 있는 Google 크롬의 경우 통합 인증을 위해 Azure AD URL을 허용 목록에 추가하는 방법에 대한 내용은 [이 문서](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)를 참조하세요.

타사 Active Directory 그룹 정책 확장을 사용하여 Mac 사용자의 Firefox 및 Google Chrome에 Azure AD URL을 배포하는 방법은 이 문서의 범위를 벗어납니다.

## <a name="step-4-test-the-feature"></a>4단계: 기능 테스트

특정 사용자에 대한 기능을 테스트하려면 다음 조건이 _모두_ 제대로 갖추어져 있는지 확인합니다.
  - 사용자가 회사 장치에 로그인하고 있습니다.
  - 장치가 이전에 AD(Active Directory) 도메인에 가입되어 있습니다.
  - 장치는 회사의 유선/무선 네트워크 또는 원격 액세스 연결(예: VPN 연결)을 통해 DC(도메인 컨트롤러)에 직접 연결되어 있습니다.
  - 그룹 정책을 사용하여 해당 사용자에게 [기능을 배포](##step-3-roll-out-the-feature)했습니다.

사용자가 암호가 아니라 사용자 이름만 입력하는 시나리오를 테스트하려면 다음을 수행합니다.
- 새 개인 브라우저 세션에서 *https://myapps.microsoft.com/*에 로그인합니다.

사용자가 사용자 이름이나 암호를 입력할 필요가 없는 시나리오를 테스트하려면 다음을 수행합니다. 
- 새 개인 브라우저 세션에서 *https://myapps.microsoft.com/contoso.onmicrosoft.com*에 로그인합니다. "*contoso*"를 테넌트의 이름으로 바꿉니다.
- 또는 새 개인 브라우저 세션에서 *https://myapps.microsoft.com/contoso.com*에 로그인합니다. "*contoso.com*"을 테넌트에서 확인된 도메인(페더레이션 도메인이 아님)으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [**기술 심층 분석**](active-directory-aadconnect-sso-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**FAQ(질문과 대답)** ](active-directory-aadconnect-sso-faq.md) -질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.


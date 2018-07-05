---
title: Azure Active Directory에서 SAML 기반 Single Sign-On 디버그 | Microsoft Docs
description: Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그합니다.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 8bb0df567fbac6e8f8a2e2f64f868b4f219e05ac
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751397"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그

[SAML(Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language)을 지원하는 Azure AD(Azure Active Directory)에서 응용 프로그램에 대한 [단일 로그온](../manage-apps/what-is-single-sign-on.md) 문제를 찾고 수정하는 방법을 알아봅니다. 

## <a name="before-you-begin"></a>시작하기 전에
[내 앱 보안 로그인 확장](../active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension)을 설치하는 것이 좋습니다. 이 브라우저 확장을 사용하면 Single Sign-On으로 문제를 해결하는 데 필요한 SAML 요청 및 SAML 응답 정보를 쉽게 수집할 수 있습니다. 확장을 설치할 수 없는 경우 이 문서에서는 확장이 설치되거나 설치되지 않았을 때의 문제를 해결하는 방법을 보여줍니다.

내 앱 보안 로그인 확장을 다운로드하고 설치하려면 다음 링크 중 하나를 사용합니다.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On 테스트

AAD 및 대상 응용 프로그램 간 SAML 기반 Single Sign-On을 테스트하려면

1.  전역 관리자 또는 응용 프로그램을 관리할 권한이 있는 다른 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  왼쪽 창에서 **Azure Active Directory**를 클릭한 다음, **엔터프라이즈 응용 프로그램**을 클릭합니다. 
3.  Enterprise 응용 프로그램 목록에서 Single Sign-On을 테스트하려는 응용 프로그램을 클릭한 다음, 왼쪽의 옵션에서 **Single sign-on**을 클릭합니다.
4.  SAML 기반 Single Sign-On 테스트 환경을 열려면 **도메인 및 URL** 섹션에서 **SAML 설정 테스트**를 클릭합니다. SAML 설정 테스트 단추가 회색으로 되어 있으면 먼저 필수 특성을 채우고 저장해야 합니다.
5.  **Single Sign-On 테스트** 블레이드에서 대상 응용 프로그램에 로그인하려면 회사 자격 증명을 사용합니다. 현재 사용자 또는 다른 사용자로 로그인할 수 있습니다. 다른 사용자로 로그인하는 경우 인증을 요구하는 메시지가 나타납니다.

    ![SAML 테스트 페이지](media/active-directory-saml-debugging/testing.png)


성공적으로 로그인하면 테스트가 통과된 것입니다. 이 경우 Azure AD는 응용 프로그램에 SAML 응답 토큰을 발급합니다. 응용 프로그램은 SAML 토큰을 사용해 성공적으로 로그인했습니다.

회사 로그인 페이지 또는 응용 프로그램 페이지에 오류가 있는 경우 해당 오류를 해결하려면 다음 섹션 중 하나를 사용합니다.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>회사 로그인 페이지에서 로그인 오류 해결

로그인하려 할 때 회사 로그인 페이지에 오류가 표시될 수 있습니다. 

![로그인 오류](media/active-directory-saml-debugging/error.png)

이 오류를 디버그하려면 오류 메시지 및 SAML 요청이 필요합니다. 내 앱 보안 로그인 확장은 자동으로 이 정보를 수집하고 Azure AD에 해결 지침을 표시합니다. 

내 앱 보안 로그인 확장 설치로 로그인 오류를 해결하려면

1.  오류가 발생하면 확장은 Azure AD **Single Sign-On 테스트** 블레이드로 리디렉션됩니다. 
2.  **Single Sign-On 테스트** 블레이드에서 **SAML 요청 다운로드**를 클릭합니다. 
3.  SAML 요청의 오류 및 값에 따른 특정 해결 지침이 표시되어야 합니다. 지침을 검토합니다.

내 앱 보안 로그인 확장을 설치하지 않고 로그인 오류를 해결하려면

1. 페이지의 오른쪽 아래 모서리에서 오류 메시지를 복사합니다. 오류 메시지에는 다음이 포함됩니다.
    - CorrelationID 및 TimeStamp. 이러한 값은 엔지니어가 문제를 식별하고 문제에 대한 정확한 해결책을 제공하는 데 유용하므로 Microsoft를 통해 지원 사례를 만들 때 중요합니다.
    - 문제의 근본 원인을 확인하는 명령문입니다.
2.  Azure AD로 다시 돌아가 **Single Sign-On 테스트** 블레이드를 찾습니다.
3.  위의 **해결 지침 가져오기** 텍스트 상자에서 오류 메시지를 붙여넣습니다.
3.  **해결 지침 가져오기**를 클릭하여 문제 해결을 위한 단계를 표시합니다. 이 지침은 SAML 요청 또는 SAML 응답에서 정보를 요구할 수 있습니다. 내 앱 보안 로그인 확장을 사용하지 않는 경우 SAML 요청 및 응답을 검색하려면 [Fiddler](http://www.telerik.com/fiddler) 같은 도구가 필요할 수 있습니다.
4.  SAML 요청의 대상이 Azure Active Directory에서 가져온 SAML Single Sign-On 서비스 URL에 해당하는지 확인합니다.
5.  SAML 요청의 발급자가 Azure Active Directory의 응용 프로그램에 대해 구성한 것과 동일한 식별자인지 확인입니다. Azure AD는 사용자 디렉터리에서 응용 프로그램을 찾는 데 발급자를 사용합니다.
6.  AssertionConsumerServiceURL은 응용 프로그램이 Azure Active Directory에서 SAML 토큰을 수신하려는 위치인지 확인합니다. Azure Active Directory에서 이 값을 구성할 수 있지만 SAML 요청에 포함된 경우에는 필수적이지 않습니다.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>응용 프로그램 페이지에서 로그인 오류 해결

성공적으로 로그인한 다음, 응용 프로그램 페이지에 오류가 표시될 수 있습니다. Azure AD가 응용 프로그램에 토큰을 발급했지만 응용 프로그램이 응답을 적용하지 않는 경우에 발생합니다.   

오류를 해결하려면:

1. 응용 프로그램이 Azure AD 갤러리에 있는 경우 Azure AD와 응용 프로그램의 통합을 위한 모든 단계를 따랐는지 확인합니다. 응용 프로그램에 대한 통합 지침을 찾으려면 [SaaS 응용 프로그램 통합 자습서 목록](../saas-apps/tutorial-list.md)을 참조합니다.
2. SAML 응답을 검색합니다.
    - 내 앱 보안 로그인 확장이 설치된 경우 **Single Sign-On 테스트** 블레이드에서 **SAML 응답 다운로드**를 클릭합니다.
    - 확장이 설치되지 않은 경우 [Fiddler](http://www.telerik.com/fiddler) 같은 도구를 사용하여 SAML 응답을 검색합니다. 
3. SAML 응답 토큰에서 이러한 요소를 확인합니다.
    - NameID 값과 형식의 사용자 고유 식별자
    - 토큰에서 발급된 클레임
    - 토큰에 서명하는 데 사용된 인증서입니다. SAML 응답을 검토하는 방법에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](active-directory-single-sign-on-protocol-reference.md)을 참조합니다.
4. SAML 응답에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](active-directory-single-sign-on-protocol-reference.md)을 참조합니다.
5. SAML 응답을 검토했으므로 문제 해결에 대한 지침은 [로그인한 후 응용 프로그램 페이지의 오류](../application-sign-in-problem-application-error.md)를 참조합니다. 
6. 아직 성공적으로 로그인할 수 없는 경우 SAML 응답에서 누락된 것을 응용 프로그램 공급업체에 요청할 수 있습니다.


## <a name="next-steps"></a>다음 단계
Single Sign-On이 응용 프로그램에 대해 작동하므로 [SaaS 응용 프로그램에 대한 사용자 프로비전 및 프로비전 해제를 자동화](../active-directory-saas-app-provisioning.md)하거나 [조건부 액세스를 시작](../active-directory-conditional-access-azure-portal-get-started.md)할 수 있습니다.



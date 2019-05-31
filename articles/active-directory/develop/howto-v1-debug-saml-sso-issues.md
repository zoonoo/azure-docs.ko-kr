---
title: Azure Active Directory에서 SAML 기반 Single Sign-On 디버그 | Microsoft Docs
description: Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그합니다.
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a06aeee1a7d9635d625edbeb2ff57a514a33a84
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962723"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그

[SAML(Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language)을 지원하는 Azure AD(Azure Active Directory)에서 애플리케이션에 대한 [단일 로그온](../manage-apps/what-is-single-sign-on.md) 문제를 찾고 수정하는 방법을 알아봅니다. 

## <a name="before-you-begin"></a>시작하기 전에

[내 앱 보안 로그인 확장](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)을 설치하는 것이 좋습니다. 이 브라우저 확장 쉽게 single sign on을 사용 하 여 문제를 해결 하 고 SAML 요청 해야 하는 SAML 응답 정보를 수집 합니다. 확장을 설치할 수 없는 경우 이 문서에서는 확장이 설치되거나 설치되지 않았을 때의 문제를 해결하는 방법을 보여줍니다.

내 앱 보안 로그인 확장을 다운로드하고 설치하려면 다음 링크 중 하나를 사용합니다.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On 테스트

SAML 기반 single sign-on 테스트 Azure AD 사이 대상 응용 프로그램:

1. 전역 관리자 또는 애플리케이션을 관리할 권한이 있는 다른 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 블레이드에서 선택 **Azure Active Directory**를 선택한 후 **Enterprise 응용 프로그램**합니다. 
1. 엔터프라이즈 응용 프로그램의 목록에서 응용 프로그램에서 single sign-on을 테스트 하려는 선택한 다음 왼쪽 선택 옵션 중에서 선택 **Single sign on**합니다.
1. SAML 기반 single sign-on 테스트 환경을 열려면로 이동 **single sign-on 테스트** (5 단계). 경우는 **테스트** 단추는 회색, 확장 및 필수 특성이 저장 먼저 입력 해야 합니다 **기본 SAML 구성을** 섹션입니다.
1. **Single Sign-On 테스트** 블레이드에서 대상 애플리케이션에 로그인하려면 회사 자격 증명을 사용합니다. 현재 사용자 또는 다른 사용자로 로그인할 수 있습니다. 다른 사용자로 로그인하는 경우 인증을 요구하는 메시지가 나타납니다.

    ![SAML 테스트 페이지](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


성공적으로 로그인하면 테스트가 통과된 것입니다. 이 경우 Azure AD는 애플리케이션에 SAML 응답 토큰을 발급합니다. 애플리케이션은 SAML 토큰을 사용해 성공적으로 로그인했습니다.

회사 로그인 페이지 또는 애플리케이션 페이지에 오류가 있는 경우 해당 오류를 해결하려면 다음 섹션 중 하나를 사용합니다.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>회사 로그인 페이지에서 로그인 오류 해결

에 로그인 하려고 할 때 나타날 수 있습니다 오류가 회사 로그인 페이지에 다음 예제와 유사 합니다.

![로그인 오류](./media/howto-v1-debug-saml-sso-issues/error.png)

이 오류를 디버그하려면 오류 메시지 및 SAML 요청이 필요합니다. 내 앱 보안 로그인 확장은 자동으로 이 정보를 수집하고 Azure AD에 해결 지침을 표시합니다. 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>내 앱 보안 로그인 확장을 사용 하 여 로그인 오류를 해결 하려면 설치

1. 오류가 발생 하는 경우 확장 리디렉션됩니다 다시 Azure AD로 **single sign-on 테스트** 블레이드입니다. 
1. 에 **single sign-on 테스트** 블레이드에서 **SAML 요청을 다운로드**합니다. 
1. SAML 요청의 오류 및 값에 따른 특정 해결 지침이 표시되어야 합니다.
1. 표시 됩니다는 **해결** 구성 문제를 해결 하려면 Azure AD에 자동으로 업데이트 하는 단추입니다. 이 단추에 표시 되지 않으면 다음 로그인 문제가 되지 않으면 Azure AD에 잘못 된 구성으로 인해 합니다.

로그인 오류에 대 한 해결 방법이 없습니다 제공 하는 경우에 사용 하 여 피드백 텍스트 상자에 알려 주세요 하는 것이 좋습니다.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치 하지 않고 오류를 해결 하려면

1. 페이지의 오른쪽 아래 모서리에서 오류 메시지를 복사합니다. 오류 메시지에는 다음이 포함됩니다.
    - CorrelationID 및 TimeStamp. 이러한 값은 엔지니어가 문제를 식별하고 문제에 대한 정확한 해결책을 제공하는 데 유용하므로 Microsoft를 통해 지원 사례를 만들 때 중요합니다.
    - 문제의 근본 원인을 확인하는 명령문입니다.
1. Azure AD로 다시 돌아가 **Single Sign-On 테스트** 블레이드를 찾습니다.
1. 위의 **해결 지침 가져오기** 텍스트 상자에서 오류 메시지를 붙여넣습니다.
1. **해결 지침 가져오기**를 클릭하여 문제 해결을 위한 단계를 표시합니다. 이 지침은 SAML 요청 또는 SAML 응답에서 정보를 요구할 수 있습니다. 와 같은 도구 들 내 앱 보안 로그인 확장을 사용 하지 않는 경우 해야 할 수 있습니다 [Fiddler](https://www.telerik.com/fiddler) SAML 요청 및 응답을 검색 하려면.
1. SAML Single Sign-on 서비스 URL에 Azure AD에서 가져온 대상 SAML 요청에 해당 하는지 확인 합니다.
1. SAML 요청의 발급자가 Azure AD에서 응용 프로그램에 대해 구성한 동일한 식별자를 확인 합니다. Azure AD는 사용자 디렉터리에서 애플리케이션을 찾는 데 발급자를 사용합니다.
1. AssertionConsumerServiceURL는 응용 프로그램에서 Azure AD에서 SAML 토큰을 받을 예상 위치를 확인 합니다. Azure AD에서이 값을 구성할 수 있습니다 하지만 필수적이 지 않습니다 SAML 요청의 일부인 경우.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>애플리케이션 페이지에서 로그인 오류 해결

성공적으로 로그인한 다음, 애플리케이션 페이지에 오류가 표시될 수 있습니다. Azure AD가 애플리케이션에 토큰을 발급했지만 애플리케이션이 응답을 적용하지 않는 경우에 발생합니다.   

이 오류를 해결 하려면 다음이 단계를 수행 합니다.

1. Azure AD 갤러리에서 응용 프로그램을 Azure AD를 사용 하 여 응용 프로그램을 통합 하기 위한 모든 단계를 수행한를 확인 합니다. 애플리케이션에 대한 통합 지침을 찾으려면 [SaaS 애플리케이션 통합 자습서 목록](../saas-apps/tutorial-list.md)을 참조합니다.
1. SAML 응답을 검색합니다.
    - 내 앱 보안 로그인 확장이 설치된 경우 **Single Sign-On 테스트** 블레이드에서 **SAML 응답 다운로드**를 클릭합니다.
    - 확장이 설치되지 않은 경우 [Fiddler](https://www.telerik.com/fiddler) 같은 도구를 사용하여 SAML 응답을 검색합니다. 
1. SAML 응답 토큰에서 이러한 요소를 확인합니다.
   - NameID 값과 형식의 사용자 고유 식별자
   - 토큰에서 발급된 클레임
   - 토큰에 서명하는 데 사용된 인증서입니다. 

     SAML 응답에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](single-sign-on-saml-protocol.md)을 참조합니다.

1. SAML 응답을 검토 한 했으므로 참조 [에 로그인 한 후 응용 프로그램 페이지에 대 한 오류](../manage-apps/application-sign-in-problem-application-error.md) 문제를 해결 하는 방법에 대 한 지침에 대 한 합니다. 
1. 여전히 성공적으로 로그인 할 수 없는 경우 SAML 응답에서 빠진 부분은 무엇 응용 프로그램 공급 업체 요청할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제는 응용 프로그램에서 single sign-on 작업을 할 수 있습니다. [사용자 프로 비전 및 SaaS 응용 프로그램에 프로 비전 해제 자동화](../manage-apps/user-provisioning.md) 하거나 [조건부 액세스 시작](../conditional-access/app-based-conditional-access.md)합니다.

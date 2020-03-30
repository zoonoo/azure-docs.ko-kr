---
title: Azure AD 응용 프로그램 갤러리에 앱 나열 | 마이크로 소프트 문서
description: Azure Active Directory 앱 갤러리에서 Single Sign-On을 지원하는 애플리케이션을 나열하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154970"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

이 문서에서는 Azure Active Directory(Azure AD) 응용 프로그램 갤러리에 응용 프로그램을 나열하고, SSO(단일 사인온)를 구현하고, 목록을 관리하는 방법을 보여 주며,

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리란?

- 고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.
- 애플리케이션 구성이 간단하고 최소화됩니다.
- 갤러리에서 빠른 검색에 애플리케이션을 제공합니다.
- 무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.
- [SCIM(교차](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)도메인 ID 관리)을 위해 시스템을 사용하는 고객은 동일한 앱에 대해 프로비전을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 페더레이션된 응용 프로그램(개방형 ID 및 SAML/WS-Fed)의 경우 응용 프로그램은 Azure AD 앱 갤러리에 나열하기 위한 서비스형 소프트웨어(SaaS) 모델을 지원해야 합니다. 엔터프라이즈 갤러리 응용 프로그램은 특정 고객이 아닌 여러 고객 구성을 지원해야 합니다.
- 개방형 ID 연결의 경우 응용 프로그램이 다중 테넌트여야 하며 응용 프로그램에 대해 [Azure AD 동의 프레임워크를](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 올바르게 구현해야 합니다. 사용자는 모든 고객이 응용 프로그램에 대한 동의를 제공할 수 있도록 로그인 요청을 공통 끝점으로 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.
- SAML 2.0/WS-Fed의 경우 응용 프로그램에 SP 또는 IDP 모드에서 SAML/WS-Fed SSO 통합을 수행할 수 있는 기능이 있어야 합니다. 요청을 제출하기 전에 이 기능이 제대로 작동하는지 확인합니다.
- 암호 SSO의 경우 단일 사인온을 예상대로 작동하도록 암호 보관을 수행할 수 있도록 응용 프로그램이 양식 인증을 지원하는지 확인합니다.
- 두 명 이상의 사용자가 등록된 테스트를 위해 영구 계정이 필요합니다.

**개발자를 위한 Azure AD를 받는 방법은 무엇입니까?**

모든 프리미엄 Azure AD 기능을 갖춘 무료 테스트 계정을 90일 동안 무료로 받을 수 있으며, 개발 작업을 수행하는 한 연장할 수 있습니다.https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>포털에서 요청 제출

응용 프로그램 통합이 Azure AD와 작동하는지 테스트한 후 응용 [프로그램 네트워크 포털에서](https://microsoft.sharepoint.com/teams/apponboarding/Apps)액세스 요청을 제출합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 그렇지 않은 경우 Outlook 또는 핫메일과 같은 Microsoft 계정을 사용하여 로그인합니다.

로그인한 후 다음 페이지가 나타나면 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오. 요청을 제출하는 데 사용할 이메일 계정을 제공합니다. Azure AD 팀은 Microsoft 응용 프로그램 네트워크 포털에 계정을 추가합니다.

![SharePoint 포털에서 요청 메시지에 액세스](./media/howto-app-gallery-listing/errorimage.png)

계정을 추가한 후 Microsoft 응용 프로그램 네트워크 포털에 로그인할 수 있습니다.

로그인한 후 다음 페이지가 나타나면 텍스트 상자에 액세스해야 하는 비즈니스 근거를 제공합니다. 그런 다음 **액세스 요청 을**선택합니다.

  ![SharePoint 포털의 비즈니스 정당화 상자](./media/howto-app-gallery-listing/accessrequest.png)

팀이 세부 정보를 검토하고 적절하게 액세스 권한을 부여합니다. 요청이 승인되면 포털에 로그인하고 홈 페이지에서 **ISV(요청 제출)** 타일을 선택하여 요청을 제출할 수 있습니다.

![홈 페이지에 요청(ISV) 타일 제출](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>포털에 로그인할 때의 문제

로그인하는 동안이 오류가 표시되는 경우 문제에 대한 세부 정보이며 이 문제를 해결할 수있는 방법입니다.

* 아래 와 같이 로그인이 차단된 경우:

  ![갤러리에서 응용 프로그램을 해결하는 문제](./media/howto-app-gallery-listing/blocked.png)

**무슨 일이죠:**

게스트 사용자는 Azure AD인 홈 테넌트에 페더레이션됩니다. 게스트 사용자는 위험이 높습니다. Microsoft는 고위험 사용자가 리소스에 액세스하는 것을 허용하지 않습니다. 모든 고위험 사용자(직원 또는 게스트/공급업체)는 Microsoft 리소스에 액세스하기 위해 위험을 수정/닫아야 합니다. 게스트 사용자의 경우 이 사용자 위험은 홈 테넌트에서 발생하며 정책은 리소스 테넌트(이 경우 Microsoft)에서 발생합니다.
 
**안전한 솔루션:**

* MFA에 등록된 게스트 사용자는 자신의 사용자 위험을 수정합니다. 이 작업은 게스트 사용자가 보안 암호 변경 또는 재설정(홈https://aka.ms/sspr) 테넌트에서 MFA 및 SSPR필요)을 수행하여 수행할 수 있습니다. 보안 암호 변경 또는 재설정은 온프레미가 아닌 Azure AD에서 시작해야 합니다.

* 게스트 사용자는 관리자가 위험을 해결합니다. 이 경우 관리자는 암호 재설정(임시 암호 생성)을 수행합니다. ID 보호가 필요하지 않습니다. 게스트 사용자의 관리자가 '비밀번호 재설정'을 클릭할 https://aka.ms/RiskyUsers 수 있습니다.

* 게스트 사용자는 관리자가 위험을 닫고 해제할 수 있습니다. 다시 말하지만 ID 보호가 필요하지 않습니다. 관리자는 '사용자 https://aka.ms/RiskyUsers 위험 해제'를 클릭할 수 있습니다. 그러나 관리자는 사용자 위험을 닫기 전에 이것이 거짓 긍정 위험 평가인지 확인하기 위해 실사를 수행해야 합니다. 그렇지 않으면 조사 없이 위험 평가를 억제하여 자신과 Microsoft의 리소스를 위험에 빠뜨리고 있습니다.

> [!NOTE]
> 액세스에 문제가 있는 경우 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오.

## <a name="implement-sso-by-using-the-federation-protocol"></a>페더레이션 프로토콜을 사용하여 SSO 구현

Azure AD 앱 갤러리에 애플리케이션을 나열하려면 먼저 Azure AD에서 지원하는 다음 페더레이션 프로토콜 중 하나를 구현해야 합니다. 또한 Azure AD 응용 프로그램 갤러리 이용 약관에 동의해야 합니다. [이 웹 사이트에서](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)Azure AD 응용 프로그램 갤러리의 이용 약관을 읽어보십시오.

- **OpenID Connect**: Open ID Connect 프로토콜을 사용하여 응용 프로그램을 Azure AD와 통합하려면 [개발자의 지침을](v1-authentication-scenarios.md)따릅니다.

    ![갤러리에 OpenID 연결 응용 프로그램 나열](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect를 사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 그림과 같이 **OpenID 연결 & OAuth 2.0을** 선택합니다.
    * 액세스에 문제가 있는 경우 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오.

- **SAML 2.0** 또는 **WS-Fed**: 앱이 SAML 2.0을 지원하는 경우 사용자 지정 [응용 프로그램을 추가하는 지침에](../active-directory-saas-custom-apps.md)따라 Azure AD 테넌트와 직접 통합할 수 있습니다.

  ![갤러리에 SAML 2.0 또는 WS-Fed 응용 프로그램 나열](./media/howto-app-gallery-listing/saml.png)

  * **SAML 2.0** 또는 **WS-Fed를**사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 그림과 같이 **SAML 2.0/WS-Fed를** 선택합니다.

  * 액세스에 문제가 있는 경우 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오.

## <a name="implement-sso-by-using-the-password-sso"></a>암호 SSO를 사용하여 SSO 구현

HTML 로그인 페이지가 있는 웹 애플리케이션을 만들어서 [암호 기반 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다.

![갤러리에 암호 SSO 응용 프로그램 나열](./media/howto-app-gallery-listing/passwordsso.png)

* 암호 SSO를 사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 그림과 같이 **암호 SSO를** 선택합니다.
* 액세스에 문제가 있는 경우 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오.

## <a name="request-for-user-provisioning"></a>사용자 프로비저닝 요청

사용자 프로비저닝을 요청하려면 다음 이미지에 표시된 프로세스를 따르십시오.

   ![사용자 프로비저닝 요청](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>기존 목록 업데이트 또는 제거

Azure AD 앱 갤러리에서 기존 응용 프로그램을 업데이트하거나 제거하려면 먼저 [응용 프로그램 네트워크 포털에서](https://microsoft.sharepoint.com/teams/apponboarding/Apps)요청을 제출해야 합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 그렇지 않은 경우 Outlook 또는 핫메일과 같은 Microsoft 계정을 사용하여 로그인합니다.

- 다음 이미지와 같이 적절한 옵션을 선택합니다.

    ![갤러리에 SAML 응용 프로그램 나열](./media/howto-app-gallery-listing/updateorremove.png)

    * 기존 응용 프로그램을 업데이트하려면 요구 사항에 따라 적절한 옵션을 선택합니다.
    * Azure AD 앱 갤러리에서 기존 응용 프로그램을 제거하려면 **갤러리에서 내 응용 프로그램 목록 제거를**선택합니다.
    * 액세스에 문제가 있는 경우 [Azure AD SSO 통합 팀에](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)문의하십시오.

## <a name="list-requests-by-customers"></a>고객의 요청 목록

고객은 고객이 새 요청을 제출하여 **앱 요청을** > 선택하여 응용 프로그램 목록을 나열하는 요청을 제출할 수**있습니다.**

![고객이 요청한 앱 타일 표시](./media/howto-app-gallery-listing/customer-submit-request.png)

다음은 고객이 요청한 응용 프로그램의 흐름입니다.

![고객이 요청한 앱 흐름 표시](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>타임라인

갤러리에 SAML 2.0 또는 WS-Fed 응용 프로그램을 나열하는 프로세스의 일정은 영업일 7~10일입니다.

  ![갤러리에 SAML 응용 프로그램을 나열하기 위한 타임라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에 OpenID Connect 응용 프로그램을 나열하는 프로세스의 일정은 영업일 2~5일입니다.

  ![갤러리에 OpenID 연결 응용 프로그램을 나열하는 타임라인](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>에스컬레이션

에스컬레이션의 경우 [Azure AD SSO 통합 팀에](mailto:SaaSApplicationIntegrations@service.microsoft.com) SaaSApplicationIntegrations@service.microsoft.com전자 메일을 보내면 가능한 한 빨리 응답할 것입니다.

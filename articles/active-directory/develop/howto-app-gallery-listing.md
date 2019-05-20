---
title: Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열 | Microsoft Docs
description: Azure Active Directory 앱 갤러리에서 Single Sign-On을 지원하는 애플리케이션을 나열하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 005e5c92a16760d8eec5dc37526f4b1f2dbd751c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540540"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>방법: Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열

이 문서에서는 Azure AD 응용 프로그램 갤러리에서 응용 프로그램 목록에서 single sign-on (SSO)를 구현 하 고 목록을 관리 하는 방법을 보여 줍니다.

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리란?

- 고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.
- 애플리케이션 구성이 간단하고 최소화됩니다.
- 갤러리에서 빠른 검색에 애플리케이션을 제공합니다.
- 무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.
- SCIM를 사용하는 고객은 동일한 앱에 프로비전을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 페더레이션 애플리케이션(Open ID 및 SAML/WS-Fed)의 경우 애플리케이션은 Azure AD 갤러리에 나열된 SaaS 모델을 지원해야 합니다. 엔터프라이즈 갤러리 애플리케이션은 특정 고객이 아닌 여러 고객 구성을 지원해야 합니다.

- Open ID Connect의 경우 애플리케이션은 다중 테넌트화되어야 하며, [Azure AD 동의 프레임워크](consent-framework.md)는 애플리케이션에 대해 올바르게 구현되어야 합니다. 모든 고객이 애플리케이션에 동의를 제공할 수 있도록 사용자가 공통 엔드포인트에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.

- SAML 2.0/WS-Fed의 경우 SP 또는 IDP 모드에서 SAML/WS-Fed SSO 통합을 수행하는 기능이 애플리케이션에 있어야 합니다. 요청을 제출하기 전에 제대로 작동하는지 확인하세요.

- 암호 SSO의 경우 암호 보관을 수행하여 Single Sign-On이 예상대로 작동할 수 있도록 애플리케이션이 폼 인증을 지원하는지 확인하세요.

- 자동 사용자 프로비전 요청의 경우 SAML 2.0/WS-Fed를 사용하여 Single Sign-On 기능이 활성화된 갤러리에 애플리케이션이 나열되어야 합니다. 아직 나열되어 있지 않은 경우 포털에서 SSO 및 사용자 프로비저닝을 함께 요청할 수 있습니다.

>[!NOTE]
>에서는 포털에서 새 요청을 가져오는 중지 되므로 많은 수의 SCIM 커넥터 요청을 사용 하 여 실행. 추가 공지가 있을 때까지 요청 기다려 주세요. 에서는 이러한 지연 시간 및이 일으킨 불편 정말 죄송 합니다.

## <a name="submit-the-request-in-the-portal"></a>포털에서 요청 제출

애플리케이션 통합이 Azure AD에서 작동하는지 테스트한 후에 [애플리케이션 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps) 액세스에 대한 요청을 제출합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 계정이 없으면 Microsoft 계정(예: Outlook 또는 Hotmail)을 사용하여 로그인합니다.

로그인 후 다음 페이지가 표시되면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하고 요청을 제출하는 데 사용할 메일 계정을 제공하세요. 그러면 Azure AD 팀에서 Microsoft Application Network Portal에 계정을 추가합니다.

![SharePoint 포털에서 액세스 요청](./media/howto-app-gallery-listing/errorimage.png)

계정이 추가되면 Microsoft Application Network Portal에 로그인할 수 있습니다.

로그인 후 다음 페이지가 표시되면 텍스트 상자에서 액세스해야 하는 비즈니스 근거를 입력한 다음, **액세스 요청**을 선택합니다.

  ![SharePoint 포털에서 액세스 요청](./media/howto-app-gallery-listing/accessrequest.png)

팀이 세부 정보를 검토하고 적절하게 액세스 권한을 부여합니다. 요청이 승인되면 포털에 로그인하여 홈페이지의 **요청 제출(ISV)** 타일 양식을 클릭하여 요청을 제출할 수 있습니다.

![SharePoint 포털 홈페이지](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

## <a name="implementing-sso-using-federation-protocol"></a>페더레이션 프로토콜을 사용하여 SSO 구현

Azure AD 앱 갤러리에 애플리케이션을 나열하려면 먼저 Azure AD에서 지원하는 다음 페더레이션 프로토콜 중 하나를 구현하고 Azure AD 애플리케이션 갤러리 사용 약관에 동의해야 합니다. Azure AD 애플리케이션 갤러리의 사용 약관은 [여기](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)에서 확인하세요.

- **OpenID Connect**: Open ID Connect 프로토콜을 사용하여 Azure AD를 통해 애플리케이션을 통합하려면 [개발자 지침](authentication-scenarios.md)을 수행합니다.

    ![갤러리에 OpenID Connect 애플리케이션을 나열하는 타임라인](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 위와 같이 **OpenID Connect 및 OAuth 2.0**을 선택합니다.
    * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요. 

- **SAML 2.0** 또는 **WS-Fed**: 앱이 SAML 2.0을 지원하는 경우 [사용자 지정 애플리케이션을 추가하는 지침](../active-directory-saas-custom-apps.md)을 사용하여 Azure AD 테넌트와 직접 통합할 수 있습니다.

  ![갤러리에 SAML 2.0 또는 WS-Fed 애플리케이션을 나열하는 타임라인](./media/howto-app-gallery-listing/saml.png)

  * **SAML 2.0** 또는 **WS-Fed**를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 위와 같이 **SAMl 2.0/WS-Fed**를 선택합니다.
  * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

## <a name="implementing-sso-using-password-sso"></a>암호 SSO를 사용하여 SSO 구현

HTML 로그인 페이지가 있는 웹 애플리케이션을 만들어서 [암호 기반 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다.

![갤러리에 암호 SSO 애플리케이션을 나열하는 타임라인](./media/howto-app-gallery-listing/passwordsso.png)

* 암호 SSO를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 위와 같이 **암호 SSO**를 선택합니다.
* 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

## <a name="updateremove-existing-listing"></a>기존 목록 업데이트/제거

Azure AD 앱 갤러리에서 기존 애플리케이션을 업데이트 또는 제거하려면 먼저 [애플리케이션 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 요청을 제출해야 합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 계정이 없으면 Microsoft 계정(예: Outlook 또는 Hotmail)을 사용하여 로그인합니다.

- 다음 이미지에 표시된 대로 적절한 옵션을 선택합니다.

    ![SAML 애플리케이션을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/updateorremove.png)

    * 기존 애플리케이션을 업데이트하려는 경우 **기존 애플리케이션 목록 업데이트**를 선택합니다.
    * Azure AD 갤러리에서 기존 애플리케이션을 제거하려는 경우 **기존 애플리케이션 목록 제거**를 선택합니다.
    * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요. 

## <a name="listing-requests-by-customers"></a>고객 요청 나열

고객을 클릭 하 여 응용 프로그램을 나열 요청을 제출할 수 있습니다 **고객이 응용 프로그램 요청** -> **새 요청을 제출**합니다.

![고객 요청 된 앱 타일](./media/howto-app-gallery-listing/customer-submit-request.png)

고객의 흐름에 요청 된 응용 프로그램-다음과 같습니다.

![고객 요청 앱 흐름](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>타임라인

갤러리에서 SAML 2.0 또는 WS-Fed 애플리케이션을 나열하는 프로세스의 타임라인은 영업일을 기준으로 7~10일입니다.

   ![SAML 애플리케이션을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에서 OpenID Connect 애플리케이션을 나열하는 프로세스의 타임라인은 영업일을 기준으로 2~5일입니다.

   ![SAML 애플리케이션을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>에스컬레이션

에스컬레이션은 [Azure AD SSO 통합 팀](mailto:SaaSApplicationIntegrations@service.microsoft.com)(SaaSApplicationIntegrations@service.microsoft.com)으로 이메일을 보내주세요. 가능한 빨리 대응하겠습니다.

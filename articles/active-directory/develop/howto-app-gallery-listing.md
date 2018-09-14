---
title: Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열 | Microsoft Docs
description: Azure Active Directory 앱 갤러리에서 Single Sign-On을 지원하는 응용 프로그램을 나열하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: d5c00e9df9c1bfee0c665cafc763c52a36f98052
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345848"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열


##  <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리란?

Azure AD(Azure Active Directory)는 클라우드 기반 ID 서비스입니다. [Azure AD 응용 프로그램 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)는 모든 응용 프로그램 커넥터가 SSO(Single Sign-On) 및 사용자 프로비전을 위해 게시되는 Azure Marketplace 앱 스토어에 위치합니다. Azure AD를 ID 공급자로 사용하는 고객은 여기에서 게시된 다른 SaaS 응용 프로그램 커넥터를 찾습니다. IT 관리자는 앱 갤러리의 커넥터를 추가한 다음, SSO(Single Sign-On) 및 프로비전을 위해 커넥터를 구성하고 사용합니다. Azure AD는 SSO(Single Sign-On)를 위해 SAML 2.0, OpenID Connect, OAuth 및 WS-Fed와 같은 모든 주요 페더레이션 프로토콜을 지원합니다.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>갤러리에 응용 프로그램을 나열하면 어떤 이점이 있나요?

*  고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.

*  응용 프로그램 구성이 간단하고 최소화됩니다.

*  갤러리에서 빠른 검색에 응용 프로그램을 제공합니다.

*  무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.

*  상호 고객을 위한 단계별 구성 자습서가 있습니다.

*  SCIM를 사용하는 고객은 동일한 앱에 프로비전을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 페더레이션 응용 프로그램(Open ID 및 SAML/WS-Fed)의 경우 응용 프로그램은 Azure AD 갤러리에 나열된 SaaS 모델을 지원해야 합니다. 엔터프라이즈 갤러리 응용 프로그램은 특정 고객이 아닌 여러 고객 구성을 지원해야 합니다.

- Open ID Connect의 경우 응용 프로그램은 다중 테넌트화되어야 하며, [Azure AD 동의 프레임워크](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework)는 응용 프로그램에 대해 올바르게 구현되어야 합니다. 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 사용자가 공통 엔드포인트에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.

- SAML 2.0/WS-Fed의 경우 SP 또는 IDP 모드에서 SAML/WS-Fed SSO 통합을 수행하는 기능이 응용 프로그램에 있어야 합니다. 요청을 제출하기 전에 제대로 작동하는지 확인하세요.

- 암호 SSO의 경우 암호 보관을 수행하여 Single Sign-On이 예상대로 작동할 수 있도록 응용 프로그램이 폼 인증을 지원하는지 확인하세요.

- 자동 사용자 프로비전 요청의 경우 응용 프로그램이 위에서 설명한 페더레이션 프로토콜 중 하나를 사용하여 Single Sign-On 기능이 활성화된 갤러리에 나열되어야 합니다. 아직 나열되어 있지 않은 경우 포털에서 SSO 및 사용자 프로비저닝을 함께 요청할 수 있습니다.

##  <a name="implementing-sso-using-federation-protocol"></a>페더레이션 프로토콜을 사용하여 SSO 구현

Azure AD 앱 갤러리에 응용 프로그램을 나열하려면 먼저 Azure AD에서 지원하는 다음 페더레이션 프로토콜 중 하나를 구현하고 Azure AD 응용 프로그램 갤러리 사용 약관에 동의해야 합니다.  Azure AD 응용 프로그램 갤러리의 사용 약관은 [여기](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/)에서 확인하세요.

*   **OpenID Connect**: Open ID Connect 프로토콜을 사용하여 Azure AD를 통해 응용 프로그램을 통합하려면 [개발자 지침](authentication-scenarios.md)을 수행합니다.

    ![갤러리에 OpenID Connect 응용 프로그램을 나열하는 타임라인](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect를 사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 위와 같이 **OpenID Connect 및 OAuth 2.0**을 선택합니다.

    * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요. 

*   **SAML 2.0** 또는 **WS-Fed**: 앱이 SAML 2.0을 지원하는 경우 [사용자 지정 응용 프로그램을 추가하는 지침](../manage-apps/configure-single-sign-on-non-gallery-applications.md)을 사용하여 Azure AD 테넌트와 직접 통합할 수 있습니다.

    ![갤러리에 SAML 2.0 또는 WS-Fed 응용 프로그램을 나열하는 타임라인](./media/howto-app-gallery-listing/saml.png)

    * **SAML 2.0** 또는 **WS-Fed**를 사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 위와 같이 **SAMl 2.0/WS-Fed**를 선택합니다.

    * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

## <a name="implementing-sso-using-password-sso"></a>암호 SSO를 사용하여 SSO 구현

HTML 로그인 페이지가 있는 웹 응용 프로그램을 만들어서 [암호 기반 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 응용 프로그램에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다.

![갤러리에 암호 SSO 응용 프로그램을 나열하는 타임라인](./media/howto-app-gallery-listing/passwordsso.png)

* 암호 SSO를 사용하여 갤러리에 나열할 응용 프로그램을 추가하려면 위와 같이 **암호 SSO**를 선택합니다.

* 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

##  <a name="updateremove-existing-listing"></a>기존 목록 업데이트/제거

Azure AD 앱 갤러리에서 기존 응용 프로그램을 업데이트 또는 제거하려면 먼저 [응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 요청을 제출해야 합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 계정이 없으면 Microsoft 계정(예: Outlook 또는 Hotmail)을 사용하여 로그인합니다.

* 아래 이미지에서 적절한 옵션 선택

    ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/updateorremove.png)

    * 기존 응용 프로그램을 업데이트하려는 경우 **기존 응용 프로그램 목록 업데이트**를 선택합니다.

    * Azure AD 갤러리에서 기존 응용 프로그램을 제거하려는 경우 **기존 응용 프로그램 목록 제거**를 선택합니다.

    * 액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요. 

## <a name="submit-the-request-in-the-portal"></a>포털에서 요청 제출

응용 프로그램 통합이 Azure AD에서 작동하는지 테스트한 후에 [응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps) 액세스에 대한 요청을 제출합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 계정이 없으면 Microsoft 계정(예: Outlook 또는 Hotmail)을 사용하여 로그인합니다.

로그인한 후에 다음 페이지가 표시됩니다. 텍스트 상자에서 액세스해야 하는 비즈니스 근거를 입력한 다음, **액세스 요청**을 선택합니다. 팀이 세부 정보를 검토하고 적절하게 액세스 권한을 부여합니다. 그 후에는 포털에 로그인하여 응용 프로그램에 대한 세부 요청을 제출할 수 있습니다.

액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

![SharePoint 포털에서 액세스 요청](./media/howto-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>타임라인
    
갤러리에서 SAML 2.0 또는 WS-Fed 응용 프로그램을 나열하는 프로세스의 타임라인은 영업일을 기준으로 7~10일입니다.

   ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에서 OpenID Connect 응용 프로그램을 나열하는 프로세스의 타임라인은 영업일을 기준으로 2~5일입니다.

   ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/timeline2.png)

사용자 프로비저닝 지원을 통해 갤러리에 응용 프로그램을 나열하는 프로세스에 대한 타임라인은 40-45 영업일입니다.

   ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>에스컬레이션

에스컬레이션은 [Azure AD SSO 통합 팀](mailto:SaaSApplicationIntegrations@service.microsoft.com)(SaaSApplicationIntegrations@service.microsoft.com)으로 이메일을 보내주세요. 가능한 빨리 대응하겠습니다.
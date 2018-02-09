---
title: "Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열"
description: "Azure Active Directory 갤러리에서 Single Sign-On을 지원하는 응용 프로그램을 나열하는 방법 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열


##  <a name="what-is-azure-ad-app-gallery"></a>Azure AD 앱 갤러리란?

Azure AD는 클라우드 기반 ID 서비스입니다. [Azure AD 앱 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)는 모든 응용 프로그램 커넥터가 SSO(Single Sign-On) 및 사용자 프로비전을 위해 게시되는 공통 저장소입니다. Azure AD를 ID 공급자로 사용하는 상호 고객은 여기에 게시된 다른 SaaS 응용 프로그램 커넥터를 찾습니다. IT 관리자는 앱 갤러리의 커넥터를 추가하여 SSO(Single Sign-On) 및 프로비전을 위해 구성하고 사용합니다. Azure AD는 SSO(Single Sign-On)를 위해 SAML 2.0, OpenID Connect, OAuth 및 WS-Fed와 같은 모든 주요 페더레이션 프로토콜을 지원합니다. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>갤러리에 응용 프로그램을 나열하면 어떤 이점이 있나요?

*  고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.

*  응용 프로그램 구성이 간단하고 최소화됩니다.

*  고객이 갤러리에서 응용 프로그램을 검색하여 찾을 수 있습니다. 

*  Azure AD SKU Free, Basic 또는 Premium에 상관없이 모든 고객이 통합을 사용할 수 있습니다.

*  상호 고객을 위한 단계별 구성 자습서가 있습니다.

*  SCIM을 사용하는 경우 동일한 앱에 대해 사용자 프로비전을 사용하도록 설정합니다.


##  <a name="what-are-the-pre-requisites"></a>필수 조건은 무엇인가요?

Azure AD 갤러리에 응용 프로그램을 나열하려면 먼저 Azure AD에서 지원하는 페더레이션 프로토콜 중 하나를 응용 프로그램에서 구현해야 합니다. Azure AD 응용 프로그램 갤러리의 사용 약관은 여기에서 읽어보십시오. 사용하는 항목: 

*   **OpenID Connect** - Azure AD에서 다중 테넌트 응용 프로그램을 만들고 응용 프로그램에 [Azure AD 승인 프레임워크](active-directory-integrating-applications.md#overview-of-the-consent-framework)를 구현합니다. 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 공통 엔드포인트에 로그인 요청을 보냅니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 고객 사용자 액세스를 제어할 수 있습니다. 응용 프로그램과 Azure AD를 통합하려면 [개발자 지침](active-directory-authentication-scenarios.md)을 수행합니다.

*   **SAML 2.0 또는 WS-Fed** – SP 또는 IDP 모드에서 SAML/WS-Fed SSO 통합을 수행하는 기능이 응용 프로그램에 있어야 합니다. SAML 2.0을 지원하는 앱은 [사용자 지정 응용 프로그램을 추가하기 위해 다음 지침](../active-directory-saas-custom-apps.md)을 사용하여 Azure AD 테넌트로 직접 통합될 수 있습니다.

*   **암호 SSO** – HTML 로그인 페이지가 있는 웹 응용 프로그램을 만들어서 [암호 기반 SSO(Single Sign-On)](../active-directory-appssoaccess-whatis.md)를 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 응용 프로그램에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에 유용합니다. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>포털에서 요청을 제출하는 프로세스

Azure AD와의 응용 프로그램 통합을 테스트한 후에는 [응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 액세스 요청을 제출해야 합니다. Office 365 계정이 있는 경우 이 계정을 사용하여 포털에 로그인할 수 있고 그렇지 않은 경우에는 Microsoft ID(Live ID, Outlook, Hotmail 등)를 사용하여 로그인할 수 있습니다. 액세스를 요청하려면 다음 페이지를 참조하세요. 텍스트 상자에 비즈니스 근거를 입력하고 **액세스 요청**을 클릭합니다. 팀에서 세부 정보를 모두 검토하고 적절한 권한을 부여합니다. 그 후에는 포털에 로그인하여 응용 프로그램에 대한 세부 요청을 제출할 수 있습니다.

액세스 관련 문제가 발생하면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

![SharePoint 포털에서 액세스 요청](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>타임라인
    
*   SAML 2.0 또는 WS-Fed 응용 프로그램을 갤러리 목록에 올리는 프로세스 - **영업일 기준 7-10일**

   ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/active-directory-app-gallery-listing/timeline.png)

*   OpenID Connect 응용 프로그램을 갤러리 목록에 올리는 프로세스 - **영업일 기준 2-5일**

   ![SAML 응용 프로그램을 갤러리 목록에 올리는 타임라인](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>에스컬레이션

에스컬레이션을 하려면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 이메일을 보내 주세요. 최대한 신속하게 연락 드리겠습니다.


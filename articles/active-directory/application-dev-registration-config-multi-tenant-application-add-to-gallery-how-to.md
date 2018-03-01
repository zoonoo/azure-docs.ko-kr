---
title: "Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 추가하는 방법 | Microsoft Docs"
description: "Azure AD 응용 프로그램 갤러리에서 사용자 지정 개발 다중 테넌트 응용 프로그램을 나열하는 방법을 설명합니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 추가하는 방법

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리란?

Azure AD는 클라우드 기반 ID 서비스입니다. [Azure AD 앱 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)는 모든 응용 프로그램 커넥터가 SSO(Single Sign-On) 및 사용자 프로비전을 위해 게시되는 공통 저장소입니다. Azure AD를 ID 공급자로 사용하는 상호 고객은 여기에 게시된 다른 SaaS 응용 프로그램 커넥터를 찾습니다. IT 관리자는 앱 갤러리의 커넥터를 추가하여 SSO(Single Sign-On) 및 프로비전을 위해 구성하고 사용합니다. Azure AD는 SSO(Single Sign-On)를 위해 SAML 2.0, OpenID Connect, OAuth 및 WS-Fed와 같은 모든 주요 페더레이션 프로토콜을 지원합니다. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>응용 프로그램이 SAML 또는 OpenIDConnect를 지원하는 경우
Azure AD 응용 프로그램 갤러리에 나열하려는 다중 테넌트 응용 프로그램이 있는 경우 먼저 응용 프로그램이 다음 Single Sign-On 기술 중 하나를 지원하는지 확인해야 합니다.

1. **OpenID Connect** - Azure AD에서 다중 테넌트 응용 프로그램을 만들고 응용 프로그램에 [Azure AD 승인 프레임워크](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)를 구현합니다. 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 공통 엔드포인트에 로그인 요청을 보냅니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 고객 사용자 액세스를 제어할 수 있습니다. 이 [문서](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)에 설명된 대로 응용 프로그램을 제출하세요.

2. **SAML** - 응용 프로그램에서 SAML 2.0을 지원하는 경우, 갤러리에서 응용 프로그램을 나열할 수 있습니다. 지침은 [여기](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)에 나열됩니다.

응용 프로그램에서 이러한 Single Sign-On 모드 중 하나를 지원하고 Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 나열하려는 경우 [이](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) 문서에 설명된 단계를 수행할 수 있습니다. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>응용 프로그램이 SAML 또는 OpenIDConnect를 지원하지 않는 경우
응용 프로그램이 이러한 모드 중 하나를 지원하지 않더라도 암호 Single Sign-On 기술을 사용하여 갤러리에 통합할 수 있습니다.

**암호 SSO** – HTML 로그인 페이지가 있는 웹 응용 프로그램을 만들어서 [암호 기반 SSO(Single Sign-On)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis)를 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 응용 프로그램에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에 유용합니다. 

이 기술을 통해 응용 프로그램을 나열하려는 경우, [이](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) 문서에 설명된 대로 요청을 제출하세요.

## <a name="escalations"></a>에스컬레이션

에스컬레이션을 하려면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 이메일을 보내 주세요. 최대한 신속하게 연락 드리겠습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램을 나열하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

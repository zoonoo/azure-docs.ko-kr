---
title: Azure Active Directory에 대해 인증된 AppSource 가져오는 방법 | Microsoft Docs
description: Azure Active Directory에 대해 인증된 응용 프로그램 AppSource 가져오는 방법에 대한 세부 정보.
services: active-directory
documentationcenter: ''
author: skwan
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2016
ms.author: skwan;bryanla

---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-(ad)"></a>Azure Active Directory (AD)에 대해 인증된 AppSource 가져오는 방법
Azure AD에 대한 AppSource 인증을 받으려면 응용 프로그램은 OpenID Connect, OAuth 2.0 또는 SAML 2.0 프로토콜을 사용하여 Azure AD로 다중 테넌트 로그인 패턴을 구현해야 합니다. 

Azure AD 로그인 또는 다중 테넌트 응용 프로그램 개발에 대해 잘 알지 못하는 경우 다음을 수행합니다.

1. [Azure AD의 인증 시나리오에서 웹앱 시나리오에 대한 브라우저][AAD-Auth-Scenarios-Browser-To-WebApp]에 대해 읽기로부터 시작합니다. 
2. 다음으로, 로그인을 구현하고 도우미 코드 샘플을 포함한 Azure AD [웹 응용 프로그램 빠른 시작 가이드][AAD-QuickStart-Web-Apps]를 확인합니다. 
3. Azure AD로 다중 테넌트 로그인 패턴을 구현하는 방법을 알아보려면 [다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure Active Directory (AD) 사용자를 로그인하는 방법][AAD-Howto-Multitenant-Overview]를 확인합니다.

## <a name="related-content"></a>관련 콘텐츠
Azure AD 로그인을 지원하는 응용 프로그램 빌드에 대한 자세한 내용을 알아보거나 도움말 및 지원을 얻으려면 [Azure AD 개발자 가이드][AAD-Dev-Guide]를 참조하세요.

아래 DISQUS 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->













<!--HONumber=Oct16_HO2-->



---
title: "Azure Active Directory에 대해 인증된 AppSource 가져오는 방법 | Microsoft Docs"
description: "Azure Active Directory에 대해 인증된 응용 프로그램 AppSource 가져오는 방법에 대한 세부 정보."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: skwan;bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: e1299c1f7f8a31f7034fc0736fcd9d66153a9758
ms.openlocfilehash: 3290a375963bc3e625cbdb05b5f9686e8cfb34f6
ms.contentlocale: ko-kr
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Azure Active Directory (AD)에 대해 인증된 AppSource 가져오는 방법
Azure AD에 대한 AppSource 인증을 받으려면 응용 프로그램에서 OpenID Connect 또는 OAuth 2.0 프로토콜을 사용하여 Azure AD로 다중 테넌트 로그인 패턴을 구현해야 합니다.  

Azure AD 로그인 또는 다중 테넌트 응용 프로그램 개발에 대해 잘 알지 못하는 경우 다음을 수행합니다.

1. 먼저 [Azure AD 인증 시나리오의 웹앱 시나리오에 대한 브라우저][AAD-Auth-Scenarios-Browser-To-WebApp]에 대해 알아봅니다.  
2. 다음으로 로그인을 구현하는 방법을 보여 주는 Azure AD [웹 응용 프로그램 빠른 시작 가이드][AAD-QuickStart-Web-Apps]를 확인하고 도우미 코드 샘플을 포함합니다.

   > [!TIP]
   > 몇 분 안에 Azure Active Directory를 실행할 수 있는 새로운 [개발자 포털](https://identity.microsoft.com/Docs/Web) 의 미리 보기를 사용해 보세요.  개발자 포털은 앱을 등록하고 코드에 Azure AD를 통합하는 과정을 안내합니다.  이 과정을 완료하면 테넌트에서 사용자를 인증할 수 있는 간단한 응용 프로그램 및 토큰을 수락하고 유효성 검사를 수행할 수 있는 백 엔드가 생성됩니다.
   >
   >
3. Azure AD로 다중 테넌트 로그인 패턴을 구현하는 방법을 알아보려면 [다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure AD(Active Directory) 사용자를 로그인하는 방법][AAD-Howto-Multitenant-Overview]을 확인합니다.

## <a name="related-content"></a>관련 콘텐츠
Azure AD 로그인을 지원하는 응용 프로그램 빌드에 대한 자세한 내용을 알아보거나 도움말 및 지원을 얻으려면 [Azure AD 개발자 가이드][AAD-Dev-Guide]를 참조하세요.

아래 Disqus 설명 섹션을 사용하면 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#guides


<!--Image references-->


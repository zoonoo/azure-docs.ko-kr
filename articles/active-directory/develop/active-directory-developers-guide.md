---
title: "개발자용 Azure Active Directory | Microsoft Docs"
description: "이 문서에서는 Azure Active Directory를 사용하여 Microsoft 회사 및 학교 계정에 로그인하는 방법의 개요를 제공합니다."
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1614e37693adad268965e586f919a842c8f42e6a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-for-developers"></a>개발자용 Azure Active Directory
Azure AD(Azure Active Directory)는 개발자가 Microsoft 회사 또는 학교 계정으로 사용자에게 안전하게 로그인하도록 허용하는 클라우드 ID 서비스입니다. 이 설명서는 산업 표준 프로토콜인 OAuth 및 OpenID Connect를 사용하여 응용 프로그램에 Azure AD 지원을 추가하는 방법을 보여줍니다.

| | |
| --- | --- |
|[인증 기본 사항](active-directory-authentication-scenarios.md) | Azure AD를 사용하는 인증에 대한 소개입니다. |
|[응용 프로그램 유형](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD에서 지원하는 인증 시나리오에 대한 개요입니다. |                                
                                                                              
## <a name="get-started"></a>시작
다음 안내 설정을 통해 Microsoft 인증 라이브러리를 사용하여 Azure AD 사용자를 로그인하는 과정을 안내합니다.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![모바일 데스크톱 앱](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />모바일 데스크톱 앱</center> | [개요](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![웹앱](./media/active-directory-developers-guide/Web_app.png)<br />웹앱</center> | [개요](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![단일 페이지 앱](./media/active-directory-developers-guide/SPA.png)<br />단일 페이지 앱</center> | [개요](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [개요](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![서비스 간](./media/active-directory-developers-guide/Service_App.png)<br />서비스 간</center> | [개요](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 클라이언트 자격 증명](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="how-to-guides"></a>방법 가이드
다음 가이드는 Azure AD로 일반적인 작업을 수행하는 방법을 알려줍니다.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[응용 프로그램 등록](active-directory-integrating-applications.md)           | Azure AD에서 응용 프로그램을 등록하는 방법입니다. |
|[다중 테넌트 응용 프로그램](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft 회사 계정에 로그인하는 방법입니다. |
|[OAuth 및 OpenID Connect 프로토콜](active-directory-protocols-openid-connect-code.md)| Microsoft 인증 프로토콜을 사용하여 사용자를 로그인하고 웹 API를 호출하는 방법입니다. |
|[추가 가이드](active-directory-developers-guide-index.md#guides)        |  Azure AD에서 사용할 수 있는 가이드 목록입니다.   |

## <a name="reference-topics"></a>참조 항목
다음 문서는 API, 프로토콜 메시지 및 Azure AD에서 사용되는 용어에 대한 자세한 정보를 제공합니다.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [인증 라이브러리(ADAL)](active-directory-authentication-libraries.md)   | Azure AD에서 제공하는 라이브러리 및 SDK에 대한 개요입니다. |
| [코드 샘플](active-directory-code-samples.md)                                  | 모든 Azure AD 코드 샘플의 목록입니다. |
| [용어](active-directory-dev-glossary.md)                                      | 이 설명서에 사용된 용어 및 단어에 대한 정의입니다. |
| [추가 참조 항목](active-directory-developers-guide-index.md#reference)| Azure AD에서 사용할 수 있는 참조 항목 목록입니다.   |


> [!NOTE]
> Microsoft 개인 계정에 로그인해야 하는 경우 [Azure AD v2.0 엔드포인트](active-directory-appmodel-v2-overview.md)를 사용하는 것이 좋습니다. Azure AD v2.0 끝점은 Microsoft 개인 계정과 Microsoft 회사 계정(Azure AD의)을 단일 인증 시스템으로 통합한 것입니다.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
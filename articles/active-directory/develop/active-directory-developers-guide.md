---
title: "개발자용 Azure Active Directory | Microsoft Docs"
description: "이 문서에서는 Azure Active Directory를 사용하여 Microsoft 회사 및 학교 계정에 로그인하는 방법의 개요를 제공합니다."
services: active-directory
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 375fdf142f7ecf5881876858178f663f516a6a71
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017

---
# <a name="azure-active-directory-for-developers"></a>개발자용 Azure Active Directory
Azure Active Directory는 개발자가 사용자를 Microsoft에서 지원하는 회사 또는 학교 계정에 안전하게 로그인할 수 있게 도와주는 클라우드 ID 서비스입니다.  여기 설명서는 산업 표준 인증 프로토콜 OAuth 및 OpenID Connect를 사용하여 응용 프로그램에 Azure AD 지원을 추가하는 방법을 보여줍니다.

| | |
| --- | --- |
|[인증 기본 사항](active-directory-authentication-scenarios.md) | Azure AD 인증 소개 |
|[응용 프로그램 유형](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD에서 지원하는 인증 시나리오의 개요 |                                
                                                                              
## <a name="get-started"></a>시작
다음 안내식 설치는 인증 라이브러리를 사용하여 Azure Active Directory 사용자를 로그인하는 과정을 안내합니다.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![모바일 및 데스크톱 앱](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />모바일 및 데스크톱 앱</center> | [개요](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [개요](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![단일 페이지 앱](./media/active-directory-developers-guide/SPA.png)<br />단일 페이지 앱</center> | [개요](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript]((https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [개요](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![서비스 간](./media/active-directory-developers-guide/Service_App.png)<br />서비스 간</center> | [개요](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 클라이언트 자격 증명](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>가이드
다음 문서는 Azure Active Directory로 일반 작업을 수행하는 방법을 알려줍니다.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[앱 등록](active-directory-integrating-applications.md)           | Azure AD에서 앱을 등록하는 방법 |
|[다중 테넌트 앱](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft 작업 계정에 로그인하는 방법 |
|[OAuth 및 OpenID Connect](active-directory-protocols-openid-connect-code.md)| 최신 인증 프로토콜을 사용하여 사용자를 로그인하고 Web API를 호출하는 방법 |
|[가이드 더 보기...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>참조
다음 문서는 API, 프로토콜 메시지 및 Azure Active Directory에서 사용되는 용어에 대한 자세한 정보를 제공합니다.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [인증 라이브러리(ADAL)](active-directory-authentication-libraries.md)   | Azure AD에서 제공하는 라이브러리 및 SDK의 개요 |
| [코드 샘플](active-directory-code-samples.md)                                  | 모든 Azure AD 코드 샘플의 목록 |
| [용어](active-directory-dev-glossary.md)                                      | 이 설명서에서 사용하는 용어 및 단어의 정의 |
| [참조 자료 더 보기...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>도움말 및 지원
Azure Active Directory 개발과 관련하여 도움을 받기에 가장 좋은 장소입니다.

|  |  
|---|
|[Stack Overflow`azure-active-directory` 및 `adal` 태그](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Azure Active Directory에 대한 피드백](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|

<br />

> [!NOTE]
> Microsoft 개인 계정에 로그인해야 하는 경우 [Azure AD v2.0 끝점](active-directory-appmodel-v2-overview.md)을 사용하는 방법을 고려해 볼 수 있습니다.  Azure AD v2.0 끝점은 Microsoft 계정과 Microsoft 회사 계정(Azure AD의)을 단일 인증 시스템으로 통합한 것입니다.


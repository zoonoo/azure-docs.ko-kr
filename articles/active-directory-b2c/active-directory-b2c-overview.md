---
title: 클라우드 ID 관리 웹앱 및 모바일 앱 개발 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 소비자 지향 응용 프로그램 개발
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6385a4e4b89cf889b71db6e09010dfe3a674336a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: 등록 및 로그인에 대해 걱정할 필요 없이 앱에 초점 맞추기

Azure AD B2C는 웹 및 모바일 응용 프로그램을 위한 클라우드 ID 관리 솔루션입니다. 수억 개의 ID로 확장되는 고가용성 글로벌 서비스입니다. 엔터프라이즈급 보안 플랫폼에 기반하여 Azure AD B2C는 응용 프로그램, 비즈니스 및 고객을 보호합니다.

Azure AD B2C를 사용하면 최소 구성으로 응용 프로그램을 인증할 수 있습니다.

* **소셜 계정**(예: Facebook, Google, LinkedIn 등)
* **엔터프라이즈 계정**(OpenID Connect 또는 SAML 등의 개방형 표준 프로토콜 사용)
* **로컬 계정**(이메일 주소 및 암호 또는 사용자 이름 및 암호)

## <a name="get-started"></a>시작하기

먼저,[Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다.

그 다음, 응용 프로그램 개발 시나리오를 선택합니다.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![모바일 및 데스크톱 앱](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />모바일 및 데스크톱 앱</center> | [개요](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [개요](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![단일 페이지 앱](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />단일 페이지 앱</center> | [개요](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Web API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [.NET Web API 호출](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>새로운 기능

여기를 다시 종종 확인하여 Azure Active Directory B2C에 대한 이후 변경 내용을 알아봅니다. 또한 @AzureAD를 사용하여 업데이트에 대해 트윗합니다.

* 이제 "기본 제공 정책"(일반 공급) 외에 ["사용자 지정 정책"](active-directory-b2c-overview-custom.md) 기능을 공개 미리 보기에서 사용할 수 있습니다.  사용자 지정 정책은 ID 전문가가 ID 경험에 대한 조합을 제어해야 합니다.
* 이제 [액세스 토큰](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) 기능을 공개 미리 보기에서 사용할 수 있습니다.
* [유럽 기반 Azure AD B2C의 일반 공급](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/) 디렉터리를 발표했습니다.
* 증가하는 [Github의 코드 샘플](https://github.com/Azure-Samples?q=b2c) 라이브러리를 확인해 보세요.

## <a name="how-to-articles"></a>방법 문서

특정 Azure Active Directory B2C 기능을 사용하는 방법을 알아봅니다.

* 소비자 지향 응용 프로그램에서 사용하기 위해 [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [Microsoft 계정](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) 및 [LinkedIn](active-directory-b2c-setup-li-app.md) 계정을 구성합니다.
* [사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집합니다.](active-directory-b2c-reference-custom-attr.md)
* [소비자 지향 응용 프로그램에서 Azure Multi-Factor Authentication을 사용합니다](active-directory-b2c-reference-mfa.md).
* [소비자를 위해 셀프 서비스 암호 재설정을 설정합니다](active-directory-b2c-reference-sspr.md).
* Azure Active Directory B2C에서 제공하는 [등록, 로그인 및 다른 소비자 지향 페이지의 모양과 느낌을 사용자 지정](active-directory-b2c-reference-ui-customization.md)합니다.
* [Azure Active Directory Graph API를 사용하여 프로그래밍 방식으로 소비자를 만들고 읽고 업데이트 및 삭제](active-directory-b2c-devquickstarts-graph-dotnet.md) 합니다.

## <a name="next-steps"></a>다음 단계

이러한 링크는 서비스를 자세히 탐색하는 데 유용합니다.

* [Azure Active Directory B2C 가격 책정 정보](https://azure.microsoft.com/pricing/details/active-directory-b2c/)를 참조하세요.
* Azure Active Directory B2C에 대한 [코드 샘플](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)을 검토합니다. 
* [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) 태그를 사용하여 Stack Overflow에 대한 도움말을 봅니다.
* [사용자 의견](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)을 사용하여 의견을 보내주세요. 사용자의 의견을 듣고 싶습니다!
* [Azure AD B2C 프로토콜 참조](active-directory-b2c-reference-protocols.md)를 검토합니다.
* [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 검토합니다.
* [Azure Active Directory B2C FAQ](active-directory-b2c-faqs.md)를 참고하세요.
* [Azure Active Directory B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)

## <a name="get-security-updates-for-our-products"></a>당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948) 를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.


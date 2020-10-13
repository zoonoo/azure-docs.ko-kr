---
title: Azure Active Directory B2C 코드 샘플 | Microsoft Docs
description: Azure Active Directory B2C 모바일, 데스크톱, 웹 및 단일 페이지 애플리케이션에 대한 샘플 코드입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 76cacbea3f98f65e290cb2722062127d2264e171
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91664837"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C 코드 샘플

다음 표에서는 iOS, Android, .NET, Node.js를 포함한 애플리케이션용 샘플의 링크를 제공합니다.

## <a name="mobile-and-desktop-apps"></a>모바일 및 데스크톱 앱

| 샘플 | Description |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | OAuth 2.0을 사용하여 Azure AD B2C 사용자를 인증하고 API를 호출하는 Swift의 iOS 샘플 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | MSAL을 사용하여 Azure Active Directory B2C를 통해 사용자를 인증하고 결과 토큰을 사용하여 Web API에 액세스하는 방법을 보여주는 간단한 Android 앱입니다. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | 타사 라이브러리를 사용하여 Objective-C로 Microsoft ID 사용자를 Azure AD B2C ID 서비스에 인증하는 iOS 애플리케이션을 빌드하는 방법을 보여주는 샘플입니다. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | 타사 라이브러리를 사용하여 Microsoft ID 사용자를 B2C ID 서비스에 인증하고 OAuth 2.0 액세스 토큰을 사용하여 Web API를 호출하는 Android 애플리케이션을 빌드하는 방법을 보여주는 샘플입니다. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Azure AD B2C를 사용하여 Windows 데스크톱 .NET(WPF) 애플리케이션으로 사용자를 로그인하고, MSAL.NET을 사용하여 액세스 토큰을 가져오고, API를 호출하는 방법을 보여주는 샘플입니다. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | MSAL을 사용하여 Azure Active Directory B2C를 통해 사용자를 인증하고 결과 토큰을 사용하여 Web API에 액세스하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다. |

## <a name="web-apps-and-apis"></a>웹앱 및 API

| 샘플 | Description |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | .NET Web API를 호출하는 .NET 웹 애플리케이션을 위한 결합 샘플로, 둘 다 Azure AD B2C를 사용하여 보호됩니다. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | OpenID Connect를 사용하여 Azure AD B2C에서 사용자를 로그인하는 ASP.NET Core 웹 애플리케이션입니다. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Azure AD B2C를 사용하여 사용자를 로그인하고, MSAL.NET을 사용하여 액세스 토큰을 가져오고, API를 호출하는 방법을 보여주는 ASP.NET Core 웹 애플리케이션입니다. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | OpenID Connect를 사용하여 Express를 통해 웹 애플리케이션을 쉽고 빠르게 설치하는 방법을 제공하는 Node.js 애플리케이션입니다. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | passport.js를 사용하여 web api를 보호하고 B2C 액세스 토큰에 액세스하는 방법을 보여주는 작은 Azure AD B2C용 node.js Web API입니다. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Microsoft ID 플랫폼의 B2C를 Python 웹 애플리케이션과 통합하는 방법을 보여줍니다.  |

## <a name="single-page-apps"></a>단일 페이지 앱

| 샘플 | Description |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Web API를 호출하는 SPA(단일 페이지 애플리케이션)입니다. 인증은 MSAL.js를 사용하여 Azure AD B2C로 수행됩니다. |

## <a name="saml-test-application"></a>SAML 테스트 애플리케이션

| 샘플 | Description |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Saml ID 공급자로 작동하도록 구성된 Azure AD B2C를 테스트하기 위한 SAML 테스트 애플리케이션입니다. |

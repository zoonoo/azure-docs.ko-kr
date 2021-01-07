---
title: MSAL에 대 한 자세한 정보 Microsoft
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리)을 사용 하면 응용 프로그램 개발자가 보안 웹 Api를 호출 하기 위해 토큰을 획득할 수 있습니다. 이러한 웹 Api는 Microsoft Graph, 다른 Microsoft Api, 타사 웹 Api 또는 고유한 web API 일 수 있습니다. MSAL은 다수의 애플리케이션 아키텍처와 플랫폼을 지원합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8fbcacf4e3c26f117be4ab5b64b35f670435e6b6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092752"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리) 개요
MSAL (microsoft 인증 라이브러리)을 통해 개발자는 사용자를 인증 하 고 보안 웹 Api에 액세스 하기 위해 Microsoft id 플랫폼 끝점에서 [토큰](developer-glossary.md#security-token) 을 획득할 수 있습니다. 이 기능을 사용 하 여 Microsoft Graph, 다른 Microsoft Api, 타사 웹 Api 또는 고유한 web API에 대 한 보안 액세스를 제공할 수 있습니다. MSAL은 .NET, JavaScript, Java, Python, Android 및 iOS를 비롯 한 다양 한 응용 프로그램 아키텍처 및 플랫폼을 지원 합니다.

MSAL을 통해 여러 플랫폼에 대해 일관된 API를 사용하여 토큰을 얻을 수 있는 방법은 여러 가지입니다. MSAL을 사용하면 다음과 같은 이점이 있습니다.

* 애플리케이션의 프로토콜에 대해 OAuth 라이브러리나 코드를 직접 사용할 필요가 없습니다.
* 사용자 대신 또는 애플리케이션 대신 토큰을 획득합니다(플랫폼에 해당하는 경우).
* 토큰 캐시를 유지 관리하고 토큰 만료가 가까워지면 토큰을 새로 고쳐줍니다. 토큰 만료를 직접 처리할 필요가 없습니다.
* 애플리케이션에 로그인할 수 있는 대상을 지정할 수 있습니다(사용자의 조직, 여러 조직, 직장 및 학교와 Microsoft 개인 계정, Azure AD B2C의 소셜 ID, 소버린의 사용자 및 국가별 클라우드).
* 구성 파일에서 애플리케이션을 설정할 수 있습니다.
* 실행 가능한 예외, 로깅 및 원격 분석을 노출하여 앱 문제를 해결할 수 있습니다.

## <a name="application-types-and-scenarios"></a>애플리케이션 유형 및 시나리오
MSAL을 사용하면 웹 애플리케이션, 웹 API, 단일 페이지 앱(JavaScript), 모바일과 원시 애플리케이션 및 디먼과 서버 쪽 애플리케이션 등과 같은 다양한 애플리케이션 유형에서 토큰을 획득할 수 있습니다.

MSAL은 다음을 비롯한 여러 가지 애플리케이션 시나리오에 사용할 수 있습니다.

* [단일 페이지 응용 프로그램 (JavaScript)](scenario-spa-overview.md)
* [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
* [사용자가 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹 애플리케이션](scenario-web-app-call-api-overview.md)
* [인증된 사용자만 액세스할 수 있도록 웹 API 보호](scenario-protected-web-api-overview.md)
* [로그인한 사용자를 대신하여 또 다른 다운스트림 웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md)
* [로그인 한 사용자를 대신 하 여 web API를 호출 하는 데스크톱 응용 프로그램](scenario-desktop-overview.md)
* [대화형으로 로그인 한 사용자를 대신 하 여 WEB API를 호출 하는 모바일 응용 프로그램](scenario-mobile-overview.md)입니다.
* [자체적으로 대신 웹 API를 호출하는 데스크톱/서비스 디먼 애플리케이션](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>언어 및 프레임워크

| 라이브러리 | 지원되는 플랫폼 및 프레임워크|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, 유니버설 Windows 플랫폼|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| AngularJS, Ember.js 또는 Durandal.js와 같은 JavaScript/TypeScript 프레임워크|
| [Android용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS 및 macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>ADAL과 MSAL의 차이점

ADAL(Active Directory 인증 라이브러리)은 개발자용 Azure AD(v1.0) 엔드포인트와 통합되며, MSAL은 Microsoft ID 플랫폼(v2.0) 엔드포인트와 통합됩니다. v1.0 엔드포인트는 회사 계정을 지원하지만 개인 계정은 지원하지 않습니다. v2.0 엔드포인트는 Microsoft 개인 계정과 회사 계정을 단일 인증 시스템으로 통합한 것입니다. 또한 MSAL을 사용하면 Azure AD B2C에 대한 인증을 받을 수 있습니다.

자세한 내용은 [ADAL.NET에서 MSAL.NET으로 마이그레이션](msal-net-migration.md)과 [ADAL.js에서 MSAL.js로 마이그레이션](msal-compare-msal-js-and-adal-js.md)을 읽어보세요.

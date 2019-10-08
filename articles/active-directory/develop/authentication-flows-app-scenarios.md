---
title: Microsoft ID 플랫폼의 인증 시나리오 | Azure
description: Microsoft ID 플랫폼의 인증 흐름 및 애플리케이션 시나리오에 대해 알아봅니다. ID를 인증하고 토큰을 획득하고 보호된 API를 호출할 수 있는 다양한 유형의 애플리케이션에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5330111e5ae56471d26ebc39dca1a036246945e1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348606"
---
# <a name="authentication-flows-and-application-scenarios"></a>인증 흐름 및 애플리케이션 시나리오

Microsoft ID 플랫폼(v2.0) 엔드포인트는 모두 업계 표준 프로토콜 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다.  [인증 라이브러리](reference-v2-libraries.md)를 사용하면 애플리케이션에서 ID를 인증하고 토큰을 획득하여 보호된 API에 액세스합니다. 이 문서에서는 다양한 인증 흐름과 이러한 흐름이 사용되는 애플리케이션 시나리오에 대해 설명합니다.  또한 이 문서에서는 [애플리케이션 시나리오 및 지원되는 인증 흐름](#scenarios-and-supported-authentication-flows)과 [애플리케이션 시나리오 및 지원되는 플랫폼과 언어](#scenarios-and-supported-platforms-and-languages) 목록도 제공합니다.

## <a name="application-categories"></a>애플리케이션 범주

토큰은 여러 애플리케이션 유형에서 가져올 수 있습니다. 브라우저(또는 iOT)가 없는 디바이스에서 실행되는 웹 애플리케이션, 모바일 또는 데스크톱 애플리케이션, 웹 API 및 애플리케이션입니다. 애플리케이션은 다음과 같은 범주로 분류할 수 있습니다.

- [보호된 리소스 및 클라이언트 애플리케이션](#protected-resources-vs-client-applications). 리소스(웹앱 또는 웹 API)를 보호하는 시나리오도 있고, 보안 토큰을 획득하여 보호된 웹 API를 호출하는 시나리오도 있습니다.
- [사용자 포함 또는 사용자 없음](#with-users-or-without-users). 로그인한 사용자가 관련되는 시나리오도 있고, 사용자가 관련되지 않는 시나리오도 있습니다(디먼 시나리오).
- [단일 페이지 애플리케이션, 퍼블릭 클라이언트 애플리케이션 및 기밀 클라이언트 애플리케이션](#single-page-applications-public-client-applications-and-confidential-client-applications). 이것은 세 가지 범주의 애플리케이션 유형입니다. 이러한 유형을 조작하는 데 사용되는 라이브러리 및 개체는 다릅니다.
- [로그인 대상 그룹](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). 일부 인증 흐름은 특정 로그인 대상에 대해 사용할 수 없습니다. 일부 흐름은 회사 또는 학교 계정에만 사용할 수 있으며, 일부는 회사 또는 학교 계정과 개인 Microsoft 계정 둘 다에 사용할 수 있습니다. 허용되는 대상은 인증 흐름에 따라 다릅니다.
- [지원되는 OAuth 2.0 흐름](#scenarios-and-supported-authentication-flows).  인증 흐름은 토큰을 요청하는 애플리케이션 시나리오를 구현하는 데 사용됩니다.  애플리케이션 시나리오와 인증 흐름 사이에는 일대일 매핑이 없습니다.
- [지원되는 플랫폼](#scenarios-and-supported-platforms-and-languages). 모든 플랫폼에서 모든 애플리케이션 시나리오를 사용할 수 있는 것은 아닙니다.

### <a name="protected-resources-vs-client-applications"></a>보호된 리소스 및 클라이언트 애플리케이션

인증 시나리오에는 두 가지 작업이 포함됩니다.

- 보호된 웹 API를 위한 **보안 토큰 획득**. [인증 라이브러리](reference-v2-libraries.md#microsoft-supported-client-libraries), 특히 MSAL(Microsoft 인증 라이브러리 제품군)을 사용하여 토큰을 획득하는 것이 좋습니다.
- **웹 API(또는 웹앱) 보호**. 리소스(웹앱 또는 웹 API)를 보호할 때의 어려움 중 하나는 보안 토큰의 유효성을 검사하는 것입니다. Microsoft는 일부 플랫폼에서 [미들웨어 라이브러리](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)를 제공합니다.

### <a name="with-users-or-without-users"></a>사용자 포함 또는 사용자 없음

대부분의 인증 시나리오는 (로그인된) **사용자**를 대신해서 토큰을 획득합니다.

![사용자 포함 시나리오](media/scenarios/scenarios-with-users.svg)

그러나 애플리케이션에서 사용자를 대신하여 토큰을 획득하는 시나리오(디먼 앱)도 있습니다.

![디먼 앱](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>단일 페이지 애플리케이션, 퍼블릭 클라이언트 애플리케이션 및 기밀 클라이언트 애플리케이션.

보안 토큰은 여러 애플리케이션 유형에서 가져올 수 있습니다. 애플리케이션은 다음과 같은 세 가지 범주로 구분됩니다.

- **SPA(단일 페이지 애플리케이션)** 는 브라우저에서 실행 중인 앱(JavaScript 또는 Typescript로 작성)에서 토큰을 가져오는 웹 애플리케이션의 형식입니다. 대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 앱은 종종 Angular, React 또는 Vue과 같은 프레임워크를 사용하여 작성됩니다. MSAL.js는 단일 페이지 애플리케이션을 지원하는 유일한 Microsoft 인증 라이브러리입니다.

- **퍼블릭 클라이언트 애플리케이션**은 항상 사용자를 로그인합니다. 이러한 앱은 다음과 같습니다.
  - 로그인한 사용자를 대신하여 웹 API를 호출하는 데스크톱 애플리케이션
  - 모바일 애플리케이션
  - 브라우저가 없는 디바이스에서 실행되는 세 번째 범주의 애플리케이션(예를 들어, iOT에서 실행되는 브라우저 없는 앱)

  이러한 앱은 [PublicClientApplication](msal-client-applications.md)이라는 MSAL 클래스로 표시됩니다.

- **기밀 클라이언트 애플리케이션**
  - 웹 API를 호출하는 웹 애플리케이션
  - 웹 API를 호출하는 웹 API
  - 디먼 애플리케이션(Linux의 디먼과 같은 콘솔 서비스 또는 Windows 서비스로 구현된 경우도 해당)
 
  이러한 유형의 앱은 [ConfidentialClientApplication](msal-client-applications.md)을 사용합니다.

## <a name="application-scenarios"></a>애플리케이션 시나리오

Microsoft ID 플랫폼 엔드포인트는 단일 페이지 앱, 웹앱, 웹 API, 모바일 및 네이티브 앱, 디먼 및 서버 쪽 앱 등 다양한 앱 아키텍처에 대한 인증을 지원합니다.  애플리케이션은 다양한 인증 흐름을 사용하여 사용자를 로그인하고 토큰을 가져와 보호된 API를 호출합니다.

### <a name="single-page-application"></a>단일 페이지 애플리케이션

많은 최신 웹 애플리케이션은 JavaScript 또는 Angular, Vue.js, React.js 같은 SPA 프레임워크를 사용하여 작성되는 클라이언트 쪽 단일 페이지 애플리케이션으로 빌드됩니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다. Microsoft ID 플랫폼을 사용하면 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다.

![단일 페이지 애플리케이션](media/scenarios/spa-app.svg)

자세한 내용은 [단일 페이지 애플리케이션](scenario-spa-overview.md)을 참조하세요.

### <a name="web-application-signing-in-a-user"></a>웹 애플리케이션의 사용자 로그인

![웹앱의 사용자 로그인](media/scenarios/scenario-webapp-signs-in-users.svg)

**웹앱을 보호하려면**(사용자 로그인) 다음을 사용합니다.

- .NET 세계에서는 ASP.NET Open ID Connect 미들웨어를 포함하는 ASP.NET 또는 ASP.NET Core. 내부적으로 리소스 보호 작업 중에 보안 토큰의 유효성 검사가 수행되며 이 작업은 MSAL 라이브러리가 아닌 [.NET용 IdentityModel 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) 라이브러리가 수행합니다.

- Node.js에서 개발하는 경우에는 Passport.js를 사용합니다.

자세한 내용은 [사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)을 참조하세요.

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>사용자를 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹 애플리케이션

![웹앱에서 웹 API 호출](media/scenarios/web-app.svg)

웹앱에서 사용자를 대신하여 **웹 API를 호출**하려면 MSAL `ConfidentialClientApplication`을 사용합니다. 획득한 토큰을 토큰 캐시에 저장하고 권한 부여 코드 흐름을 사용합니다. 그러면 컨트롤러는 필요할 때 캐시에서 자동으로 토큰을 획득합니다. 필요한 경우 MSAL에서 토큰을 새로 고칩니다.

자세한 내용은 [웹 API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)을 참조하세요.

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>로그인한 사용자를 대신하여 웹 API를 호출하는 데스크톱 애플리케이션

사용자를 로그인하는 데스크톱 애플리케이션에서 웹 API를 호출하려면 MSAL의 PublicClientApplication 대화형 토큰 획득 메서드를 사용합니다. 이러한 대화형 메서드를 사용하여 로그인 UI 환경을 제어할 수 있습니다. 이 상호 작용을 설정하기 위해 MSAL은 웹 브라우저를 활용합니다.

![데스크톱](media/scenarios/desktop-app.svg)

Windows 도메인 또는 AAD에 가입된 컴퓨터에서 실행 중인 Windows 호스트 애플리케이션의 경우 또 다른 가능성이 있습니다. 이러한 애플리케이션은 [Windows 통합 인증](https://aka.ms/msal-net-iwa)을 사용하여 토큰을 자동으로 가져올 수 있습니다.

브라우저 없이 디바이스에서 실행되는 애플리케이션은 사용자를 대신하여 API를 호출할 수 있습니다. 인증을 받으려면 사용자는 웹 브라우저가 있는 다른 디바이스에 로그인해야 합니다. 이 시나리오를 사용하려면 [디바이스 코드 흐름](https://aka.ms/msal-net-device-code-flow)을 사용해야 합니다.

![디바이스 코드 흐름](media/scenarios/device-code-flow-app.svg)

마지막으로, 권장하는 방법은 아니지만 퍼블릭 클라이언트 애플리케이션에서 [사용자 이름/암호](https://aka.ms/msal-net-up)를 사용할 수 있습니다. 이 흐름은 DevOps와 같은 일부 시나리오에서는 여전히 필요하지만, 이 흐름을 사용하면 애플리케이션에 제약이 발생합니다. 예를 들어 이 흐름을 사용하는 앱은 Multi-Factor Authentication(조건부 액세스)을 수행해야 하는 사용자를 로그인할 수 없습니다. 또한 애플리케이션에서 Single Sign-On을 사용할 수도 없습니다. 사용자 이름/암호를 사용하는 인증은 최신 인증 원칙에 반하며, 레거시 용도로만 제공됩니다.

데스크톱 애플리케이션에서 토큰 캐시를 영구적으로 유지하려면 [토큰 캐시 직렬화를 사용자 지정](https://aka.ms/msal-net-token-cache-serialization)해야 합니다. [이중 토큰 캐시 직렬화](https://aka.ms/msal-net-dual-cache-serialization)를 구현하여 이전 인증 라이브러리 세대(ADAL.NET 3.x 및 4.x)와 호환되는 토큰 캐시를 사용하도록 설정할 수도 있습니다.

자세한 내용은 [웹 API를 호출하는 데스크톱 앱](scenario-desktop-overview.md)을 참조하세요.

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>대화형으로 로그인한 사용자를 대신하여 Web API를 호출하는 모바일 애플리케이션

데스크톱 애플리케이션과 마찬가지로, 모바일 애플리케이션은 MSAL의 PublicClientApplication 대화형 토큰 획득 메서드를 사용하여 웹 API를 호출하는 토큰을 획득합니다.

![모바일](media/scenarios/mobile-app.svg)

MSAL iOS 및 MSAL Android는 기본적으로 시스템 웹 브라우저를 사용합니다. 그러나 포함된 웹 보기를 사용하도록 지시할 수도 있습니다. 모바일 플랫폼에 따라 다음과 같은 사양이 적용됩니다. (UWP, iOS, Android).

디바이스 ID와 관련된 조건부 액세스나 등록 중인 디바이스와 관련된 일부 시나리오에서는 디바이스에 [브로커](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)를 설치해야 합니다. 브로커의 예로는 Microsoft 회사 포털(Android), Microsoft Authenticator(Android 및 iOS)가 있습니다. 이제 MSAL은 브로커와 상호 작용할 수 있습니다.

> [!NOTE]
> 모바일 앱(MSAL.iOS, MSAL.Android 또는 MSAL.NET/Xamarin 사용)에는 앱 보호 정책이 적용될 수 있습니다(예를 들어, 사용자가 일부 보호된 텍스트를 복사하지 못하도록 함). 이 앱은 [intune에서 관리되고](https://docs.microsoft.com/intune/app-sdk) intune에서 관리형 앱으로 인식됩니다. [Intune SDK](https://docs.microsoft.com/intune/app-sdk-get-started)는 MSAL 라이브러리와는 별개이며, 자체적으로 AAD와 통신합니다.

자세한 내용은 [웹 API를 호출하는 모바일 앱](scenario-mobile-overview.md)을 참조하세요.

### <a name="protected-web-api"></a>보호된 웹 API

Microsoft ID 플랫폼 엔드포인트를 사용하여 앱의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. 보호된 웹 API는 해당 데이터를 보호하고 들어오는 요청을 인증하기 위해 액세스 토큰을 사용하여 호출됩니다. 웹 API 호출자는 HTTP 요청의 인증 헤더에 액세스 토큰에 추가합니다. ASP.NET 또는 ASP.NET Core 웹 API를 보호하려는 경우 액세스 토큰의 유효성을 검사해야 합니다. 이를 위해 ASP.NET JWT 미들웨어를 사용합니다. 내부적으로 유효성 검사는 MSAL.NET이 아니라 [.NET용 IdentityModel 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) 라이브러리에서 수행됩니다.

자세한 내용은 [보호된 웹 API](scenario-protected-web-api-overview.md)를 참조하세요.

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>해당 API가 호출된 사용자를 대신하여 또 다른 다운스트림 웹 API를 호출하는 웹 API

또한 ASP.NET 또는 ASP.NET Core 보호된 웹 API에서 사용자를 대신하여 다른 웹 API를 호출하도록 하려면 앱은 [사용자를 대신해서](https://aka.ms/msal-net-on-behalf-of) 토큰을 획득하는 ConfidentialClientApplication의 메서드를 사용하여 다운스트림 웹 API에 대한 토큰을 획득해야 합니다. 이를 서비스 간 호출이라고도 합니다.
다른 웹 API를 호출하는 웹 API는 사용자 지정 캐시 직렬화도 제공해야 합니다.

  ![Web API](media/scenarios/web-api.svg)

자세한 내용은 [웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md)를 참조하세요.

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>사용자 없이(자체 이름에) 웹 API를 호출하는 데스크톱/서비스 또는 웹 디먼 애플리케이션

장기 실행 프로세스가 있거나 사용자 상호 작용 없이 작동하는 앱 역시 보안 웹 API에 액세스하는 방법이 필요합니다. 이러한 앱은 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다. 클라이언트 암호 또는 인증서를 사용하여 해당 ID를 증명합니다.
MSAL ConfidentialClientApplication의 [클라이언트 자격 증명](https://aka.ms/msal-net-client-credentials) 획득 메서드를 사용하여 맨 위에 앱용 토큰을 획득하는 이러한 앱(디먼 앱)을 작성할 수 있습니다. 여기서는 앱이 이전에 Azure AD에 암호(애플리케이션 암호나 인증서 또는 클라이언트 어설션)를 등록한 후 이 호출과 공유한다고 가정합니다.

![디먼 앱](media/scenarios/daemon-app.svg)

자세한 내용은 [웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)을 참조하세요.

## <a name="scenarios-and-supported-authentication-flows"></a>시나리오 및 지원되는 인증 흐름

토큰 획득을 포함하는 시나리오는 [Microsoft ID 플랫폼 프로토콜](active-directory-v2-protocols.md)의 세부 정보에 설명된 OAuth 2.0 인증 흐름에도 매핑됩니다.

<table>
 <thead>
  <tr><th>시나리오</th> <th>자세한 시나리오 연습</th> <th>OAuth 2.0 Flow/Grant</th> <th>대상</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">단일 페이지 앱</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">암시적</a></td>
   <td>회사/학교 계정 및 개인 계정, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">사용자를 로그인하는 웹앱</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">인증 코드</a></td>
   <td>회사/학교 계정 및 개인 계정, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web API를 호출하는 웹앱</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">인증 코드</a></td>
   <td>회사/학교 계정 및 개인 계정, B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Web API를 호출하는 데스크톱 앱</a></td>
   <td>대화형(PKCE를 사용하는 <a href="v2-oauth2-auth-code-flow.md">인증 코드</a>)</td>
   <td>회사/학교 계정 및 개인 계정, B2C</td>
 </tr>

  <tr>
   <td>통합 Windows 인증</td>
   <td>회사 또는 학교 계정</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">리소스 소유자 암호</a></td>
   <td>회사 또는 학교 계정, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">디바이스 코드</a></td>
   <td>회사 또는 학교 계정*</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">웹 API를 호출하는 모바일 앱</a></td>
   <td>대화형(PKCE를 사용하는 <a href="v2-oauth2-auth-code-flow.md">인증 코드</a>)</td>
   <td>회사/학교 계정 및 개인 계정, B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">리소스 소유자 암호</a></td>
   <td>회사 또는 학교 계정, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls Web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Web API를 호출하는 Daemon 앱</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">클라이언트 자격 증명</a></td>
   <td>AAD 조직에서만 사용되는 앱 전용 권한(사용자 없음)</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">웹 API를 호출하는 웹 API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">다음 항목 대신</a></td>
   <td>회사/학교 계정 및 개인 계정</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>시나리오 및 지원되는 플랫폼과 언어

모든 플랫폼에서 모든 애플리케이션 유형을 사용할 수 있는 것은 아닙니다. 다양한 언어를 사용하여 애플리케이션을 빌드할 수도 있습니다. Microsoft 인증 라이브러리는 다양한 **플랫폼**(JavaScript, .NET Framework, .NET Core, Windows 10/UWP, Xamarin.iOS, Xamarin.Android, 네이티브 iOS, Mac OS, 네이티브 Android, Java, Python)을 지원합니다. 아래 표의 Windows에서는 .NET Core가 언급될 때마다 .NET Framework도 가능합니다(테이블이 복잡하지 않도록 하기 위해 생략됨).

|시나리오  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [단일 페이지 앱](scenario-spa-overview.md) <br/>[![단일 페이지 앱](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md) <br/>[![사용자를 로그인하는 웹앱](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [웹 API를 호출하는 웹앱](scenario-web-app-call-api-overview.md) <br/> <br/>[![웹 API를 호출하는 웹앱](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Web API를 호출하는 데스크톱 앱](scenario-desktop-overview.md) <br/> <br/>[![웹 API를 호출하는 데스크톱 웹](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![디바이스 코드 흐름](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL.objc |
| [웹 API를 호출하는 모바일 앱](scenario-mobile-overview.md) <br/> [![웹 API를 호출하는 모바일 앱](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C 또는 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [디먼 앱](scenario-daemon-overview.md) <br/> [![디먼 앱](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md) <br/><br/> [![웹 API를 호출하는 웹 API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

[OS/언어별 Microsoft 지원 라이브러리](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)도 참조하세요.

## <a name="next-steps"></a>다음 단계
[인증 기본 사항](authentication-scenarios.md) 및 [액세스 토큰](access-tokens.md)에 대해 자세히 알아보세요.

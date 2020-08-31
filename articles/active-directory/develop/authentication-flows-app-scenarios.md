---
title: Microsoft ID 플랫폼 인증 흐름 및 앱 시나리오 | Azure
description: Id 인증, 토큰 획득 및 보호 된 Api 호출을 포함 하 여 Microsoft id 플랫폼용 응용 프로그램 시나리오에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 99d16407d1cfadcb7d3a79968fae4436e7c631e3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115648"
---
# <a name="authentication-flows-and-application-scenarios"></a>인증 흐름 및 애플리케이션 시나리오

Microsoft ID 플랫폼(v2.0) 엔드포인트는 다양한 종류의 최신 애플리케이션 아키텍처에 대한 인증을 지원합니다. 모든 아키텍처는 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md) 업계 표준 프로토콜을 기반으로 합니다. 응용 프로그램은 [Microsoft id 플랫폼용 인증 라이브러리](reference-v2-libraries.md)를 사용 하 여 id를 인증 하 고 토큰을 획득 하 여 보호 된 api에 액세스 합니다.

이 문서에서는 인증 흐름과 이러한 흐름을 사용 하는 응용 프로그램 시나리오에 대해 설명 합니다.

## <a name="application-categories"></a>애플리케이션 범주

토큰은 다음을 비롯 한 여러 유형의 응용 프로그램에서 가져올 수 있습니다.

- 웹앱
- 모바일 앱
- 데스크톱 앱
- Web API

브라우저를 포함 하지 않거나 IoT (사물 인터넷)에서 실행 되 고 있는 장치에서 실행 되는 앱에서 토큰을 가져올 수도 있습니다.

다음 섹션에서는 응용 프로그램의 범주에 대해 설명 합니다.

### <a name="protected-resources-vs-client-applications"></a>보호된 리소스 및 클라이언트 애플리케이션

인증 시나리오에는 두 가지 작업이 포함됩니다.

- **보호 된 WEB API에 대 한 보안 토큰 가져오기**: [Microsoft에서 지 원하는 클라이언트 라이브러리](reference-v2-libraries.md#microsoft-supported-client-libraries) 를 사용 하 여 토큰을 얻는 것이 좋습니다. 특히 MSAL (Microsoft 인증 라이브러리) 제품군을 권장 합니다.
- **WEB API 또는 웹 앱 보호**: 이러한 리소스를 보호 하는 한 가지 문제는 보안 토큰의 유효성을 검사 하는 것입니다. 일부 플랫폼의 경우 Microsoft에서 [미들웨어 라이브러리](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)를 제공합니다.

### <a name="with-users-or-without-users"></a>사용자 포함 또는 사용자 없음

대부분의 인증 시나리오에서는 로그인한 사용자를 대신하여 토큰을 획득합니다.

![사용자 포함 시나리오](media/scenarios/scenarios-with-users.svg)

그러나 디먼 앱도 있습니다. 이러한 시나리오에서 응용 프로그램은 사용자를 대신 하 여 토큰을 획득 합니다.

![사용자 없음 시나리오](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>단일 페이지, 퍼블릭 클라이언트 및 기밀 클라이언트 애플리케이션

여러 유형의 응용 프로그램에서 보안 토큰을 가져올 수 있습니다. 이러한 응용 프로그램은 다음과 같은 세 가지 범주로 구분 됩니다. 각 라이브러리는 서로 다른 라이브러리와 개체에 사용됩니다.

- **단일 페이지 응용 프로그램**: spas 라고도 하며, 브라우저에서 실행 되는 JavaScript 또는 TypeScript 앱에서 토큰을 획득 하는 웹 앱입니다. 많은 최신 앱에는 기본적으로 JavaScript로 작성 된 프런트 엔드에 단일 페이지 응용 프로그램이 있습니다. 애플리케이션은 종종 Angular, React 또는 Vue와 같은 프레임워크를 사용합니다. MSAL.js는 단일 페이지 애플리케이션을 지원하는 유일한 Microsoft 인증 라이브러리입니다.

- **공용 클라이언트 응용 프로그램**: 다음 형식과 같이이 범주의 앱은 항상 로그인 사용자입니다.
  - 로그인 한 사용자를 대신 하 여 web Api를 호출 하는 데스크톱 앱
  - 모바일 앱
  - IoT에서 실행 하는 것과 같이 브라우저가 없는 장치에서 실행 되는 앱
  
- **기밀 클라이언트 응용 프로그램**:이 범주의 앱은 다음과 같습니다.
  - 웹 API를 호출 하는 웹 앱
  - 웹 API를 호출 하는 웹 Api
  - 디먼 앱(Linux 디먼 또는 Windows 서비스와 같은 콘솔 서비스로 구현된 경우에도 해당)

### <a name="sign-in-audience"></a>로그인 대상 그룹

사용 가능한 인증 흐름은 로그인 대상 그룹에 따라 달라집니다. 일부 흐름은 회사 또는 학교 계정에서만 사용할 수 있습니다. 회사 또는 학교 계정 및 개인 Microsoft 계정 둘 다에서 사용할 수 있습니다.

자세한 내용은 [지원 되는 계정 유형](v2-supported-account-types.md#account-type-support-in-authentication-flows)을 참조 하세요.

## <a name="application-scenarios"></a>애플리케이션 시나리오

Microsoft id 플랫폼 끝점은 이러한 앱 아키텍처에 대 한 인증을 지원 합니다.

- 단일 페이지 앱
- 웹앱
- Web API
- 모바일 앱
- 네이티브 앱
- 디먼 앱
- 서버 쪽 앱

애플리케이션은 다양한 인증 흐름을 사용하여 사용자를 로그인하고, 토큰을 가져와서 보호된 API를 호출합니다.

### <a name="single-page-application"></a>단일 페이지 애플리케이션

많은 최신 웹 앱은 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 이러한 응용 프로그램은 각도, Vue.js 및 React.js 같은 JavaScript 또는 프레임 워크를 사용 합니다. 이러한 애플리케이션은 웹 브라우저에서 실행됩니다.

단일 페이지 응용 프로그램은 인증 특성 측면에서 기존의 서버 쪽 웹 앱과 다릅니다. Microsoft ID 플랫폼을 사용하면 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다.

![단일 페이지 애플리케이션](media/scenarios/spa-app.svg)

자세한 내용은 [단일 페이지 애플리케이션](scenario-spa-overview.md)을 참조하세요.

### <a name="web-app-that-signs-in-a-user"></a>사용자가 로그인 하는 웹 앱

![사용자를 로그인하는 웹앱](media/scenarios/scenario-webapp-signs-in-users.svg)

사용자가 로그인 하는 웹 앱을 보호 하려면:

- .NET에서 개발 하는 경우 ASP.NET Openid connect Connect 미들웨어와 함께 ASP.NET 또는 ASP.NET Core를 사용 합니다. 리소스를 보호 하기 위해 보안 토큰의 유효성을 검사 하는 작업이 포함 됩니다. 보안 토큰은 MSAL 라이브러리가 아닌 [.net 용 system.identitymodel 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) 에 의해 수행 됩니다.

- Node.js에서 개발하는 경우 Passport.js를 사용합니다.

자세한 내용은 [사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)을 참조하세요.

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>사용자를 대신 하 여 사용자를 로그인 하 고 web API를 호출 하는 웹 앱

![웹 API를 호출하는 웹앱](media/scenarios/web-app.svg)

사용자를 대신 하 여 웹 앱에서 web API를 호출 하려면 권한 부여 코드 흐름을 사용 하 여 획득 한 토큰을 토큰 캐시에 저장 합니다. 필요한 경우 MSAL은 토큰을 새로 고치고, 컨트롤러는 캐시에서 토큰을 자동으로 가져옵니다.

자세한 내용은 웹 Api를 [호출 하는 웹 앱](scenario-web-app-call-api-overview.md)을 참조 하세요.

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>로그인 한 사용자를 대신 하 여 web API를 호출 하는 데스크톱 앱

데스크톱 앱에서 사용자에 게 로그인 하는 web API를 호출 하려면 MSAL의 대화형 토큰 획득 메서드를 사용 합니다. 이러한 대화형 메서드를 사용하면 로그인 UI 환경을 제어할 수 있습니다. MSAL은 이 상호 작용에 웹 브라우저를 사용합니다.

![웹 API를 호출하는 데스크톱 앱](media/scenarios/desktop-app.svg)

Windows 도메인에 조인되거나 Azure AD(Azure Active Directory)에서 조인한 컴퓨터에서 Windows에 호스팅된 애플리케이션을 사용할 수도 있습니다. 이러한 애플리케이션은 [Windows 통합 인증](https://aka.ms/msal-net-iwa)을 사용하여 토큰을 자동으로 가져올 수 있습니다.

브라우저 없이 디바이스에서 실행되는 애플리케이션은 여전히 사용자를 대신하여 API를 호출할 수 있습니다. 인증하려면 사용자가 웹 브라우저를 사용하는 다른 디바이스에 로그인해야 합니다. 이 시나리오에서는 [장치 코드 흐름](https://aka.ms/msal-net-device-code-flow)을 사용 해야 합니다.

![디바이스 코드 흐름](media/scenarios/device-code-flow-app.svg)

[사용자 이름/암호 흐름](scenario-desktop-acquire-token.md#username-and-password) 은 사용 하지 않는 것이 좋지만 공용 클라이언트 응용 프로그램에서 사용할 수 있습니다. 이 흐름은 여전히 DevOps와 같은 일부 시나리오에서 필요합니다.

사용자 이름/암호 흐름을 사용 하면 응용 프로그램이 제한 됩니다. 예를 들어 응용 프로그램은 Azure AD에서 다단계 인증 또는 조건부 액세스 도구를 사용 해야 하는 사용자에 게 로그인 할 수 없습니다. 또한 애플리케이션에서 Single Sign-On의 이점을 누릴 수 없습니다. 사용자 이름/암호 흐름으로 인증 하는 것은 최신 인증의 원칙에 따라 진행 되며 레거시 용도로만 제공 됩니다.

데스크톱 앱에서 토큰 캐시를 유지 하려면 [토큰 캐시 serialization](scenario-desktop-acquire-token.md#file-based-token-cache)을 사용자 지정할 수 있습니다. [이중 토큰 캐시 serialization](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)을 구현 하 여 이전 버전과 호환 되는 토큰 캐시를 사용할 수 있습니다. 이러한 토큰은 이전 인증 라이브러리 생성을 지원 합니다. 특정 라이브러리에는 .NET용 Azure AD 인증 라이브러리(ADAL.NET) 버전 3 및 4가 포함됩니다.

자세한 내용은 [웹 API를 호출하는 데스크톱 앱](scenario-desktop-overview.md)을 참조하세요.

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>대화형 사용자를 대신 하 여 web API를 호출 하는 모바일 앱

모바일 앱은 데스크톱 앱과 유사 하 게 MSAL의 대화형 토큰 획득 메서드를 호출 하 여 web API를 호출 하기 위한 토큰을 가져옵니다.

![웹 API를 호출하는 모바일 앱](media/scenarios/mobile-app.svg)

MSAL iOS 및 MSAL Android는 기본적으로 시스템 웹 브라우저를 사용합니다. 그러나 포함 된 웹 뷰를 대신 사용 하도록 지시할 수 있습니다. 유니버설 Windows 플랫폼 (UWP), iOS 또는 Android 모바일 플랫폼에 종속 된 specificities 있습니다.

디바이스 ID 또는 디바이스 등록과 관련된 조건부 액세스가 포함된 일부 시나리오에서는 broker를 디바이스에 설치해야 합니다. broker의 예로는 Android의 Microsoft 회사 포털과 Android 및 iOS의 Microsoft Authenticator가 있습니다. 이제 MSAL에서 broker와 상호 작용할 수 있습니다. 브로커에 대 한 자세한 내용은 [Android 및 iOS에서 Broker 활용](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)을 참조 하세요.

자세한 내용은 [웹 API를 호출하는 모바일 앱](scenario-mobile-overview.md)을 참조하세요.

> [!NOTE]
> MSAL. iOS, MSAL을 사용 하는 모바일 앱입니다. Android 또는 MSAL.NET on Xamarin은 앱 보호 정책을 적용할 수 있습니다. 예를 들어 정책을 사용하면 사용자가 보호된 텍스트를 복사하지 못할 수 있습니다. 모바일 앱은 Intune에서 관리 되며 Intune에서 관리 되는 앱으로 인식 됩니다. 자세한 내용은 [Microsoft Intune APP SDK 개요](/intune/app-sdk)를 참조 하세요.
>
> [Intune 앱 SDK](/intune/app-sdk-get-started)는 MSAL 라이브러리와는 별개이며, 자체적으로 Azure AD와 상호 작용합니다.

### <a name="protected-web-api"></a>보호된 웹 API

Microsoft id 플랫폼 끝점을 사용 하 여 앱의 RESTful web API와 같은 웹 서비스를 보호할 수 있습니다. 보호 된 웹 API는 액세스 토큰을 통해 호출 됩니다. 토큰은 API의 데이터를 보호 하 고 들어오는 요청을 인증 하는 데 도움이 됩니다. 웹 API 호출자는 액세스 토큰을 HTTP 요청의 인증 헤더에 추가합니다.

ASP.NET 또는 ASP.NET Core web API를 보호 하려는 경우 액세스 토큰의 유효성을 검사 해야 합니다. 이 유효성 검사를 위해 ASP.NET JWT 미들웨어를 사용합니다. 유효성 검사는 MSAL.NET이 아니라 [.NET용 IdentityModel 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) 라이브러리에서 수행합니다.

자세한 내용은 [보호된 웹 API](scenario-protected-web-api-overview.md)를 참조하세요.

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>사용자를 대신 하 여 다른 웹 API를 호출 하는 web API

사용자를 대신 하 여 다른 web api를 호출 하는 보호 된 웹 API의 경우 앱은 다운스트림 웹 API에 대 한 토큰을 획득 해야 합니다. 이러한 호출을 *서비스* 간 호출이 라고도 합니다. 다른 웹 Api를 호출 하는 웹 Api는 사용자 지정 캐시 직렬화를 제공 해야 합니다.

![다른 웹 API를 호출하는 웹 API](media/scenarios/web-api.svg)

자세한 내용은 [웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md)를 참조하세요.

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>디먼 이름에서 웹 API를 호출 하는 디먼 앱

장기 실행 프로세스가 있거나 사용자 상호 작용 없이 작동하는 앱에도 보안 웹 API에 액세스할 수 있는 방법이 필요합니다. 이러한 앱은 앱의 id를 사용 하 여 인증 하 고 토큰을 가져올 수 있습니다. 앱은 클라이언트 비밀 또는 인증서를 사용하여 해당 ID를 증명합니다.

MSAL의 [클라이언트 자격 증명](scenario-daemon-acquire-token.md#acquiretokenforclient-api) 획득 메서드를 사용 하 여 호출 앱에 대 한 토큰을 획득 하는 이러한 디먼 앱을 작성할 수 있습니다. 이러한 방법에는 Azure AD에서 앱 등록에 추가 하는 클라이언트 암호가 필요 합니다. 그런 다음, 앱에서 이 비밀을 호출된 디먼과 공유합니다. 이러한 암호의 예로는 응용 프로그램 암호, 인증서 어설션 및 클라이언트 어설션이 있습니다.

![다른 앱 및 API에서 호출하는 디먼 앱](media/scenarios/daemon-app.svg)

자세한 내용은 [웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)을 참조하세요.

## <a name="scenarios-and-supported-authentication-flows"></a>시나리오 및 지원되는 인증 흐름

인증 흐름을 사용 하 여 토큰을 요청 하는 응용 프로그램 시나리오를 구현 합니다. 애플리케이션 시나리오와 인증 흐름 간에는 일대일 매핑이 없습니다.

토큰 획득을 포함 하는 시나리오는 OAuth 2.0 인증 흐름에도 매핑됩니다. 자세한 내용은 [Microsoft id 플랫폼에서 OAuth 2.0 및 Openid connect Connect 프로토콜](active-directory-v2-protocols.md)을 참조 하세요.

<table>
 <thead>
  <tr><th>시나리오</th> <th>자세한 시나리오 연습</th> <th>OAuth 2.0 흐름 및 권한 부여</th> <th>사용자</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">단일 페이지 앱</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">암시적</a></td>
   <td>회사 또는 학교 계정, 개인 계정 및 Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">사용자가 로그인하는 웹앱</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">인증 코드</a></td>
   <td>회사/학교 계정, 개인 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web API를 호출하는 웹앱</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">인증 코드</a></td>
   <td>회사/학교 계정, 개인 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Web API를 호출하는 데스크톱 앱</a></td>
   <td>PKCE에서 <a href="v2-oauth2-auth-code-flow.md">권한 부여 코드</a> 를 사용 하 여 대화형</td>
   <td>회사/학교 계정, 개인 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td>Windows 통합 인증</td>
   <td>회사 또는 학교 계정</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">리소스 소유자 암호</a></td>
   <td>회사/학교 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">디바이스 코드</a></td>
   <td>회사 또는 학교 계정</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Web API를 호출하는 모바일 앱</a></td>
   <td>PKCE에서 <a href="v2-oauth2-auth-code-flow.md">권한 부여 코드</a> 를 사용 하 여 대화형</td>
   <td>회사/학교 계정, 개인 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">리소스 소유자 암호</a></td>
   <td>회사/학교 계정 및 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">웹 Api를 호출 하는 디먼 앱</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">클라이언트 자격 증명</a></td>
   <td>사용자가 없고 Azure AD 조직 에서만 사용 되는 앱 전용 권한</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">웹 API를 호출하는 웹 API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">On-behalf-of</a></td>
   <td>회사/학교 계정 및 개인 계정</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>시나리오 및 지원되는 플랫폼과 언어

Microsoft 인증 라이브러리는 여러 플랫폼을 지원 합니다.

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- 네이티브 iOS
- macOS
- 네이티브 Android
- Java
- Python

다양한 언어를 사용하여 애플리케이션을 빌드할 수도 있습니다.

> [!NOTE]
> 일부 응용 프로그램 유형은 모든 플랫폼에서 사용할 수 없습니다.

다음 표의 Windows 열에서 .NET Core가 언급될 때마다 .NET Framework도 사용할 수 있습니다. 복잡한 표가 되지 않도록 후자는 생략되었습니다.

|시나리오  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [단일 페이지 앱](scenario-spa-overview.md) <br/>[![단일 페이지 앱](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md) <br/>[![사용자를 로그인 하는 웹 앱](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web API를 호출하는 웹앱](scenario-web-app-call-api-overview.md) <br/> <br/>[![웹 API를 호출하는 웹앱](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Web API를 호출하는 데스크톱 앱](scenario-desktop-overview.md) <br/> <br/>[ ![ 웹 api를 호출 하는 데스크톱 앱](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ 장치 코드 흐름](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/Objective C 또는 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Web API를 호출하는 모바일 앱](scenario-mobile-overview.md) <br/> [![Web API를 호출하는 모바일 앱](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C 또는 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [디먼 앱](scenario-daemon-overview.md) <br/> [![디먼 앱](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md) <br/><br/> [![웹 API를 호출하는 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

자세한 내용은 [Microsoft 지원 라이브러리 BY OS/language](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Microsoft id 플랫폼의](access-tokens.md) [인증 기본 사항](./authentication-vs-authorization.md) 및 액세스 토큰에 대해 자세히 알아보세요.
* [IoT 앱에 대 한 액세스 보안](/azure/architecture/example-scenario/iot-aad/iot-aad)에 대해 자세히 알아보세요.
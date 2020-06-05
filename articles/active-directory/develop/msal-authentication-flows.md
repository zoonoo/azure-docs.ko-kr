---
title: MSAL 인증 흐름 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)에서 사용하는 인증 흐름 및 권한 부여에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47978317b1ae914e952b764def854d8a011293e0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266613"
---
# <a name="authentication-flows"></a>인증 흐름

이 문서에서는 MSAL(Microsoft 인증 라이브러리)에서 제공하는 다양한 인증 흐름을 설명합니다.  이러한 흐름은 다양한 애플리케이션 시나리오에서 사용할 수 있습니다.

| 흐름 | Description | 사용 대상|  
| ---- | ----------- | ------- | 
| [대화형](#interactive) | 브라우저 또는 팝업 창을 통해 사용자에게 자격 증명을 묻는 메시지를 표시하는 대화형 프로세스를 통해 토큰을 가져옵니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md) |
| [암시적 부여](#implicit-grant) | 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 토큰을 가져올 수 있습니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다.| [SPA(단일 페이지 애플리케이션)](scenario-spa-overview.md) |
| [인증 코드](#authorization-code) | 웹 API와 같은 보호된 리소스에 대한 액세스 권한을 얻기 위해 디바이스에 설치된 앱에서 사용됩니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md), [웹앱](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. | [디먼 앱](scenario-daemon-overview.md) |
| [디바이스 코드](#device-code) | 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력이 제한된 디바이스에 로그인할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Windows 통합 인증](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 도메인 또는 Azure AD(Azure Active Directory)에 연결된 컴퓨터의 애플리케이션에서 사용자의 UI 상호 작용 없이 자동으로 토큰을 획득할 수 있습니다.| [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [사용자 이름/암호](scenario-desktop-acquire-token.md#username-and-password) | 암호를 직접 처리하여 애플리케이션에 사용자가 로그인할 수 있도록 허용합니다. 이 흐름은 권장되지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법
 
클라이언트를 구축하는 방법에 따라 Microsoft ID 플랫폼에서 지원되는 인증 흐름 중 하나(또는 여러 개)를 사용할 수 있습니다.  이러한 흐름은 다양한 토큰(id_token, 새로 고침 토큰, 액세스 토큰) 및 인증 코드를 생성하고 작동하는 데 다른 토큰을 요구할 수 있습니다. 이 차트에서는 다음 개요를 제공합니다.
 
|흐름 | 필수 사항 | id_token | 액세스 토큰 | 새로 고침 토큰 | 인증 코드 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[인증 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[새로 고침 토큰 상환](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 새로 고침 토큰 | x | x | x| |
|[On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[디바이스 코드 흐름](v2-oauth2-device-code.md) | | x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x(앱 전용)| | |
 
암시적 모드를 통해 발급된 토큰은 URL(여기서 `response_mode`는 `query` 또는 `fragment`)을 통해 브라우저에 다시 전달되기 때문에 길이 제한이 있습니다.  일부 브라우저는 브라우저 표시줄에 배치할 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패할 수 있습니다.  따라서 이러한 토큰은 `groups` 또는 `wids` 클레임을 포함하지 않습니다.

## <a name="interactive"></a>대화형

MSAL은 사용자에게 로그인에 대한 자격 증명을 대화형으로 요청하고 해당 자격 증명을 사용하여 토큰을 얻을 수 있는 기능을 지원합니다.

![대화형 흐름 다이어그램](media/msal-authentication-flows/interactive.png)

MSAL.NET을 사용하여 특정 플랫폼에서 대화형으로 토큰을 얻는 방법에 대한 자세한 내용은 다음을 참조하세요.
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [유니버설 Windows 플랫폼](msal-net-uwp-considerations.md)

MSAL.js의 대화형 호출에 대한 자세한 내용은 [MSAL.js 대화형 요청의 프롬프트 동작](msal-js-prompt-behavior.md)을 참조하세요.

## <a name="implicit-grant"></a>암시적 부여

MSAL은 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 Microsoft ID 플랫폼에서 토큰을 가져올 수 있도록 하는 [OAuth 2 암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 지원합니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다.

![암시적 허용 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 애플리케이션은 JavaScript 또는 Angular, Vue.js, React.js 같은 SPA 프레임워크를 사용하여 작성되는 클라이언트 쪽 단일 페이지 애플리케이션으로 빌드됩니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다. Microsoft ID 플랫폼을 사용하면 암시적 권한 부여 흐름을 사용하여 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다. 암시적 흐름을 사용하면 애플리케이션에서 ID 토큰을 가져와 인증된 사용자를 나타낼 수 있으며, 보호된 API를 호출하는 데 필요한 토큰에도 액세스할 수 있습니다.

이 인증 흐름에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요하기 때문에 Electron 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다.

## <a name="authorization-code"></a>인증 코드

MSAL은 [OAuth 2 인증 코드 부여](v2-oauth2-auth-code-flow.md)를 지원합니다. 이는 디바이스에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 

사용자가 웹 애플리케이션(웹 사이트)에 로그인하면 웹 애플리케이션은 인증 코드를 수신합니다.  인증 코드는 웹 API를 호출하는 토큰을 얻는 데 사용됩니다. ASP.NET 및 ASP.NET Core 웹앱에서 `AcquireTokenByAuthorizationCode`의 유일한 목표는 토큰 캐시에 토큰을 추가하는 것입니다. 그러면 애플리케이션에서 토큰을 사용할 수 있습니다(일반적으로 컨트롤러에서 `AcquireTokenSilent`를 사용하여 API에 대한 토큰만 가져옴).

![인증 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 액세스 토큰에 대해 사용되는 인증 코드를 요청합니다.
2. 액세스 토큰을 사용하여 웹 API를 호출합니다.

### <a name="considerations"></a>고려 사항

- 토큰을 사용하는 데 인증 코드는 한 번만 사용할 수 있습니다. 동일한 인증 코드를 사용하여 토큰을 여러 번 얻으려고 시도하지 마세요(프로토콜 표준 사양에서 명시적으로 금지됨). 코드를 의도적으로 여러 번 사용하는 경우 또는 프레임워크에서도 여러 번 사용하는 것을 인식하지 못하기 때문에 다음과 같은 오류가 발생합니다. `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- ASP.NET 또는 ASP.NET Core 애플리케이션을 작성하는 경우 이미 인증 코드를 사용한 것을 프레임워크에 알리지 않으면 이 문제가 발생할 수 있습니다. 이를 위해 `AuthorizationCodeReceived` 이벤트 처리기의 `context.HandleCodeRedemption()` 메서드를 호출해야 합니다.

- 액세스 토큰을 ASP.NET과 공유하지 않는 것이 좋습니다. 이로 인해 증분 동의가 올바르게 실행되지 않을 수 있습니다. 자세한 내용은 [문제 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)을 참조하세요.

## <a name="on-behalf-of"></a>On-behalf-of

MSAL은 [OAuth 2 On-behalf-of 인증 흐름](v2-oauth2-on-behalf-of-flow.md)을 지원합니다.  이 흐름은 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 하는 경우에 사용됩니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 대신하여 Microsoft ID 플랫폼에서 액세스 토큰을 보호해야 합니다.

![On-behalf-of 흐름 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램에서:

1. 애플리케이션은 웹 API에 대한 액세스 토큰을 획득합니다.
2. 클라이언트(웹, 데스크톱, 모바일 또는 단일 페이지 애플리케이션)는 HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 추가하여 보호된 웹 API를 호출합니다. 웹 API는 사용자를 인증합니다.
3. 클라이언트에서 웹 API를 호출하면 웹 API는 사용자를 대신해 다른 토큰을 요청합니다.  
4. 보호된 웹 API는 이 토큰을 사용하여 사용자 대신 다운스트림 웹 API를 호출합니다.  웹 API는 나중에 다른 다운스트림 API에 대한 토큰을 요청할 수도 있습니다(계속해서 같은 사용자를 대신함).

## <a name="client-credentials"></a>클라이언트 자격 증명

MSAL은 [OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)을 지원합니다. 이 흐름을 사용하면 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 애플리케이션은 종종 디먼 또는 서비스 계정이라고 합니다. 

클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스(기밀 클라이언트)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 기밀 클라이언트 흐름은 공용 클라이언트 애플리케이션만 지원하기 때문에 모바일 플랫폼(UWP, Xamarin.iOS 및 Xamarin.Android)에서 사용할 수 없습니다. 공용 클라이언트 애플리케이션은 ID 공급자에게 애플리케이션 ID를 증명하는 방법을 모릅니다. 웹앱 또는 웹 API 백 엔드에서 인증서를 배포하여 보안 연결을 수행할 수 있습니다.

MSAL.NET은 두 가지 유형의 클라이언트 자격 증명을 지원합니다. 이러한 클라이언트 자격 증명은 Azure AD에 등록해야 합니다. 자격 증명은 코드에서 기밀 클라이언트 애플리케이션의 생성자에게 전달됩니다.

### <a name="application-secrets"></a>애플리케이션 비밀

![암호를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 애플리케이션 비밀 또는 암호 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="certificates"></a>인증서

![인증서를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 인증서 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

이러한 클라이언트 자격 증명은 다음과 같아야 합니다.
- Azure AD에 등록했습니다.
- 코드에서 기밀 클라이언트 애플리케이션의 생성자에게 전달되어야 합니다.

## <a name="device-code"></a>디바이스 코드

MSAL은 [OAuth 2 디바이스 코드 흐름](v2-oauth2-device-code.md)을 지원합니다. 이 코드를 사용하면 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력 제한된 디바이스에 로그인할 수 있습니다. Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요합니다. 디바이스 코드 흐름을 사용하면 사용자가 다른 디바이스(예: 다른 컴퓨터 또는 휴대폰)를 사용하여 대화형으로 로그인할 수 있습니다. 이 경우 디바이스 또는 운영 체제는 웹 브라우저를 제공하지 않습니다.

애플리케이션은 디바이스 코드 흐름을 사용하여 이러한 디바이스 또는 운영 체제용으로 특별히 설계된 2단계 프로세스를 통해 토큰을 가져옵니다. 이러한 애플리케이션의 예로는 IoT 디바이스 또는 CLI(명령줄 도구)에서 실행되는 애플리케이션이 있습니다. 

![디바이스 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램에서:

1. 사용자 인증이 필요할 때마다 앱은 코드를 제공하고, 다른 디바이스(예: 인터넷에 연결된 스마트폰)를 사용하여 URL(예: `https://microsoft.com/devicelogin`)로 이동하도록 사용자에게 요청합니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시되고, 필요한 경우 동의 확인 프롬프트 및 [다단계 인증](../authentication/concept-mfa-howitworks.md)을 비롯한 일반적인 인증 환경을 진행합니다.

2. 인증에 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="constraints"></a>제약 조건

- 디바이스 코드 흐름은 공용 클라이언트 애플리케이션에서만 사용할 수 있습니다.
- 공용 클라이언트 애플리케이션을 구성할 때 전달되는 인증 기관은 다음 중 하나여야 합니다.
  - 테넌트(`https://login.microsoftonline.com/{tenant}/` 형식, 여기서 `{tenant}`는 테넌트 ID 또는 테넌트와 연결된 도메인을 나타내는 GUID)
  - 회사 및 학교 계정(`https://login.microsoftonline.com/organizations/`)
- Microsoft 개인 계정은 Azure AD v2.0 엔드포인트에서 아직 지원되지 않습니다(`/common` 또는 `/consumers` 테넌트를 사용할 수 없음).

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

MSAL은 도메인에 가입되거나 Azure AD에 가입된 Windows 컴퓨터에서 실행되는 데스크톱 또는 모바일 애플리케이션에 대해 IWA(Windows 통합 인증)를 지원합니다. 이러한 애플리케이션은 IWA를 사용하여 사용자의 UI 상호 작용 없이 토큰을 자동으로 가져올 수 있습니다. 

![Windows 통합 인증 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. Windows 통합 인증을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="constraints"></a>제약 조건

IWA는 페더레이션 사용자(즉, Active Directory에서 만들어지고 Azure AD에서 지원하는 사용자)만 지원합니다. Active Directory 지원(관리되는 사용자) 없이 Azure AD에서 직접 만든 사용자는 이 인증 흐름을 사용할 수 없습니다. 이러한 제한은 [사용자 이름/암호 흐름](#usernamepassword)에 영향을 주지 않습니다.

IWA는 .NET Framework, .NET Core 및 유니버설 Windows 플랫폼용으로 작성된 앱을 위한 것입니다.

IWA는 다단계 인증을 바이패스하지 않습니다. 다단계 인증이 구성된 경우 다단계 인증 질문이 필요하면 IWA가 실패할 수 있습니다. 다단계 인증을 사용하려면 사용자 조작이 필요합니다.

사용자는 ID 공급자가 언제 2단계 인증을 수행하도록 요청하는지 제어하지 않습니다. 테넌트 관리자가 제어합니다. 일반적으로 다른 국가/지역에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되어 있지 않은 경우 2단계 인증이 필요하며 VPN을 통해 연결된 경우에도 간혹 필요한 경우가 있습니다. Azure AD는 AI를 사용하여 2단계 인증이 필요한 경우를 계속 학습합니다. IWA가 실패하면 [대화형 사용자 프롬프트]\(#대화형)로 대체해야 합니다.

공용 클라이언트 애플리케이션을 구성할 때 전달되는 인증 기관은 다음 중 하나여야 합니다.
- 테넌트(`https://login.microsoftonline.com/{tenant}/` 형식, 여기서 `tenant`는 테넌트 ID 또는 테넌트와 연결된 도메인을 나타내는 GUID)
- 회사 및 학교 계정(`https://login.microsoftonline.com/organizations/`) Microsoft 개인 계정은 지원되지 않습니다(`/common` 또는 `/consumers` 테넌트를 사용할 수 없음).

IWA는 자동 흐름이므로 다음 중 하나를 충족해야 합니다.
- 애플리케이션의 사용자가 사전에 애플리케이션 사용에 동의해야 합니다. 
- 테넌트 관리자는 테넌트의 모든 사용자에게 사전에 애플리케이션 사용 동의를 받아야 합니다.

이는 다음 중 하나에 해당함을 의미합니다.
- 개발자가 Azure Portal에서 자신에 대해 **권한 부여**를 선택했습니다.
- 테넌트 관리자가 애플리케이션 등록의 **API 권한** 탭에서 **{테넌트 도메인}에 대한 관리자 동의 권한 부여/해지**를 선택했습니다([웹 API 액세스 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) 참조).
- 사용자가 애플리케이션에 동의하는 방법을 제공했습니다([개별 사용자 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent) 참조).
- 테넌트 관리자가 애플리케이션에 동의하는 방법을 제공했습니다([관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) 참조).

IWA 흐름이 .NET 데스크톱, .NET Core 및 Windows 유니버설 플랫폼 앱에 대해 사용하도록 설정됩니다. .Net Core는 운영 체제에서 사용자 이름을 가져올 수 없기 때문에 IWA에 대한 사용자 이름을 제공해야 합니다.
  
동의에 대한 자세한 내용은 [v2.0 권한 및 동의](v2-permissions-and-consent.md)를 참조하세요.

## <a name="usernamepassword"></a>사용자 이름/암호

MSAL은 [OAuth 2 리소스 소유자 암호 자격 증명 부여](v2-oauth-ropc.md)를 지원하므로 애플리케이션에서 암호를 직접 처리하여 사용자를 로그인할 수 있습니다. 데스크톱 애플리케이션에서 사용자 이름/암호 흐름을 사용하여 토큰을 자동으로 가져올 수 있습니다. 애플리케이션을 사용하는 경우 UI가 필요하지 않습니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 사용자 이름 및 암호를 ID 공급자에게 보내 토큰을 가져옵니다.
2. 토큰을 사용하여 웹 API를 호출합니다.

> [!WARNING]
> 이 흐름은 권장되지 않습니다. 높은 수준의 신뢰 및 사용자 노출이 필요합니다.  보다 안전한 다른 흐름을 사용할 수 없는 경우에만 이 흐름을 사용해야 합니다. 자세한 내용은 [암호 문제 증가에 대한 해결책](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)을 참조하세요. 

Windows 도메인에 가입된 컴퓨터에서 자동으로 토큰을 획득하는 기본 흐름은 [Windows 통합 인증](#integrated-windows-authentication)입니다. 또는 [디바이스 코드 흐름](#device-code)을 사용할 수도 있습니다.

이 흐름은 일부 경우(DevOps 시나리오)에는 유용하지만 고유의 UI를 제공하는 대화형 시나리오에서 사용자 이름/암호를 사용하려는 경우에는 사용하지 않는 것이 좋습니다. 사용자 이름/암호를 사용하여 다음을 수행할 수 없습니다.
- 다단계 인증을 수행해야 하는 사용자는 상호 작용이 없으므로 로그인할 수 없습니다.
- 사용자는 Single Sign-On을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

[Windows 통합 인증 제약 조건](#integrated-windows-authentication) 외에도 다음과 같은 제약 조건이 적용됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행되는 경우에는 이 흐름을 사용할 수 없습니다. 많은 조직이 여기에 해당합니다.
- Microsoft 계정이 아닌 회사 및 학교 계정만 적용됩니다.
- 이 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 유니버설 Windows 플랫폼에서는 사용할 수 없습니다.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 세부 정보

MSAL.NET 및 Azure AD B2C에서 ROPC를 사용하는 방법에 대한 자세한 내용은 [Azure AD B2C에서 ROPC 사용](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)을 참조하세요.

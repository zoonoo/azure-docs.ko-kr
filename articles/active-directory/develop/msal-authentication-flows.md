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
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050504"
---
# <a name="authentication-flows"></a>인증 흐름

이 문서에서는 MSAL(Microsoft 인증 라이브러리)에서 제공하는 다양한 인증 흐름에 대해 설명합니다.  이러한 흐름은 다양한 응용 프로그램 시나리오에서 사용할 수 있습니다.

| 흐름 | 설명 | 사용 대상|  
| ---- | ----------- | ------- | 
| [대화형](#interactive) | 브라우저 또는 팝업 창을 통해 사용자에게 자격 증명을 묻는 대화형 프로세스를 통해 토큰을 가져옵니다. | [데스크톱 앱,](scenario-desktop-overview.md) [모바일 앱](scenario-mobile-overview.md) |
| [암시적 부여](#implicit-grant) | 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 토큰을 얻을 수 있도록 허용합니다. 이렇게 하면 앱이 클라이언트 JavaScript 코드 내에서 사용자에 로그인하고 세션을 유지 관리하며 다른 웹 API에 대한 토큰을 얻을 수 있습니다.| [SPA(단일 페이지 애플리케이션)](scenario-spa-overview.md) |
| [권한 부여 코드](#authorization-code) | 장치에 설치된 앱에서 웹 API와 같은 보호된 리소스에 액세스하는 데 사용됩니다. 이렇게 하면 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. | [데스크톱 앱,](scenario-desktop-overview.md) [모바일 앱,](scenario-mobile-overview.md) [웹 앱](scenario-web-app-call-api-overview.md) | 
| [을 대신하여](#on-behalf-of) | 응용 프로그램은 서비스 또는 웹 API를 호출하며, 이 API는 다른 서비스 또는 웹 API를 호출해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 응용 프로그램의 ID를 사용하여 웹 호스팅 리소스에 액세스할 수 있습니다. 일반적으로 사용자와의 즉각적인 상호 작용 없이 백그라운드에서 실행되어야 하는 서버 간 상호 작용에 사용됩니다. | [데몬 앱](scenario-daemon-overview.md) |
| [디바이스 코드](#device-code) | 사용자가 스마트 TV, IoT 장치 또는 프린터와 같이 입력이 제한된 장치에 로그인할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Windows 통합 인증](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 도메인 또는 Azure Active Directory(Azure AD)의 응용 프로그램이 사용자의 UI 상호 작용 없이 자동으로 토큰을 획득하도록 허용합니다.| [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [사용자 이름/암호](scenario-desktop-acquire-token.md#username-and-password) | 응용 프로그램이 암호를 직접 처리하여 사용자에 로그인할 수 있습니다. 이 흐름은 권장되지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름이 토큰 및 코드를 방출하는 방법
 
클라이언트가 빌드되는 방식에 따라 Microsoft ID 플랫폼에서 지원하는 인증 흐름 중 하나(또는 여러 개)를 사용할 수 있습니다.  이러한 흐름은 권한 부여 코드뿐만 아니라 다양한 토큰(id_tokens, 새로 고침 토큰, 액세스 토큰)을 생성할 수 있으며 작동하도록 하기 위해 다른 토큰이 필요합니다. 이 차트는 개요를 제공합니다.
 
|흐름 | 필요 | id_token | 액세스 토큰 | 토큰 새로 고침 | 인증 코드 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[토큰 사용 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 토큰 새로 고침 | x | x | x| |
|[흐름을 대신하여](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[디바이스 코드 흐름](v2-oauth2-device-code.md) | | x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x(앱 전용)| | |
 
암시적 모드를 통해 발급된 토큰은 URL(위치 `response_mode` `query` 또는 `fragment`있음)을 통해 브라우저로 다시 전달되기 때문에 길이 제한이 있습니다.  일부 브라우저는 브라우저 표시줄에 넣을 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패합니다.  따라서 이러한 토큰에는 `groups` `wids` 클레임이 없거나 클레임이 없습니다.

## <a name="interactive"></a>대화형

MSAL은 사용자에게 자격 증명에 로그인하라는 메시지를 대화식으로 표시하고 해당 자격 증명을 사용하여 토큰을 가져오는 기능을 지원합니다.

![대화형 흐름 다이어그램](media/msal-authentication-flows/interactive.png)

MSAL.NET 사용하여 특정 플랫폼에서 토큰을 대화식으로 획득하는 방법에 대한 자세한 내용은 다음을 참조하십시오.
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [범용 윈도우 플랫폼](msal-net-uwp-considerations.md)

MSAL.js의 대화형 호출에 대한 자세한 내용은 [MSAL.js 대화형 요청의 프롬프트 동작을](msal-js-prompt-behavior.md)참조하십시오.

## <a name="implicit-grant"></a>암시적 부여

MSAL은 [OAuth 2 암시적 권한 부여 흐름을](v2-oauth2-implicit-grant-flow.md)지원하므로 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 Microsoft ID 플랫폼에서 토큰을 얻을 수 있습니다. 이렇게 하면 앱이 클라이언트 JavaScript 코드 내에서 사용자에 로그인하고 세션을 유지 관리하며 다른 웹 API에 대한 토큰을 얻을 수 있습니다.

![암시적 권한 부여 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 응용 프로그램은 JavaScript 또는 Angular, Vue.js 및 React.js와 같은 SPA 프레임워크를 사용하여 작성된 클라이언트 측 단일 페이지 응용 프로그램으로 빌드됩니다. 이러한 응용 프로그램은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 응용 프로그램과 는 다른 인증 특성을 갖습니다. Microsoft ID 플랫폼을 사용하면 단일 페이지 응용 프로그램이 사용자를 로그인하고 토큰을 사용하여 암시적 권한 부여 흐름을 사용하여 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다. 암시적 흐름을 통해 응용 프로그램은 ID 토큰을 사용하여 인증된 사용자를 나타내고 보호된 API를 호출하는 데 필요한 토큰에 액세스할 수 있습니다.

이 인증 흐름에는 기본 플랫폼과의 상호 작용에 대한 추가 기능이 필요하기 때문에 전자 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 응용 프로그램 시나리오는 포함되지 않습니다.

## <a name="authorization-code"></a>인증 코드

MSAL은 [OAuth 2 권한 부여 코드 부여를](v2-oauth2-auth-code-flow.md)지원합니다. 이 권한 부여는 장치에 설치된 앱에서 웹 API와 같은 보호된 리소스에 액세스할 수 있도록 하는 데 사용할 수 있습니다. 이렇게 하면 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 

사용자가 웹 응용 프로그램(웹 사이트에 로그인하면 웹 응용 프로그램은 권한 부여 코드를 받습니다.  권한 부여 코드는 웹 API를 호출하는 토큰을 획득하는 데 사용됩니다. ASP.NET 및 ASP.NET 코어 웹 앱에서 `AcquireTokenByAuthorizationCode` 유일한 목표는 토큰 캐시에 토큰을 추가하는 것입니다. 그런 다음 응용 프로그램에서 토큰을 사용할 수 있습니다(일반적으로 컨트롤러에서 API에 대한 `AcquireTokenSilent`토큰을 사용하여 토큰을 가져옵니다).

![권한 부여 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

앞의 다이어그램에서 응용 프로그램은 다음과 같은

1. 액세스 토큰에 대해 사용되는 권한 부여 코드를 요청합니다.
2. 액세스 토큰을 사용하여 웹 API를 호출합니다.

### <a name="considerations"></a>고려 사항

- 권한 부여 코드를 한 번만 사용하여 토큰을 사용할 수 있습니다. 동일한 권한 부여 코드로 토큰을 여러 번 획득하려고 시도하지 마십시오(프로토콜 표준 사양에 의해 명시적으로 금지됨). 의도적으로 코드를 여러 번 사용하거나 프레임워크가 이를 수행한다는 것을 인식하지 못하기 때문에 다음과 같은 오류가 발생합니다.`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- ASP.NET 또는 ASP.NET Core 응용 프로그램을 작성하는 경우 권한 부여 코드를 이미 사용한 프레임워크에 알리지 않으면 이러한 일이 발생할 수 있습니다. 이를 위해 이벤트 처리기의 `context.HandleCodeRedemption()` 메서드를 `AuthorizationCodeReceived` 호출해야 합니다.

- ASP.NET 액세스 토큰을 공유하지 마십시오. 자세한 내용은 [#693 문제를](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)참조하십시오.

## <a name="on-behalf-of"></a>을 대신하여

MSAL은 [OAuth 2 온-대신 인증 흐름을](v2-oauth2-on-behalf-of-flow.md)지원합니다.  이 흐름은 응용 프로그램이 서비스 또는 웹 API를 호출할 때 사용되며, 이 흐름은 다른 서비스 또는 웹 API를 호출해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스가 다운스트림 서비스에 인증된 요청을 하려면 사용자를 대신하여 Microsoft ID 플랫폼에서 액세스 토큰을 보호해야 합니다.

![흐름을 대신하는 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램에서 다음을 확인할 수 있습니다.

1. 응용 프로그램은 웹 API에 대한 액세스 토큰을 획득합니다.
2. 클라이언트(웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램)는 보호된 웹 API를 호출하여 HTTP 요청의 인증 헤더에 액세스 토큰을 베어러 토큰으로 추가합니다. 웹 API는 사용자를 인증합니다.
3. 클라이언트가 웹 API를 호출하면 웹 API는 사용자를 대신하여 다른 토큰을 요청합니다.  
4. 보호된 웹 API는 이 토큰을 사용하여 사용자를 대신하여 다운스트림 웹 API를 호출합니다.  웹 API는 나중에 다른 다운스트림 API에 대한 토큰을 요청할 수도 있습니다(하지만 여전히 동일한 사용자를 대신하여).

## <a name="client-credentials"></a>클라이언트 자격 증명

MSAL은 [OAuth 2 클라이언트 자격 증명 흐름을](v2-oauth2-client-creds-grant-flow.md)지원합니다. 이 흐름을 사용하면 응용 프로그램의 ID를 사용하여 웹 호스팅 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 애플리케이션은 종종 디먼 또는 서비스 계정이라고 합니다. 

클라이언트 자격 증명 부여 흐름은 웹 서비스(기밀 클라이언트)가 사용자를 가장하는 대신 자체 자격 증명을 사용하여 다른 웹 서비스를 호출할 때 인증할 수 있도록 합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 기밀 클라이언트 흐름은 공용 클라이언트 응용 프로그램만 지원하므로 모바일 플랫폼(UWP, Xamarin.iOS 및 Xamarin.Android)에서는 사용할 수 없습니다. 공용 클라이언트 응용 프로그램은 ID 공급자에게 응용 프로그램의 ID를 증명하는 방법을 알지 못합니다. 인증서를 배포하여 웹 앱 또는 웹 API 백 엔드에서 보안 연결을 수행할 수 있습니다.

MSAL.NET 두 가지 유형의 클라이언트 자격 증명을 지원합니다. 이러한 클라이언트 자격 증명은 Azure AD에 등록해야 합니다. 자격 증명은 코드의 기밀 클라이언트 응용 프로그램의 생성자에게 전달됩니다.

### <a name="application-secrets"></a>애플리케이션 비밀

![암호가 있는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

앞의 다이어그램에서 응용 프로그램은 다음과 같은

1. 응용 프로그램 암호 또는 암호 자격 증명을 사용하여 토큰을 획득합니다.
2. 토큰을 사용하여 리소스를 요청합니다.

### <a name="certificates"></a>인증서

![인증서가 있는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

앞의 다이어그램에서 응용 프로그램은 다음과 같은

1. 인증서 자격 증명을 사용하여 토큰을 획득합니다.
2. 토큰을 사용하여 리소스를 요청합니다.

이러한 클라이언트 자격 증명은 다음과 같은 세세한 있어야 합니다.
- Azure AD에 등록되었습니다.
- 코드의 기밀 클라이언트 응용 프로그램을 구성할 때 전달됩니다.

## <a name="device-code"></a>디바이스 코드

MSAL은 사용자가 스마트 TV, IoT 장치 또는 프린터와 같은 입력 제한 장치에 로그인할 수 있는 [OAuth 2 장치 코드 흐름을](v2-oauth2-device-code.md)지원합니다. Azure AD를 사용 하 고 대화형 인증 웹 브라우저가 필요 합니다. 장치 코드 흐름을 통해 사용자는 다른 장치(예: 다른 컴퓨터 또는 휴대폰)를 사용하여 장치 또는 운영 체제에서 웹 브라우저를 제공하지 않는 대화식으로 로그인할 수 있습니다.

장치 코드 흐름을 사용 하 여 응용 프로그램은 이러한 장치 또는 운영 체제에 대 한 특별히 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 IoT 장치 또는 명령줄 도구(CLI)에서 실행되는 응용 프로그램이 있습니다. 

![장치 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램에서 다음을 확인할 수 있습니다.

1. 사용자 인증이 필요할 때마다 앱은 코드를 제공하고 사용자에게 다른 장치(예: 인터넷에 연결된 스마트폰)를 사용하여 URL(예: `https://microsoft.com/devicelogin`)으로 이동하도록 요청합니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시되고 필요한 경우 동의 프롬프트 및 다단계 인증을 비롯한 정상적인 인증 환경을 통해 진행됩니다.

2. 인증이 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="constraints"></a>제약 조건

- 장치 코드 흐름은 공용 클라이언트 응용 프로그램에서만 사용할 수 있습니다.
- 공용 클라이언트 응용 프로그램을 생성할 때 전달된 권한은 다음 중 하나여야 합니다.
  - 테넌트(테넌트 `{tenant}` ID를 나타내는 GUID 또는 테넌트와 연결된 도메인인 양식). `https://login.microsoftonline.com/{tenant}/`
  - 모든 직장 및 학교`https://login.microsoftonline.com/organizations/`계정에 대 한 ().
- Microsoft 개인 계정은 Azure AD v2.0 끝점에서 아직 지원되지 `/common` 않습니다(또는 `/consumers` 테넌트에서 사용할 수 없습니다).

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

MSAL은 가입된 도메인 또는 Azure AD 가조된 Windows 컴퓨터에서 실행되는 데스크톱 또는 모바일 응용 프로그램에 대한 통합 Windows 인증(IWA)을 지원합니다. 이러한 응용 프로그램은 IWA를 사용하여 사용자의 UI 상호 작용 없이 자동으로 토큰을 획득할 수 있습니다. 

![통합 윈도우 인증 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

앞의 다이어그램에서 응용 프로그램은 다음과 같은

1. 통합 Windows 인증을 사용하여 토큰을 획득합니다.
2. 토큰을 사용하여 리소스를 요청합니다.

### <a name="constraints"></a>제약 조건

IWA는 페더레이션된 사용자만 지원하므로 Active Directory에서 만들어지고 Azure AD에서 지원하는 사용자를 의미합니다. Active Directory 백업(관리되는 사용자)이 없으면 Azure AD에서 직접 만든 사용자는 이 인증 흐름을 사용할 수 없습니다. 이 제한은 사용자 [이름/암호 흐름에](#usernamepassword)영향을 주지 않습니다.

IWA는 .NET 프레임워크, .NET 코어 및 유니버설 Windows 플랫폼용으로 작성된 앱용입니다.

IWA는 다단계 인증을 우회하지 않습니다. 다단계 인증이 구성된 경우 다단계 인증 문제가 필요한 경우 IWA가 실패할 수 있습니다. 다단계 인증에는 사용자 상호 작용이 필요합니다.

ID 공급자가 2단계 인증을 수행하도록 요청하는 시기는 제어할 수 없습니다. 테넌트 관리자가 합니다. 일반적으로 다른 국가에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되지 않은 경우, VPN을 통해 연결된 경우에도 이중 인증이 필요합니다. Azure AD는 AI를 사용하여 이중 인증이 필요한지 지속적으로 학습합니다. IWA에 실패하면 [대화형 사용자 프롬프트](#interactive)로 돌아가야 합니다.

공용 클라이언트 응용 프로그램을 생성할 때 전달된 권한은 다음 중 하나여야 합니다.
- 테넌트(테넌트 `tenant` ID를 나타내는 guid 또는 테넌트와 연결된 도메인인 양식). `https://login.microsoftonline.com/{tenant}/`
- 모든 직장 및 학교`https://login.microsoftonline.com/organizations/`계정에 대 한 (). Microsoft 개인 계정은 지원되지 `/common` 않습니다(사용하거나 `/consumers` 테넌트할 수 있음).

IWA는 자동 흐름이므로 다음 중 하나가 true여야 합니다.
- 응용 프로그램 사용자가 응용 프로그램 사용에 이전에 동의했어야 합니다. 
- 테넌트 관리자는 응용 프로그램을 사용하려면 테넌트의 모든 사용자에게 이전에 동의했어야 합니다.

이는 다음 중 하나가 true임을 의미합니다.
- 개발자는 Azure 포털에서 **권한 부여를** 직접 선택했습니다.
- 테넌트 관리자가 응용 프로그램 등록의 **API 권한** 탭에서 **{tenant 도메인}에 대한 권한 부여/해지 관리자 동의를** 선택했습니다(웹 [API에 액세스할 수 있는 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조).
- 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공했습니다(개별 [사용자 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent)참조).
- 테넌트 관리자가 응용 프로그램에 대해 동의할 수 있는 방법을 제공했습니다(관리자 [동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)참조).

IWA 흐름은 .NET 데스크톱, .NET 코어 및 Windows 유니버설 플랫폼 앱에 대해 활성화됩니다. .NET Core에서는 운영 체제에서 사용자 이름을 가져올 수 없으므로 IWA에 사용자 이름을 제공해야 합니다.
  
동의에 대한 자세한 내용은 [v2.0 권한 및 동의를](v2-permissions-and-consent.md)참조하십시오.

## <a name="usernamepassword"></a>사용자 이름/암호

MSAL은 [OAuth 2 리소스 소유자 암호 자격 증명 부여를](v2-oauth-ropc.md)지원하므로 응용 프로그램이 암호를 직접 처리하여 사용자에 로그인할 수 있습니다. 데스크톱 응용 프로그램에서 사용자 이름/암호 흐름을 사용하여 자동으로 토큰을 획득할 수 있습니다. 응용 프로그램을 사용할 때 UI가 필요하지 않습니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

앞의 다이어그램에서 응용 프로그램은 다음과 같은

1. ID 공급자에게 사용자 이름과 암호를 전송하여 토큰을 획득합니다.
2. 토큰을 사용하여 웹 API를 호출합니다.

> [!WARNING]
> 이 흐름은 권장되지 않습니다. 높은 신뢰와 사용자 노출이 필요합니다.  이 흐름은 보다 안전한 다른 흐름을 사용할 수 없는 경우에만 사용해야 합니다. 자세한 내용은 [증가하는 암호 문제에 대한 해결 방법은 무엇입니까?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

Windows 도메인 에 가입된 컴퓨터에서 토큰을 자동으로 획득하기 위한 기본 흐름은 [통합 Windows 인증입니다.](#integrated-windows-authentication) 그렇지 않으면 장치 [코드 흐름을](#device-code)사용할 수도 있습니다.

경우에 따라 유용하지만(DevOps 시나리오) 사용자 이름/암호를 사용자 고유의 UI를 제공하는 대화형 시나리오에서 사용하려는 경우 사용하지 마십시오. 사용자 이름/암호를 사용하여:
- 다단계 인증을 수행해야 하는 사용자는 상호 작용이 없으므로 로그인할 수 없습니다.
- 사용자는 단일 사인온을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

[통합 Windows 인증 제약 조건](#integrated-windows-authentication)외에도 다음 제약 조건도 적용됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행되는 경우 이 흐름을 사용할 수 없습니다. 많은 조직에서 그렇게 합니다.
- 그것은 직장과 학교 계정 (하지 마이크로 소프트 계정)에 대해서만 작동합니다.
- 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 유니버설 Windows 플랫폼에서는 사용할 수 없습니다.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 세부 사항

MSAL.NET 및 Azure AD B2C 사용에 대한 자세한 내용은 [Azure AD B2C(MSAL.NET)에서 ROPC 사용(ROPC 사용)을](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)참조하십시오.

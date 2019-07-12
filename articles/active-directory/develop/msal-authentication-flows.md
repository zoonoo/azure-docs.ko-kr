---
title: 인증 흐름 (Microsoft 인증 라이브러리) | Azure
description: Microsoft 인증 라이브러리 (MSAL)에서 사용 되는 권한 부여 확인 하 고 인증 흐름에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807203"
---
# <a name="authentication-flows"></a>인증 흐름

이 문서에서는 Microsoft 인증 라이브러리 (MSAL)에서 제공 하는 다양 한 인증 흐름을 설명 합니다.  이러한 흐름은 다양 한 다른 응용 프로그램 시나리오에서에서 사용할 수 있습니다.

| 흐름 | 설명 | 사용 대상|  
| ---- | ----------- | ------- | 
| [대화형](#interactive) | 브라우저 또는 팝업을 통해 자격 증명을 묻는 메시지를 표시 하는 대화형 프로세스를 통해 토큰을 가져옵니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md) |
| [암시적 권한 부여](#implicit-grant) | 응용 프로그램을 백 엔드 서버 자격 증명 교환을 수행 하지 않고 토큰을 가져올 수 있습니다. 이 응용 프로그램을 사용자를 로그인 하 고, 세션을 유지 관리, JavaScript 코드 클라이언트 내에서 모든 다른 웹 Api에 토큰을 가져올 수 있습니다.| [단일 페이지 응용 프로그램 (SPA)](scenario-spa-overview.md) |
| [인증 코드](#authorization-code) | Web Api와 같은 보호 된 리소스에 액세스 하는 장치에 설치 된 앱에서 사용 합니다. 이 옵션을 사용 하면 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. | [데스크톱 앱](scenario-desktop-overview.md)하십시오 [모바일 앱](scenario-mobile-overview.md), [웹 앱](scenario-web-app-call-api-overview.md) | 
| [-대신-의](#on-behalf-of) | 응용 프로그램 서비스 또는 다른 서비스 호출 또는 웹 API에는 웹 API 호출 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 이 기능을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 직접적인 사용자 상호 작용 없이 백그라운드에서 실행 해야 하는 서버-투-서버 상호 작용에 일반적으로 사용 합니다. | [디먼 앱](scenario-daemon-overview.md) |
| [장치 코드](#device-code) | 스마트 TV와 같은 입력 제한 된 장치, IoT 장치 또는 프린터에 로그인 할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Windows 통합 인증](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 자동으로 (UI 상호 작용 없이 사용자 로부터) 토큰을 획득 도메인 또는 Azure Active Directory (Azure AD)에서 응용 프로그램 가입 된 컴퓨터 수 있습니다.| [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [사용자 이름/암호](scenario-desktop-acquire-token.md#username--password) | 응용 프로그램을 사용자가 암호를 직접 처리 하 여 로그인 할 수 있습니다. 이 흐름을 권장 하지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>대화형
MSAL은 사용자에 게 대화형 프롬프트 자격 증명에 대 한 로그인 및 해당 자격 증명을 사용 하 여 토큰을 가져올 수 있는 기능을 지원 합니다.

![대화형 흐름 다이어그램](media/msal-authentication-flows/interactive.png)

MSAL.NET을 사용 하 여 대화형으로 특정 플랫폼에서 토큰을 획득 하려고 하는 방법은 다음을 참조 하세요.
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [유니버설 Windows 플랫폼](msal-net-uwp-considerations.md)

MSAL.js의 대화형 호출은에 대 한 자세한 내용은 참조 하세요. [프롬프트 MSAL.js 대화형 요청에서 동작](msal-js-prompt-behavior.md)합니다.

## <a name="implicit-grant"></a>암시적 부여

MSAL을 지원 합니다 [암시적 OAuth 2 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md)에서 토큰을 가져오는 Microsoft id 플랫폼 백 엔드 서버를 수행 하지 않고 자격 증명 교환을 앱을 허용 하는 합니다. 이 응용 프로그램을 사용자를 로그인 하 고, 세션을 유지 관리, JavaScript 코드 클라이언트 내에서 모든 다른 웹 Api에 토큰을 가져올 수 있습니다.

![암시적 권한 부여 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 응용 프로그램, JavaScript 또는 Angular, Vue.js, React.js 등 SPA 프레임 워크를 사용 하 여 작성 된 클라이언트 쪽, 단일 페이지 응용 프로그램으로 빌드됩니다. 이러한 응용 프로그램이은 웹 브라우저에서 실행 하 고 기존 서버 쪽 웹 응용 프로그램 보다 다양 한 인증 특징이. Microsoft id 플랫폼에는 단일 페이지 응용 프로그램을을 사용자 로그인 및 암시적 허용 흐름을 사용 하 여 백 엔드 서비스 또는 웹 Api에 액세스 하는 토큰을 가져올 수 있습니다. 암시적 흐름을 응용 프로그램을을 보호 된 Api를 호출 하는 데 필요한 토큰에 액세스할 수도 및 인증된 된 사용자를 나타내는 ID 토큰을 가져올 수 있습니다.

필요 하므로 추가 기능 기본 플랫폼과 상호 작용에 대 한이 인증 흐름에서 전자 및 React Native와 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하는 응용 프로그램 시나리오를 포함 하지 않습니다.

## <a name="authorization-code"></a>인증 코드
MSAL을 지원 합니다 [OAuth 2 권한 부여 코드 부여](v2-oauth2-auth-code-flow.md)합니다. 이 권한 부여는 web Api와 같은 보호 된 리소스에 액세스 하는 장치에 설치 된 앱에서 사용할 수 있습니다. 이 옵션을 사용 하면 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 

사용자가 웹 응용 프로그램 (웹 사이트)에 로그인 하는 경우 웹 응용 프로그램 권한 부여 코드를 받습니다.  권한 부여 코드는 웹 Api를 호출 하 여 토큰을 획득 교환 됩니다. ASP.NET 및 ASP.NET Core 웹 앱의 유일한 목표에서 `AcquireTokenByAuthorizationCode` 토큰 캐시에 토큰을 추가 하는 것입니다. 응용 프로그램에서 토큰을 사용할 수 있습니다 (일반적으로 컨트롤러에는 방금 용 토큰을 가져옵니다 API를 사용 하 여 `AcquireTokenSilent`).

![권한 부여 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

위의 다이어그램에서 응용 프로그램:

1. 이것을 교환 하는 액세스 토큰에 대 한 권한 부여 코드를 요청 합니다.
2. Web API를 호출할 액세스 토큰을 사용 합니다.

### <a name="considerations"></a>고려 사항
- 토큰을 교환 하는 권한 부여 코드를 한 번만 사용할 수 있습니다. 여러 번 (것은 명시적으로 금지 프로토콜 표준 사양에서) 동일한 권한 부여 코드를 사용 하는 토큰을 획득 하지 마세요. 사용 하면 코드를 여러 번 의도적 또는 프레임 워크 또한 작업을 수행 하므로 인식할 수 없는 때문에 다음 오류가 표시 됩니다. `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- ASP.NET 또는 ASP.NET Core 응용 프로그램을 작성 하는 경우 권한 부여 코드를 이미 사용 하는 프레임 워크에 지정 하지 않으면이 발생할 수 있습니다. 이 위해 호출 해야 합니다 `context.HandleCodeRedemption()` 메서드는 `AuthorizationCodeReceived` 이벤트 처리기입니다.

- 올바르게 발생 하는 증분 동의 하지 못할 수 있습니다 하는 ASP.NET을 사용 하 여 액세스 토큰을 공유 하지 마세요. 자세한 내용은 [#693 발급](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)합니다.

## <a name="on-behalf-of"></a>-대신-의

MSAL을 지원 합니다 [OAuth 2-대신-의 인증 흐름](v2-oauth2-on-behalf-of-flow.md)합니다.  이 흐름은 응용 프로그램 서비스 또는 다른 서비스 호출 또는 웹 API에는 웹 API 호출 하는 경우 사용 됩니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 다운스트림 서비스에 인증 된 요청을 하려면 중간 계층 서비스에 대 한 Microsoft id 플랫폼에서 사용자 대신 액세스 토큰을 보호 해야 합니다.

![대신-의 흐름 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램:

1. 응용 프로그램 웹 API에 대 한 액세스 토큰을 획득 합니다.
2. 클라이언트 (웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램)는 보호 된 웹 API에 HTTP 요청의 인증 헤더에서 전달자 토큰으로 액세스 토큰 추가 호출 합니다. Web API에서 사용자를 인증 합니다.
3. 클라이언트가 웹 API를 호출 하면 web API 토큰을 요청 다른-대신-의 사용자입니다.  
4. 보호 된 web API이이 토큰을 사용 하 여 사용자-대신-의 다운스트림 web API를 호출 합니다.  Web API 다른 다운스트림 Api에 대 한 (하지만 여전히 동일한 사용자 대신) 이후 요청 토큰을 수도 있습니다.

## <a name="client-credentials"></a>클라이언트 자격 증명

MSAL을 지원 합니다 [OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)합니다. 이 흐름을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 응용 프로그램은 디먼 또는 서비스 계정을 라고도 합니다. 

클라이언트 자격 증명 흐름 허용 다른 웹 서비스를 호출할 때 인증에 사용자를 가장 하는 대신 자체 자격 증명을 사용 하는 웹 서비스 (기밀 클라이언트)을 부여 합니다. 이 시나리오에서는 클라이언트에서는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 공용 클라이언트 응용 프로그램 에서만 지원 하기 때문에 기밀 클라이언트 흐름의 모바일 플랫폼 (UWP, Xamarin.iOS 및 Xamarin.Android)에 사용할 수 없습니다. 공용 클라이언트 응용 프로그램에 Id 공급자 응용 프로그램의 id를 증명 하는 방법을 모릅니다. 웹 앱에 보안 연결을 수행할 수 있습니다 또는 웹 API 백 인증서를 배포 하 여 종료 합니다.

MSAL.NET 두 가지 유형의 클라이언트 자격 증명을 지원합니다. 클라이언트 자격 증명에 이러한 Azure AD를 사용 하 여 등록 해야 합니다. 자격 증명의 기밀 클라이언트 응용 프로그램 코드에서 생성자에 전달 됩니다.

### <a name="application-secrets"></a>애플리케이션 비밀 

![암호를 사용 하 여 기밀 클라이언트의 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

위의 다이어그램에서 응용 프로그램:

1. 응용 프로그램 암호 자격 증명을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

### <a name="certificates"></a>인증서 

![인증서를 사용 하 여 기밀 클라이언트의 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

위의 다이어그램에서 응용 프로그램:

1. 인증서 자격 증명을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

이러한 클라이언트 자격 증명 해야 합니다.
- Azure AD에 등록 합니다.
- 코드에서 기밀 클라이언트 응용 프로그램 생성 시에 전달 합니다.


## <a name="device-code"></a>장치 코드
MSAL을 지원 합니다 [OAuth 2 장치 코드 흐름](v2-oauth2-device-code.md), 스마트 TV와 같은 입력 제한 된 장치, IoT 장치 또는 프린터에 로그인 할 수 있습니다. Azure AD 사용 하 여 대화형 인증 웹 브라우저가 필요 합니다. 장치 코드 흐름에 다른 장치 (예: 다른 컴퓨터 또는 휴대폰)를 사용 하 여 대화형으로 장치 또는 운영 체제를 제공 하지 않습니다 웹 브라우저를 로그인 할 수가 있습니다.

장치 코드 흐름을 사용 하면 응용 프로그램이 특히 이러한 장치 또는 운영 체제를 위한 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로 IoT 장치 또는 명령줄 도구 (CLI)에서 실행 되는 있습니다. 

![장치 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램:

1. 사용자 인증이 필요할 때마다 앱 코드를 제공 하 고 다른 장치 (예: 인터넷에 연결 된 스마트폰)를 사용 하 여 URL로 이동 하도록 요청 합니다 (예를 들어 https://microsoft.com/devicelogin) 합니다. 사용자는 다음 코드를 입력 하 라는 메시지가 표시 됩니다 하 고 동의 확인 프롬프트 및 필요한 경우 multi-factor authentication을 포함 하 여 일반 인증 경험을 통해 진행 됩니다.

2. 인증이 완료 되 면 명령줄 앱 인 백 채널을 통해 필요한 토큰을 받고 하는 데 필요한 웹 API 호출을 수행 하는 데 사용 합니다.

### <a name="constraints"></a>제약 조건

- 장치 코드 흐름 에서만 공용 클라이언트 응용 프로그램에 제공 됩니다.
- 다음 중 하나 여야 합니다 공용 클라이언트 응용 프로그램을 생성할 때 전달 된 인증 기관:
  - 테 넌 트 (양식의 `https://login.microsoftonline.com/{tenant}/` 여기서 `{tenant}` 테 넌 트와 연결 된 도메인 또는 테 넌 트 ID를 나타내는 guid)입니다.
  - 에 대 한 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`).
- 개인 Microsoft 계정과 Azure AD v2.0 끝점에서 아직 지원 되지 않습니다 (사용할 수 없습니다는 `/common` 또는 `/consumers` 테 넌 트)입니다.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증
도메인 가입 또는 Azure AD에서 실행 되는 모바일 응용 프로그램에 연결 된 Windows 컴퓨터 또는 MSAL 데스크톱에 대 한 Windows 통합 인증 (IWA)을 지원 합니다. IWA를 사용 하 여 이러한 응용 프로그램 없이 토큰을 자동으로 (사용자의 모든 UI 상호 작용)를 획득할 수 있습니다. 

![통합된 Windows 인증의 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

위의 다이어그램에서 응용 프로그램:

1. 통합 Windows 인증을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

### <a name="constraints"></a>제약 조건

IWA 페더레이션된 사용자만, 즉 Azure AD에서 지 원하는 Active Directory에서 만든 사용자를 지원 합니다. 백업 Active Directory 없이도 Azure AD에서 직접 만든 사용자 (관리 되는 사용자)이 인증 흐름을 사용할 수 없습니다. 이 제한에 영향을 주지는 [사용자 이름/암호 흐름](#usernamepassword)합니다.

IWA는.NET Framework,.NET Core 및 유니버설 Windows 플랫폼 플랫폼용으로 작성 된 앱입니다.

IWA는 multi-factor authentication을 바이패스 하지 않습니다. 다단계 인증으로 구성 된 경우 multi-factor authentication 챌린지 필요한 경우 IWA 실패할 수 있습니다. Multi-factor authentication 사용자가 개입을 해야 합니다.

Id 공급자 수행할 2 단계 인증을 요청 하는 경우를 제어 하지 않습니다. 테 넌 트 관리자는 다음 작업을 수행 하지 않습니다. 일반적으로 2 단계 인증이 필요 다른 국가에서 로그인 할 때, 경우 연결 되지 않은 VPN을 통해 회사 네트워크 및 VPN을 통해 연결 되어 있을 때 더욱 그렇습니다. Azure AD는 AI를 사용 하 여 계속 해 서 2 단계 인증이 필요한 경우에 대해 알아봅니다. IWA 못하면 있습니다 해야 대체 [대화형 사용자 프롬프트] (#interactive).

다음 중 하나 여야 합니다 공용 클라이언트 응용 프로그램을 생성할 때 전달 된 인증 기관:
- 테 넌 트 (양식의 `https://login.microsoftonline.com/{tenant}/` 여기서 `tenant` 테 넌 트와 연결 된 도메인 또는 테 넌 트 ID를 나타내는 guid)입니다.
- 에 대 한 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`). 개인 Microsoft 계정이 지원 되지 않습니다 (사용할 수 없습니다 `/common` 또는 `/consumers` 테 넌 트)입니다.

IWA 자동 흐름 이기 때문에 다음 중 하나에 적용 되어야 합니다.
- 응용 프로그램을 사용할 응용 프로그램의 사용자를 이전에 동의한 해야 합니다. 
- 테 넌 트 관리자는 응용 프로그램을 사용 하도록 테 넌 트에서 모든 사용자에 게 이전에 동의한 해야 합니다.

즉, 다음 중 하나는 true.
- 개발자로 서 선택한 **부여** 자신에 대 한 Azure portal에서.
- 테 넌 트 관리자가 선택한 **{테 넌 트 도메인}에 대 한 Grant/revoke 관리자 동의** 에 **API 사용 권한** 응용 프로그램 등록의 탭 (참조 [웹 Api에 액세스 권한을 추가 합니다. ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- 응용 프로그램에 동의 하는 사용자가 제공한 (참조 [개별 사용자의 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- 테 넌 트 관리자가 응용 프로그램에 대 한 동의 하는 방법을 제공한 (참조 [관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

.NET 데스크톱,.NET Core 및 Windows 유니버설 플랫폼 앱에 대 한 IWA 흐름 활성화 됩니다. .NET Core에서 사용자를 갖는 오버 로드에만 사용할 수 있는 경우 .NET Core 플랫폼 운영 체제에 username을 요청할 수 없습니다.
  
동의에 대 한 자세한 내용은 참조 하세요. [v2.0 권한 및 동의](v2-permissions-and-consent.md)합니다.

## <a name="usernamepassword"></a>사용자 이름/암호 
MSAL을 지원 합니다 [OAuth 2 리소스 소유자 암호 자격 증명 부여](v2-oauth-ropc.md), 직접 자신의 암호를 처리 하 여 사용자를 로그인에 응용 프로그램을 허용 하는. 데스크톱 응용 프로그램에서 자동으로 토큰을 획득 하는 사용자 이름/암호 흐름을 사용할 수 있습니다. UI 응용 프로그램을 사용할 때 필요 합니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

위의 다이어그램에서 응용 프로그램:

1. Id 공급자를 사용자 이름 및 암호를 전송 하 여 토큰을 획득 합니다.
2. 토큰을 사용 하 여 web API를 호출 합니다.

> [!WARNING]
> 이 흐름을 권장 하지 않습니다. 높은 수준의 신뢰 및 사용자 노출 해야합니다.  다른, 보다 안전한 흐름을 사용할 수 없는 경우에이 흐름을 사용 해야 합니다. 자세한 내용은 [암호의 증가 문제를 해결 하려면 란?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)합니다. 

Windows 도메인에 가입 된 컴퓨터에서 자동으로 토큰 획득 하기 위해 기본 흐름은 다음과 [통합 Windows 인증](#integrated-windows-authentication)합니다. 그렇지 않은 경우 이용할 수 있습니다 [장치 코드 흐름](#device-code)합니다.

경우에이 유용한 경우도 (DevOps 시나리오) UI를 직접 입력할 수 있는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우를 방지 하려고 합니다. 사용 하 여 사용자 이름/암호:
- 다단계 인증을 수행 해야 하는 사용자 (개입 없이 하므로)에 로그인 할 수 없습니다.
- 사용자가 single sign on 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

별도로 합니다 [통합 Windows 인증 제약 조건](#integrated-windows-authentication), 다음과 같은 제약 조건이 적용:

- 사용자 이름/암호 흐름은 조건부 액세스 및 multi-factor authentication을 사용 하 여 호환 되지 않습니다. 결과적으로 앱을 실행 하면 Azure AD 테 넌 트에서 테 넌 트 관리자가 multi-factor authentication 인증을 필요로 하는이 흐름을 사용할 수 없습니다. 대부분의 조직에서는 이렇게 합니다.
- 회사 및 학교 계정 (되지 Microsoft 계정)에 대해서만 작동합니다.
- 흐름은.NET 데스크톱 및.NET Core에 없는 유니버설 Windows 플랫폼에서 사용할 수 있습니다.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 세부 정보

MSAL.NET 및 Azure AD B2C 사용에 대 한 자세한 내용은 참조 하세요. [ROPC를 사용 하 여 Azure AD B2C (MSAL.NET)을 사용 하 여](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)입니다.

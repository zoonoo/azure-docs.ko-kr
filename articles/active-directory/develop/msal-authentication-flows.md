---
title: 인증 흐름 (Microsoft 인증 라이브러리) | Azure
description: 인증 흐름/권한 부여를 사용 하 여 인증 라이브러리 (MSAL (Microsoft)에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: b7db73ff8bef553b36408cfae90e32014f875bd3
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190996"
---
# <a name="authentication-flows"></a>인증 흐름

이 문서에서는 Microsoft 인증 라이브러리 (MSAL)에서 제공 하는 다양 한 인증 흐름을 설명 합니다.  이러한 흐름은 다양 한 다른 응용 프로그램 시나리오에서에서 사용할 수 있습니다.

| 흐름 | 설명 | 사용 위치|  
| ---- | ----------- | ------- | 
| [대화형](#interactive) | 팝업 창 또는 브라우저를 통해 자격 증명을 묻는 메시지를 표시 하는 대화형 프로세스를 통해 토큰을 가져옵니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md) |
| [암시적 권한 부여](#implicit-grant) | 응용 프로그램을 백 엔드 서버 자격 증명 교환을 수행 하지 않고 토큰을 가져올 수 있습니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다.| [단일 페이지 응용 프로그램 (SPA)](scenario-spa-overview.md) |
| [인증 코드](#authorization-code) | Web Api와 같은 보호 된 리소스에 액세스 하는 장치에 설치 된 앱에서 사용 합니다. 이 로그인 하 고 모바일 및 데스크톱 앱에 대 한 API 액세스를 추가할 수 있습니다. | [데스크톱 앱](scenario-desktop-overview.md)하십시오 [Mobile apps](scenario-mobile-overview.md), [웹 앱](scenario-web-app-call-api-overview.md) | 
| [-대신-의](#on-behalf-of) | 응용 프로그램 호출 서비스/웹 API를 차례로 다른 서비스/웹 API를 호출 해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 이 기능을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 직접적인 사용자 상호 작용 없이 백그라운드에서 실행 해야 하는 서버-투-서버 상호 작용에 일반적으로 사용 합니다. | [디먼 앱](scenario-daemon-overview.md) |
| [장치 코드](#device-code) | 스마트 TV와 같은 입력 제한 된 장치, IoT 장치 또는 프린터에 로그인 할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [통합된 Windows 인증](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 도메인 또는 Azure AD 응용 프로그램을 자동으로 (UI 상호 작용 없이 사용자 로부터) 토큰을 획득 하는 컴퓨터를 가입을 허용 합니다.| [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [사용자 이름/암호](scenario-desktop-acquire-token.md#username--password) | 응용 프로그램을 사용자가 암호를 직접 처리 하 여 로그인 할 수 있습니다. 이 흐름은 권장 되지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>대화형
MSAL은 대화형으로 로그인 하 고 해당 자격 증명을 사용 하 여 토큰을 가져올 자격 증명에 대 한 사용자에 게 하는 기능을 지원 합니다.

![대화형 흐름](media/msal-authentication-flows/interactive.png)

MSAL.NET을 사용 하 여 대화형으로 특정 플랫폼에서 토큰을 획득 하려고 하는 방법은 다음과 같습니다.
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [유니버설 Windows 플랫폼](msal-net-uwp-considerations.md)

에 대 한 자세한 내용은 MSAL.js의 대화형 호출은, [프롬프트 MSAL.js 대화형 요청에서 동작](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>암시적 부여

MSAL을 지원 합니다 [암시적 OAuth 2 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md)에서 토큰을 가져오는 Microsoft id 플랫폼 백 엔드 서버를 수행 하지 않고 자격 증명 교환을 앱을 허용 하는 합니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다.

![암시적 허용 흐름](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 응용 프로그램은 JavaScript 또는 Angular, Vue.js, React.js 등 SPA 프레임 워크를 사용 하 여 작성 하는 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 이러한 응용 프로그램이은 웹 브라우저에서 실행 하 고 기존 서버 쪽 웹 응용 프로그램 보다 다양 한 인증 특징이. Microsoft id 플랫폼에는 단일 페이지 응용 프로그램을 사용자를 로그인 하 고 백 엔드 서비스 또는 웹 Api 암시적 허용 흐름을 사용 하 여 액세스 토큰을 가져올 수 있습니다. 암시적 흐름을 응용 프로그램을을 보호 된 Api를 호출 하는 데 필요한 토큰에 액세스할 수도 및 인증된 된 사용자를 나타내는 ID 토큰을 가져올 수 있습니다.

이 인증 흐름에서 필요 하므로 추가 기능 기본 플랫폼과 상호 작용에 대 한 전자 및 React Native와 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하 여 응용 프로그램 시나리오를 다루지 않습니다.

## <a name="authorization-code"></a>인증 코드
MSAL을 지원 합니다 [OAuth 2 권한 부여 코드 부여](v2-oauth2-auth-code-flow.md), web Api와 같은 보호 된 리소스에 액세스 하는 장치에 설치 된 앱에서 사용할 수 있습니다. 이 로그인 하 고 모바일 및 데스크톱 앱에 대 한 API 액세스를 추가할 수 있습니다. 

사용자가 웹 응용 프로그램 (웹 사이트)에 로그인 하는 경우 웹 응용 프로그램 권한 부여 코드를 받습니다.  권한 부여 코드는 웹 Api를 호출 하 여 토큰을 획득 교환 됩니다. ASP.NET에서 ASP.NET core 웹 앱과의 유일한 목표 / `AcquireTokenByAuthorizationCode` 만 사용 하 여 API에 대 한 토큰을 가져올 응용 프로그램 (일반적으로 컨트롤러)에 다음 수 있도록 토큰을 토큰 캐시에 추가 하는 것 `AcquireTokenSilent`입니다.

![권한 부여 코드 흐름](media/msal-authentication-flows/authorization-code.png)

1. 이것을 교환 하는 액세스 토큰에 대 한 권한 부여 코드를 요청 합니다.
2. Web API를 호출할 액세스 토큰을 사용 합니다.

### <a name="considerations"></a>고려 사항
- 권한 부여 코드를 사용할 수 있는 한 번만 토큰으로 교환할 합니다. 여러 번 (것은 명시적으로 금지 프로토콜 표준 사양에서) 동일한 권한 부여 코드를 사용 하는 토큰을 획득 하려고 시도 하지 마세요. 사용 하면 코드를 여러 번 의도적 또는 프레임 워크 또한 작업을 수행 하므로 인식 되지 않으므로 오류가 표시 됩니다. `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- ASP.NET/ASP.NET Core 응용 프로그램을 작성 하는 경우 이미 권한 부여 코드를 사용 했습니다. ASP.NET/Core 프레임 워크에는 지정 하지 않으면이 발생할 수 있습니다. 이 위해 호출할 필요가 `context.HandleCodeRedemption()` 메서드는 `AuthorizationCodeReceived` 이벤트 처리기입니다.

- 올바르게 발생 하는 증분 동의 하지 못할 수 있습니다 하는 ASP.NET을 사용 하 여 액세스 토큰을 공유 하지 마세요.  자세한 내용은 [#693 발급](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)합니다.

## <a name="on-behalf-of"></a>-대신-의

MSAL을 지원 합니다 [OAuth 2-대신-의 인증 흐름](v2-oauth2-on-behalf-of-flow.md)합니다.  이 흐름에는 응용 프로그램 호출 서비스/웹 API를 차례로 다른 서비스/웹 API를 호출 해야 하는 경우 사용 됩니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 다운스트림 서비스에 인증 된 요청을 하려면 중간 계층 서비스에 대 한 Microsoft id 플랫폼에서 사용자 대신 액세스 토큰을 보호 해야 합니다.

![On-Behalf-Of 흐름](media/msal-authentication-flows/on-behalf-of.png)

1. 웹 API에 대 한 액세스 토큰을 획득합니다.
2. 클라이언트 (웹, 데스크톱, 모바일, 단일 페이지 응용 프로그램)는 HTTP 요청의 인증 헤더에서 전달자 토큰으로 액세스 토큰을 추가, 보호 된 Web API를 호출 합니다. Web API에서 사용자를 인증 합니다.
3. Web API를 호출 하는 클라이언트에 Web API 사용자-대신-의 다른 토큰 요청 합니다.  
4. 이 토큰을 사용 하 여 사용자-대신-의 다운스트림 웹 API를 호출 하는 보호 된 Web API입니다.  나중에 Web API는 다른 다운스트림 Api에 대 한 (하지만 여전히 동일한 사용자 대신) 토큰을 요청할 수 있습니다.

## <a name="client-credentials"></a>클라이언트 자격 증명

MSAL을 지원 합니다 [OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)합니다. 이 흐름을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 응용 프로그램은 디먼 또는 서비스 계정을 라고도 합니다. 

클라이언트 자격 증명 흐름 허용 다른 웹 서비스를 호출할 때 인증에 사용자를 가장 하는 대신 자체 자격 증명을 사용 하는 웹 서비스 (기밀 클라이언트)을 부여 합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 공용 클라이언트 응용 프로그램 에서만 지원 되므로 기밀 클라이언트 흐름의 모바일 플랫폼 (UWP, Xamarin.iOS 및 Xamarin.Android)에 제공 되지 않습니다.  공용 클라이언트 응용 프로그램에 Id 공급자 응용 프로그램의 id를 증명 하는 방법을 모릅니다. 웹 앱에 보안 연결을 수행할 수 있습니다 또는 인증서를 배포 하 여 web API 백 엔드가 있습니다.

MSAL.NET 두 가지 유형의 클라이언트 자격 증명을 지원합니다. 클라이언트 자격 증명에 이러한 Azure AD를 사용 하 여 등록 해야 합니다. 자격 증명의 기밀 클라이언트 응용 프로그램 코드에서 생성자에 전달 됩니다.

### <a name="application-secrets"></a>애플리케이션 비밀 

![암호를 사용 하 여 기밀 클라이언트](media/msal-authentication-flows/confidential-client-password.png)

1. 응용 프로그램 암호/암호 자격 증명을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

### <a name="certificates"></a>인증서 

![인증서를 사용 하 여 기밀 클라이언트](media/msal-authentication-flows/confidential-client-certificate.png)

1. 인증서 자격 증명을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

이러한 클라이언트 자격 증명 해야 합니다.
- Azure AD에 등록 합니다.
- 코드에서 기밀 클라이언트 응용 프로그램 생성 시에 전달 합니다.


## <a name="device-code"></a>장치 코드
MSAL을 지원 합니다 [OAuth 2 장치 코드 흐름](v2-oauth2-device-code.md), 스마트 TV와 같은 입력 제한 된 장치, IoT 장치 또는 프린터에 로그인 할 수 있습니다. Azure AD 사용 하 여 대화형 인증 웹 브라우저가 필요 합니다. 장치 코드 흐름에 다른 장치 (예: 다른 컴퓨터 또는 휴대폰)를 사용 하 여 대화형으로 장치 또는 운영 체제를 제공 하지 않습니다 웹 브라우저를 로그인 할 수가 있습니다.

장치 코드 흐름을 사용 하면 응용 프로그램이 특히 이러한 장치/OS를 위한 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로 iOT 장치 또는 명령줄 도구 (CLI)에서 실행 되는 응용 프로그램. 

![디바이스 코드 흐름](media/msal-authentication-flows/device-code.png)

1. 사용자 인증이 필요할 때마다 앱 코드를 제공 하 고 다른 장치 (예: 인터넷에 연결 된 smartphone)를 사용 하 여 URL로 이동 하도록 요청 합니다 (예를 들어 http://microsoft.com/devicelogin)있는 사용자를 될 하 라는 메시지가 표시 코드를 입력 합니다. 완료 웹 페이지 인해 필요한 경우 동의 확인 프롬프트 및 multi-factor authentication을 포함 하는 일반적인 인증 환경 안내 합니다.

2. 인증이 성공 하면 필요한 웹 API 호출을 수행 하는 데 사용할 및 명령줄 앱 인 백 채널을 통해 필요한 토큰을 받게 됩니다.

### <a name="constraints"></a>제약 조건

- 장치 코드 흐름 에서만 공용 클라이언트 응용 프로그램에 제공 됩니다.
- 공용 클라이언트 응용 프로그램을 생성 해야 하는 경우 기관에 전달:
  - 테 넌 트 (양식의 `https://login.microsoftonline.com/{tenant}/` 여기서 `{tenant}` 테 넌 트와 연결 된 도메인 또는 테 넌 트 ID를 나타내는 GUID입니다.
  - 또는 모든 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`).
- 개인 Microsoft 계정과 Azure AD v2.0 끝점에서 아직 지원 되지 않습니다 (사용할 수 없습니다는 `/common` 또는 `/consumers` 테 넌 트)입니다.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증
도메인 가입 또는 Azure AD에서 실행 되는 모바일 응용 프로그램에 연결 된 Windows 컴퓨터 또는 MSAL 데스크톱에 대 한 Windows 통합 인증 (IWA)을 지원 합니다. IWA를 사용 하 여 이러한 응용 프로그램 없이 토큰을 자동으로 (사용자의 모든 UI 상호 작용)를 획득할 수 있습니다. 

![Windows 통합 인증](media/msal-authentication-flows/integrated-windows-authentication.png)

1. 통합 Windows 인증을 사용 하 여 토큰을 획득 합니다.
2. 리소스의 요청에 토큰을 사용 합니다.

### <a name="constraints"></a>제약 조건

IWA 지원 **페더레이션** 사용자만 합니다.  사용자는 Active Directory에서 만든 및 Azure Active Directory에서 지원 합니다. AD 백업 하지 않고 Azure AD에서 직접 만든 사용자 (**관리 되는** 사용자)이 인증 흐름을 사용할 수 없습니다. 이 제한에 영향을 주지 않습니다 합니다 [사용자 이름/암호 흐름](#usernamepassword)합니다.

IWA는.NET Framework,.NET Core 및 유니버설 Windows 플랫폼 플랫폼용으로 작성 된 앱입니다.

IWA는 MFA (다단계 인증)를 무시 되지 않습니다. MFA를 구성 하는 경우 MFA 사용자 상호 작용을 필요로 하기 때문에 MFA 챌린지를 필요한 경우 IWA 실패할 수 있습니다. 이 항목은 까다롭습니다. IWA 비 대화형 이지만 2 단계 인증 (2FA) 기능은 사용자 상호 작용 해야 합니다. Id 공급자 요청 수행 하는 데 2fa가 제어 하지 않으면 테 넌 트 관리자입니다. 이 관찰에서 VPN을 통해 회사 네트워크에 연결 되지 않음 및 경우에 따라 VPN을 통해 연결 하는 경우에 하는 경우 다른 국가에서 로그인 하는 경우 2fa가 필요 합니다. 규칙 집합을 결정적으로 예상 하, Azure Active Directory AI를 사용 하 여 지속적으로 2FA가 필요한 경우에 대해 알아봅니다. 사용자 프롬프트를 대체 해야 합니다 (https://aka.ms/msal-net-interactive) IWA 실패 하는 경우.

공용 클라이언트 응용 프로그램을 생성 해야 하는 경우 기관에 전달:
- 테 넌 트 (양식의 `https://login.microsoftonline.com/{tenant}/` 여기서 `tenant` 테 넌 트와 연결 된 도메인 또는 테 넌 트 ID를 나타내는 guid입니다.
- 에 대 한 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`). 개인 Microsoft 계정이 지원 되지 않습니다 (사용할 수 없습니다 `/common` 또는 `/consumers` 테 넌 트)입니다.

IWA 자동 흐름 이므로:
- 응용 프로그램을 사용할 응용 프로그램의 사용자를 이전에 동의한 해야 합니다. 
- 또는 응용 프로그램을 사용 하려면 테 넌 트에서 모든 사용자에 게 테 넌 트 관리자를 이전에 동의한 해야 합니다.
- 이는 다음을 의미합니다.
    - 개발자로 서 사용자가 누른 하거나 합니다 **부여** 자신에 대 한 Azure portal에서 단추 
    - 또는 테 넌 트 관리자가 눌렀습니다 합니다 **{테 넌 트 도메인}에 대 한 Grant/revoke 관리자 동의** 단추를 **API 사용 권한** 응용 프로그램 등록의 탭 (참조 [권한을 추가 액세스 web Api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - 또는 응용 프로그램에 동의 하는 사용자가 제공한 (참조 [개별 사용자의 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - 테 넌 트 관리자가 응용 프로그램에 대 한 동의 하는 방법을 제공 하거나 (참조 [관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

.NET 데스크톱,.NET Core 및 Windows 유니버설 플랫폼 앱에 대 한 IWA 흐름 활성화 됩니다. .NET Core에서 사용자를 갖는 오버 로드만은 사용할 수 있는.NET Core 플랫폼 os username을 요청할 수 없습니다.
  
동의에 대 한 자세한 내용은 참조 하세요. [v2.0 권한 및 동의](v2-permissions-and-consent.md)합니다.

## <a name="usernamepassword"></a>사용자 이름/암호 
MSAL을 지원 합니다 [OAuth 2 리소스 소유자 암호 자격 증명 부여](v2-oauth-ropc.md), 직접 자신의 암호를 처리 하 여 사용자를 로그인에 응용 프로그램을 허용 하는. 데스크톱 응용 프로그램에서 자동으로 토큰을 획득 하는 사용자 이름/암호 흐름을 사용할 수 있습니다. UI 응용 프로그램을 사용할 때 필요 합니다.

![사용자 이름/암호 흐름](media/msal-authentication-flows/username-password.png)

1. Id 공급자를 사용자 이름 및 암호를 전송 하 여 토큰을 획득 합니다.
2. 웹 토큰을 사용 하 여 API를 호출 합니다.

> [!WARNING]
> 이 흐름은 다음과 **좋지** 높은 수준의 신뢰 및 사용자 노출 필요 하므로 합니다.  다른, 보다 안전한 흐름을 사용할 수 없는 경우에이 흐름을 사용 해야 합니다. 이 문제에 대 한 자세한 내용은 참조 하세요. [이 문서에서는](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)합니다. 

Windows 도메인에 가입 된 컴퓨터에서 자동으로 토큰 획득 하기 위해 기본 흐름은 다음과 [통합 Windows 인증](#integrated-windows-authentication)합니다. 그렇지 않은 경우 사용할 수도 있습니다 [장치 코드 흐름](#device-code)

경우에이 유용한 경우도 (DevOps 시나리오) UI를 직접 입력할 수 있는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우 실제로 벗어나면 이동 하는 방법에 대 한 간주 해야 합니다. 사용자 이름/암호를 사용 하 여 사용자는 제공 접속 다양 한 작업:
- 최신 id의 테 넌 트 핵심: 암호 fished 가져옵니다 재생 합니다. 이 개념을 가로챌 수 있는 공유 암호를 지정 했으므로 합니다.
암호 없는 호환 되지 않습니다.
- MFA를 수행 해야 하는 사용자 (개입 없이 하므로)에 로그인 할 수 없습니다.
- 사용자가 single sign on 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

별도로 합니다 [통합 Windows 인증 제약 조건](#integrated-windows-authentication), 다음과 같은 제약 조건이 적용:

- 사용자 이름/암호 흐름 조건부 액세스 및 multi-factor authentication을 사용 하 여 호환 되지 않습니다. 결과적으로 앱을 실행 하면 Azure AD 테 넌 트에서 테 넌 트 관리자가 multi-factor authentication 인증을 필요로 하는이 흐름을 사용할 수 없습니다. 대부분의 조직에서는 이렇게 합니다.
- 작업에 대해서만 작동 및 학교 계정 (MSA)
- 흐름은.NET 데스크톱 및.NET core에 없는 유니버설 Windows 플랫폼에서 사용할 수 있습니다.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 세부 정보

MSAL.NET 및 Azure AD B2C 사용에 대 한 자세한 내용은 읽을 [ROPC를 사용 하 여 Azure AD B2C (MSAL.NET)을 사용 하 여](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)입니다.

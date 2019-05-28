---
title: 토큰 관리(Microsoft 인증 라이브러리) | Azure
description: MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰을 획득 및 캐시하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546058"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>MSAL을 사용하여 토큰 획득 및 캐싱
[액세스 토큰](access-tokens.md)을 사용하면 클라이언트에서 Azure를 통해 보호되는 웹 API를 안전하게 호출할 수 있습니다. MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰을 획득하는 방법에는 여러 가지가 있습니다. 몇 가지 방법에는 웹 브라우저를 통한 사용자 상호 작용이 필요합니다. 일부 방법에는 사용자 상호 작용이 필요하지 않습니다. 일반적으로 토큰을 획득하는 방법은 애플리케이션이 공용 클라이언트 애플리케이션(데스크톱 또는 모바일 앱) 또는 기밀 클라이언트 애플리케이션(Web App, Web API 또는 Windows 서비스와 같은 디먼 애플리케이션)인지에 따라 달라집니다.

MSAL은 토큰을 획득한 후에 캐시합니다.  애플리케이션 코드는 다른 방법으로 토큰을 획득하기 전에 먼저 캐시에서 토큰을 자동으로 가져오도록 시도해야 합니다.

또한 캐시에서 계정을 제거하여 획득한 토큰 캐시를 지울 수도 있습니다. 이 경우 브라우저에 있는 세션 쿠키는 제거되지 않습니다.

## <a name="scopes-when-acquiring-tokens"></a>토큰 획득 시 범위
[범위](v2-permissions-and-consent.md)는 클라이언트 애플리케이션에서 액세스를 요청하기 위해 웹 API에서 공개하는 권한입니다. 클라이언트 애플리케이션은 웹 API에 액세스하기 위해 토큰을 가져오도록 인증 요청을 할 때 이러한 범위에 대한 사용자의 동의를 요청합니다. MSAL을 사용하면 토큰에서 개발자용 Azure AD(v1.0) 및 Microsoft ID 플랫폼(v2.0) API에 액세스하기 위한 토큰을 가져올 수 있습니다. v2.0 프로토콜은 요청에서 리소스 대신 범위를 사용합니다. 자세한 내용은 [v1.0 및 v2.0 비교](active-directory-v2-compare.md)를 참조하세요. v2.0 엔드포인트는 허용하는 토큰 버전에 대한 웹 API의 구성에 따라 액세스 토큰을 MSAL에 반환합니다.

많은 MSAL 토큰 획득 메서드의 경우 *범위* 매개 변수가 필요합니다. 이 매개 변수는 원하는 권한 및 요청된 리소스를 선언하는 간단한 문자열 목록입니다. 잘 알려진 범위는 [Microsoft Graph 권한](/graph/permissions-reference)입니다.

또한 MSAL에서는 v1.0 리소스에도 액세스할 수 있습니다. 자세한 내용은 [v1.0 애플리케이션에 대한 범위](msal-v1-app-scopes.md)를 참조하세요.

### <a name="request-specific-scopes-for-a-web-api"></a>웹 API에 대한 특정 범위 요청
애플리케이션에서 리소스 API에 대한 특정 권한이 있는 토큰을 요청해야 하는 경우 API의 앱 ID URI가 포함된 범위를 *&lt;앱 ID URI&gt;/&lt;scope&gt;* 형식으로 전달해야 합니다.

예를 들어 Microsoft Graph API에 대한 범위가 `https://graph.microsoft.com/User.Read`이거나,

사용자 지정 웹 PI에 대한 범위가 `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`입니다.

Microsoft Graph API의 경우에만 범위 값 `user.read`는 `https://graph.microsoft.com/User.Read` 형식에 매핑하고 서로 교환해서 사용할 수 있습니다.

> [!NOTE]
> Azure Resource Manager API(https://management.core.windows.net/)와 같은 특정 웹 API에서 액세스 토큰의 대상 그룹 클레임(aud)에는 후행 ‘/’가 필요합니다. 이 경우 API에서 토큰이 유효하려면 범위를 https://management.core.windows.net//user_impersonation(이중 슬래시에 주의)으로 전달해야 합니다.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>증분 동의에 대한 동적 범위 요청
v1.0을 사용하여 애플리케이션을 빌드하는 경우 로그인할 때 사용자가 동의하도록 애플리케이션에서 요구하는 사용 권한의 전체 세트(정적 범위)를 등록해야 했습니다. v2.0에서는 범위 매개 변수를 사용하여 필요에 따라 추가 사용 권한을 요청할 수 있습니다. 이러한 범위를 동적 범위라고 하며, 사용자가 범위에 대한 증분 동의를 제공하도록 허용합니다.

예를 들어 처음에 사용자가 로그인하도록 하고 모든 종류의 액세스를 거부할 수 있습니다. 나중에 토큰 획득 메서드에서 일정 범위를 요청하여 사용자의 일정을 읽을 수 있는 권한을 부여하고 사용자의 동의를 받을 수 있습니다.

예: `https://graph.microsoft.com/User.Read` 및 `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>자동으로 토큰 획득(캐시에서)
MSAL은 하나의 토큰 캐시(또는 기밀 클라이언트 애플리케이션의 경우 두 개의 캐시)를 유지 관리하고, 토큰을 획득한 후에 캐시합니다.  대부분의 경우 토큰을 자동으로 가져오려고 하면 캐시의 토큰에 따라 더 많은 범위가 있는 다른 토큰을 획득하게 됩니다. 또한 토큰 캐시에 새로 고침 토큰도 포함되어 있으므로 만료 기간이 가까워질 때 토큰을 새로 고칠 수 있습니다.

### <a name="recommended-call-pattern-for-public-client-applications"></a>공용 클라이언트 애플리케이션에 권장되는 호출 패턴
애플리케이션 코드는 먼저 캐시에서 토큰을 자동으로 가져오도록 시도해야 합니다.  메서드 호출에서 “UI 필요” 오류 또는 예외를 반환하는 경우 다른 방법으로 토큰을 획득하도록 시도합니다. 

그러나 토큰을 자동으로 획득하려고 시도하면 **안 되는** 두 가지 흐름이 있습니다.

- [클라이언트 자격 증명 흐름](msal-authentication-flows.md#client-credentials) - 사용자 토큰 캐시를 사용하지 않고 애플리케이션 토큰 캐시를 사용합니다. 이 메서드는 STS에 요청을 보내기 전에 이 애플리케이션 토큰 캐시를 확인합니다.
- 웹앱의 [권한 부여 코드 흐름](msal-authentication-flows.md#authorization-code) - 사용자가 로그인하고 더 많은 범위에 동의하도록 하여 애플리케이션에서 얻은 코드를 사용합니다. 코드는 계정이 아닌 매개 변수로 전달되므로 메서드는 코드를 사용하기 전에 캐시를 볼 수 없습니다. 어쨌든 서비스에 대한 호출이 필요합니다.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>권한 부여 코드 흐름을 사용하는 웹앱에 권장되는 호출 패턴 
[OpenID Connect 권한 부여 코드 흐름](v2-protocols-oidc.md)을 사용하는 웹 애플리케이션의 경우 컨트롤러에 권장되는 패턴은 다음과 같습니다.

- 사용자 지정 직렬화가 있는 토큰 캐시를 사용하여 기밀 클라이언트 애플리케이션을 인스턴스화합니다. 
- 권한 부여 코드 흐름을 사용하여 토큰을 획득합니다.

## <a name="acquiring-tokens"></a>토큰 획득
일반적으로 토큰을 획득하는 메서드는 공용 클라이언트 애플리케이션인지 아니면 기밀 클라이언트 애플리케이션인지에 따라 달라집니다.

### <a name="public-client-applications"></a>공용 클라이언트 애플리케이션
공용 클라이언트 애플리케이션(데스크톱 또는 모바일 앱)의 경우 다음과 같습니다.
- 사용자가 UI 또는 팝업 창을 통해 로그인하도록 하여 토큰을 대화형으로 획득하는 경우가 많습니다.
- 데스크톱 애플리케이션이 도메인 또는 Azure에 조인된 Windows 컴퓨터에서 실행되는 경우 Windows 통합 인증(IWA/Kerberos)을 사용하여 [로그인한 사용자에 대한 토큰을 자동으로 가져올](msal-authentication-flows.md#integrated-windows-authentication) 수 있습니다.
- .NET 프레임워크 데스크톱 클라이언트 애플리케이션에서 [사용자 이름과 암호가 포함된 토큰을 가져올](msal-authentication-flows.md#usernamepassword) 수 있지만 권장되지는 않습니다. 기밀 클라이언트 애플리케이션에서는 사용자 이름/암호를 사용하지 않습니다.
- 웹 브라우저가 없는 디바이스에서 실행되는 애플리케이션에서 [디바이스 코드 흐름](msal-authentication-flows.md#device-code)을 통해 토큰을 획득할 수 있습니다. URL과 코드가 사용자에게 제공되면, 사용자가 다른 디바이스의 웹 브라우저로 이동하여 코드를 입력하고 로그인합니다.  그런 다음, Azure AD에서 브라우저가 없는 디바이스에 토큰을 다시 보냅니다.

### <a name="confidential-client-applications"></a>기밀 클라이언트 애플리케이션 
기밀 클라이언트 애플리케이션(웹앱, 웹 API 또는 Windows 서비스와 같은 디먼 애플리케이션)의 경우 다음과 같습니다.
- [클라이언트 자격 증명 흐름](msal-authentication-flows.md#client-credentials)을 사용하여 사용자가 아니라 **애플리케이션 자체**에 대한 토큰을 획득합니다. 이는 동기화 도구 또는 특정 사용자가 아니라 일반적으로 사용자를 처리하는 도구에 사용할 수 있습니다. 
- 웹 API에 대한 [On-Behalf-Of 흐름](msal-authentication-flows.md#on-behalf-of)을 사용하여 사용자를 대신해 API를 호출합니다. 사용자 어설션(예: SAML 또는 JWT 토큰)에 따라 토큰을 획득하기 위해 애플리케이션이 클라이언트 자격 증명을 사용하여 식별됩니다. 이 흐름은 서비스 간 호출에서 특정 사용자의 리소스에 액세스해야 하는 애플리케이션에서 사용됩니다.
- 사용자가 권한 부여 요청 URL을 통해 로그인하면 웹앱에서 [권한 부여 코드 흐름](msal-authentication-flows.md#authorization-code)을 사용하여 토큰을 획득합니다. OpenID Connect 애플리케이션은 일반적으로 이 메커니즘을 사용합니다. 이 경우 사용자가 OpenID Connect를 사용하여 로그인한 다음, 사용자를 대신하여 웹 API에 액세스할 수 있습니다.


## <a name="authentication-results"></a>인증 결과 
클라이언트에서 액세스 토큰을 요청하면 Azure AD는 액세스 토큰에 대한 일부 메타데이터가 포함된 인증 결과도 반환합니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효 범위가 포함되어 있습니다. 이 데이터를 사용하면 앱에서 액세스 토큰 자체를 구문 분석하지 않고도 액세스 토큰의 인텔리전트 캐싱을 수행할 수 있습니다.  인증 결과에는 다음이 포함됩니다.

- 웹 API에서 리소스에 액세스하기 위한 [액세스 토큰](access-tokens.md). 이 문자열은 일반적으로 base64로 인코딩된 JWT이지만 클라이언트가 액세스 토큰 내부를 확인해서는 안 됩니다. 형식은 안정적 상태 유지가 보장되지 않으며 리소스에 대해 암호화할 수 있습니다. 클라이언트의 액세스 토큰 콘텐츠에 따라 코드를 작성하는 사용자는 오류 및 클라이언트 논리 나누기의 가장 큰 원인 중 하나입니다.
- 사용자에 대한 [ID 토큰](id-tokens.md)(JWT).
- 토큰이 만료되는 날짜/시간을 알려주는 토큰 만료 기간.
- 사용자가 검색한 테넌트가 포함된 테넌트 ID. 게스트 사용자(Azure AD B2B 시나리오)의 경우 테넌트 ID는 고유 테넌트가 아니라 게스트 테넌트입니다. 토큰이 사용자 이름으로 전달되면 인증 결과에도 이 사용자에 대한 정보가 포함됩니다. 애플리케이션에 대한 토큰이 사용자의 개입이 없이 요청되는 기밀 클라이언트 흐름의 경우 이 사용자 정보는 null입니다.
- 토큰이 발급된 범위.
- 사용자에 대한 고유한 ID.

## <a name="next-steps"></a>다음 단계
[오류 및 예외 처리](msal-handling-exceptions.md)에 대해 알아봅니다. 

---
title: MSAL (Microsoft 인증 라이브러리)을 사용 하 여 & 캐시 토큰 얻기 | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL을 사용 하 여 토큰을 가져오고 캐싱하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: da79a74121318993f807ec3bde101b652a8b49da
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628138"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰 가져오기 및 캐시

[액세스 토큰](access-tokens.md)을 사용하면 클라이언트에서 Azure를 통해 보호되는 웹 API를 안전하게 호출할 수 있습니다. MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰을 획득 하는 방법에는 여러 가지가 있습니다. 일부 경우에는 웹 브라우저를 통한 사용자 조작이 필요 하지만 다른 사용자는 사용자 조작이 필요 하지 않습니다. 일반적으로 토큰을 획득 하는 데 사용 되는 방법은 응용 프로그램이 데스크톱 또는 모바일 앱과 같은 공용 클라이언트 응용 프로그램 인지, 아니면 웹 앱, web API 또는 디먼 응용 프로그램과 같은 기밀 클라이언트 응용 프로그램 인지에 따라 달라 집니다.

MSAL은 획득 된 토큰을 캐시 합니다. 응용 프로그램 코드는 먼저 다른 방법으로 토큰을 획득 하기 전에 캐시에서 자동으로 토큰을 가져오려고 시도 합니다.

또한 캐시에서 계정을 제거하여 획득한 토큰 캐시를 지울 수도 있습니다. 그러나 브라우저에 있는 세션 쿠키는 제거 되지 않습니다.

## <a name="scopes-when-acquiring-tokens"></a>토큰 획득 시 범위

[범위](v2-permissions-and-consent.md) 는 웹 API가 클라이언트 응용 프로그램에서에 대 한 액세스를 요청할 수 있는 권한을 노출 하는 권한입니다. 클라이언트 애플리케이션은 웹 API에 액세스하기 위해 토큰을 가져오도록 인증 요청을 할 때 이러한 범위에 대한 사용자의 동의를 요청합니다. MSAL을 사용하면 토큰에서 개발자용 Azure AD(v1.0) 및 Microsoft ID 플랫폼(v2.0) API에 액세스하기 위한 토큰을 가져올 수 있습니다. v2.0 프로토콜은 요청에서 리소스 대신 범위를 사용합니다. 자세한 내용은 [v1.0 및 v2.0 비교](../azuread-dev/azure-ad-endpoint-comparison.md)를 참조하세요. v2.0 엔드포인트는 허용하는 토큰 버전에 대한 웹 API의 구성에 따라 액세스 토큰을 MSAL에 반환합니다.

몇 가지 MSAL 토큰 획득 메서드에는 `scopes` 매개 변수가 필요 합니다. `scopes`매개 변수는 필요한 사용 권한 및 요청 된 리소스를 선언 하는 문자열 목록입니다. 잘 알려진 범위는 [Microsoft Graph 권한](/graph/permissions-reference)입니다.

또한 MSAL에서는 v1.0 리소스에도 액세스할 수 있습니다. 자세한 내용은 [v 1.0 응용 프로그램의 범위](msal-v1-app-scopes.md)를 참조 하세요.

### <a name="request-scopes-for-a-web-api"></a>웹 API에 대 한 요청 범위

응용 프로그램에서 리소스 API에 대 한 특정 권한으로 액세스 토큰을 요청 해야 하는 경우 API의 앱 ID URI가 포함 된 범위를 형식으로 전달 합니다 `<app ID URI>/<scope>` .

다른 리소스에 대 한 몇 가지 예제 범위 값은 다음과 같습니다.

- Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- 사용자 지정 web API: `api://11111111-1111-1111-1111-111111111111/api.read`

범위 값의 형식은 액세스 토큰을 받는 리소스 (API) 및 허용 하는 클레임 값에 따라 달라 집니다 `aud` .

Microsoft Graph에 대해서만 `user.read` 범위가로 매핑되고 `https://graph.microsoft.com/User.Read` 두 범위 형식을 서로 바꿔 사용할 수 있습니다.

Azure Resource Manager API와 같은 특정 웹 Api ( https://management.core.windows.net/) 액세스 토큰의 대상 그룹 클레임 ()에서 후행 슬래시 ('/')를 사용 `aud` 합니다. 이 경우 `https://management.core.windows.net//user_impersonation` 이중 슬래시 ('//')를 포함 하 여 범위를로 전달 합니다.

다른 Api는 범위 값에 *스키마 나 호스트가 포함 되지 않아야* 하 고 앱 ID (GUID)와 범위 이름만 필요 합니다. 예를 들면 다음과 같습니다.

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> 다운스트림 리소스가 사용자의 제어를 받지 않는 경우 `401` 액세스 토큰을 리소스에 전달할 때 또는 다른 오류가 발생 하는 경우 다른 범위 값 형식 (예: 구성표 및 호스트 포함/제외)을 시도해 야 할 수 있습니다.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>증분 동의에 대한 동적 범위 요청

응용 프로그램에서 제공 하는 기능이 나 요구 사항이 변경 되 면 범위 매개 변수를 사용 하 여 필요에 따라 추가 권한을 요청할 수 있습니다. 이러한 *동적 범위* 를 통해 사용자는 범위에 대해 증분 동의를 제공할 수 있습니다.

예를 들어 사용자에 게 로그인 할 수 있지만, 처음에는 리소스에 대 한 액세스를 거부 합니다. 나중에 토큰 획득 방법에서 일정 범위를 요청 하 고 사용자의 동의를 가져오면 해당 일정을 볼 수 있는 기능을 제공할 수 있습니다. 예를 들어 `https://graph.microsoft.com/User.Read` 및 범위를 요청 `https://graph.microsoft.com/Calendar.Read` 합니다.

## <a name="acquiring-tokens-silently-from-the-cache"></a>자동으로 토큰 획득(캐시에서)

MSAL은 토큰 캐시 (또는 기밀 클라이언트 응용 프로그램에 대 한 두 개의 캐시)를 유지 관리 하 고 획득 한 후에 토큰을 캐시 합니다. 대부분의 경우 토큰을 자동으로 가져오려고 하면 캐시의 토큰에 따라 더 많은 범위가 있는 다른 토큰을 획득하게 됩니다. 또한 토큰 캐시에 새로 고침 토큰도 포함되어 있으므로 만료 기간이 가까워질 때 토큰을 새로 고칠 수 있습니다.

### <a name="recommended-call-pattern-for-public-client-applications"></a>공용 클라이언트 애플리케이션에 권장되는 호출 패턴

응용 프로그램 코드는 먼저 캐시에서 자동으로 토큰을 가져오려고 시도 합니다. 메서드 호출에서 “UI 필요” 오류 또는 예외를 반환하는 경우 다른 방법으로 토큰을 획득하도록 시도합니다.

하지만 토큰을 자동으로 획득 하려고 **하지 않아야** 하는 두 개의 흐름이 있습니다.

- [클라이언트 자격 증명 흐름](msal-authentication-flows.md#client-credentials)-사용자 토큰 캐시를 사용 하지 않고 응용 프로그램 토큰 캐시를 사용 합니다. 이 메서드는 STS (보안 토큰 서비스)에 요청을 보내기 전에 응용 프로그램 토큰 캐시를 확인 합니다.
- [권한 부여 코드](msal-authentication-flows.md#authorization-code) 는 응용 프로그램이 사용자에 게 로그인 하 고 더 많은 범위에 동의 하도록 하 여 가져온 코드를 교환. 계정이 아닌 코드는 매개 변수로 전달 되기 때문에 메서드는 코드를 교환 하기 전에 캐시에서 찾을 수 없으므로 서비스에 대 한 호출을 호출 합니다.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>권한 부여 코드 흐름을 사용 하는 웹 앱의 권장 호출 패턴

[OpenID Connect 권한 부여 코드 흐름](v2-protocols-oidc.md)을 사용하는 웹 애플리케이션의 경우 컨트롤러에 권장되는 패턴은 다음과 같습니다.

- 사용자 지정 직렬화가 있는 토큰 캐시를 사용하여 기밀 클라이언트 애플리케이션을 인스턴스화합니다.
- 권한 부여 코드 흐름을 사용하여 토큰을 획득합니다.

## <a name="acquiring-tokens"></a>토큰 획득

일반적으로 토큰을 획득하는 메서드는 공용 클라이언트 애플리케이션인지 아니면 기밀 클라이언트 애플리케이션인지에 따라 달라집니다.

### <a name="public-client-applications"></a>퍼블릭 클라이언트 애플리케이션

공용 클라이언트 애플리케이션(데스크톱 또는 모바일 앱)의 경우 다음과 같습니다.

- 사용자가 UI 또는 팝업 창을 통해 로그인하도록 하여 토큰을 대화형으로 획득하는 경우가 많습니다.
- 데스크톱 애플리케이션이 도메인 또는 Azure에 조인된 Windows 컴퓨터에서 실행되는 경우 Windows 통합 인증(IWA/Kerberos)을 사용하여 [로그인한 사용자에 대한 토큰을 자동으로 가져올](msal-authentication-flows.md#integrated-windows-authentication) 수 있습니다.
- .NET framework 데스크톱 클라이언트 응용 프로그램에서 [사용자 이름 및 암호를 사용 하는 토큰을 가져올](msal-authentication-flows.md#usernamepassword) 수 있습니다 (권장 하지 않음). 기밀 클라이언트 애플리케이션에서는 사용자 이름/암호를 사용하지 않습니다.
- 웹 브라우저가 없는 장치에서 실행 되는 응용 프로그램의 [장치 코드 흐름](msal-authentication-flows.md#device-code) 을 통해 토큰을 가져올 수 있습니다. URL과 코드가 사용자에게 제공되면, 사용자가 다른 디바이스의 웹 브라우저로 이동하여 코드를 입력하고 로그인합니다. 그런 다음, Azure AD에서 브라우저가 없는 디바이스에 토큰을 다시 보냅니다.

### <a name="confidential-client-applications"></a>기밀 클라이언트 애플리케이션

기밀 클라이언트 응용 프로그램 (웹 응용 프로그램, web API 또는 Windows 서비스와 같은 데몬 응용 프로그램)의 경우 다음을 수행 합니다.

- [클라이언트 자격 증명 흐름](msal-authentication-flows.md#client-credentials)을 사용하여 사용자가 아니라 **애플리케이션 자체** 에 대한 토큰을 획득합니다. 이 기술은 특정 사용자가 아닌 일반 사용자를 처리 하는 도구 또는 동기화에 사용할 수 있습니다.
- 웹 API에 대해 사용자를 대신 하 여 API를 호출 하는 [대신 흐름](msal-authentication-flows.md#on-behalf-of) 을 사용 합니다. 응용 프로그램은 사용자 어설션 (예: 또는 JWT 토큰)을 기반으로 토큰을 획득 하기 위해 클라이언트 자격 증명으로 식별 됩니다. 이 흐름은 서비스 간 호출에서 특정 사용자의 리소스에 액세스해야 하는 애플리케이션에서 사용됩니다.
- 사용자가 권한 부여 요청 URL을 통해 로그인하면 웹앱에서 [권한 부여 코드 흐름](msal-authentication-flows.md#authorization-code)을 사용하여 토큰을 획득합니다. OpenID Connect 애플리케이션은 일반적으로 이 메커니즘을 사용합니다. 이 경우 사용자가 OpenID Connect를 사용하여 로그인한 다음, 사용자를 대신하여 웹 API에 액세스할 수 있습니다.

## <a name="authentication-results"></a>인증 결과

클라이언트가 액세스 토큰을 요청 하는 경우 Azure AD는 액세스 토큰에 대 한 메타 데이터를 포함 하는 인증 결과도 반환 합니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효 범위가 포함되어 있습니다. 이 데이터를 사용하면 앱에서 액세스 토큰 자체를 구문 분석하지 않고도 액세스 토큰의 인텔리전트 캐싱을 수행할 수 있습니다. 인증 결과에는 다음이 포함됩니다.

- 웹 API에서 리소스에 액세스하기 위한 [액세스 토큰](access-tokens.md). 이 문자열은 일반적으로 b a s e 64로 인코딩된 JWT 이지만 클라이언트는 액세스 토큰 내에서 볼 수 없습니다. 형식이 안정적으로 유지 되 고 리소스에 대해 암호화 될 수 있습니다. 클라이언트의 액세스 토큰 내용에 따라 코드를 작성 하는 사람들은 오류 및 클라이언트 논리 중단 가장 일반적인 원인 중 하나입니다.
- 사용자 (JWT)의 [ID 토큰](id-tokens.md) 입니다.
- 토큰이 만료되는 날짜/시간을 알려주는 토큰 만료 기간.
- 사용자가 검색한 테넌트가 포함된 테넌트 ID. 게스트 사용자(Azure AD B2B 시나리오)의 경우 테넌트 ID는 고유 테넌트가 아니라 게스트 테넌트입니다. 토큰이 사용자 이름으로 전달되면 인증 결과에도 이 사용자에 대한 정보가 포함됩니다. 애플리케이션에 대한 토큰이 사용자의 개입이 없이 요청되는 기밀 클라이언트 흐름의 경우 이 사용자 정보는 null입니다.
- 토큰이 발급된 범위.
- 사용자에 대한 고유한 ID.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>고급 백그라운드 앱 및 서비스에서 사용자의 캐시 된 토큰 액세스

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>다음 단계

Java 용 MSAL을 사용 하는 경우 [java 용 msal의 사용자 지정 토큰 캐시 serialization](msal-java-token-cache-serialization.md)에 대해 알아봅니다.

[오류 및 예외 처리](msal-handling-exceptions.md)에 대해 알아봅니다.
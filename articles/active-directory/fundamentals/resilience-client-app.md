---
title: 개발하는 클라이언트 애플리케이션에서 인증 및 권한 부여의 복원력 향상
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼을 사용하여 클라이언트 애플리케이션에서 인증 및 권한 부여의 복원력을 높이기 위한 지침
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226593"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>개발하는 클라이언트 애플리케이션에서 인증 및 권한 부여의 복원력 향상

이 섹션에서는 Microsoft ID 플랫폼 및 Azure Active Directory을 사용하는 클라이언트 애플리케이션에 복원력을 구축하고 사용자를 대신하여 사용자를 로그인하고 작업을 수행하는 방법에 대한 지침을 제공합니다.

## <a name="use-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리) 사용

[Microsoft 인증 라이브러리(MSAL)](../develop/msal-overview.md)는 [Microsoft ID 플랫폼](../develop/index.yml)의 핵심 부분입니다. 토큰 획득, 관리, 캐싱 및 새로 고침을 간소화하고 관리하며 복원력을 위한 모범 사례를 사용합니다. MSAL은 개발자가 구현 세부 정보를 걱정하지 않아도 안전한 솔루션을 사용할 수 있도록 설계되었습니다.

MSAL은 토큰을 캐시하고 자동 토큰 획득 패턴을 사용합니다. 또한 Windows UWP, iOS 및 Android와 같은 보안 스토리지를 기본적으로 제공하는 플랫폼에서 토큰 캐시를 자동으로 직렬화합니다. 개발자는 [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.NET](../develop/msal-net-token-cache-serialization.md), [Java용 MSAL](../develop/msal-java-token-cache-serialization.md) 및 [Python용 MSAL](../develop/msal-python-token-cache-serialization.md)을 사용할 때 직렬화 동작을 사용자 지정할 수 있습니다.

![MSAL을 사용하여 Microsoft ID를 호출하는 애플리케이션이 있는 디바이스 이미지](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

MSAL을 사용하는 경우 토큰 캐싱, 새로 고침 및 자동 획득이 자동으로 지원됩니다. 간단한 패턴을 사용하여 최신 인증에 필요한 토큰을 가져올 수 있습니다. 다양한 언어가 지원되며 [샘플](../develop/sample-v2-code.md) 페이지에서 사용자의 언어와 시나리오에 맞는 샘플을 찾을 수 있습니다.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL은 경우에 따라 사전에 토큰을 새로 고칠 수 있습니다. Microsoft ID가 수명이 긴 토큰을 발급하면 토큰을 새로 고칠 수 있는 최적의 시간("새로 고침\_")에 대한 정보를 클라이언트에 보낼 수 있습니다. MSAL은 이 정보에 따라 토큰을 사전에 새로 고칩니다. 이전 토큰이 유효하면 애플리케이션이 계속 실행되지만 토큰을 새로 획득하는 데는 시간이 더 오래 걸립니다.

### <a name="stay-up-to-date"></a>최신 소식을 받아보세요.

개발자는 최신 MSAL 릴리스로 업데이트하는 프로세스가 필요합니다. 인증은 앱 보안의 일부이며 앱은 새로운 MSAL 릴리스에 포함된 보안 향상 기능을 사용하여 최신 상태로 유지해야 합니다. 이는 일반적으로 지속적인 개발 중인 라이브러리에 적합한 사례이며, 이렇게 하면 앱 복원력과 관련하여 최신 코드를 갖출 수 있습니다. Microsoft ID가 애플리케이션의 복원력을 향상하는 방법을 계속 혁신함에 따라 최신 MSAL을 사용하는 앱이 이러한 혁신을 기반으로 구축하는 데 가장 적합합니다.

[최신 MSAL.js 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[최신 MSAL .NET 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[최신 MSAL Python 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[최신 MSAL Java 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[최신 MSAL iOS 및 macOS 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[최신 MSAL Android 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[최신 MSAL Angular 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[최신 Microsoft.Identity.Web 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>토큰 처리에 복원력 있는 패턴 사용

MSAL을 사용하지 않는 경우 토큰 처리에 이러한 복원력 있는 패턴을 사용할 수 있습니다. 이러한 모범 사례는 MSAL 라이브러리에 의해 자동으로 구현됩니다. 

일반적으로 최신 인증을 사용하는 애플리케이션은 엔드포인트를 호출하여 사용자를 인증하거나 보호된 API를 호출하도록 애플리케이션에 권한을 부여하는 토큰을 검색합니다. MSAL은 인증의 세부 정보를 처리하고 이 프로세스의 복원력을 향상시키기 위해 여러 패턴을 구현합니다. MSAL 이외의 라이브러리를 사용하도록 선택하는 경우 이 섹션의 지침을 사용하여 모범 사례를 구현합니다. MSAL을 사용하는 경우 MSAL에서 자동으로 구현하기 때문에 이러한 모든 모범 사례를 무료로 사용할 수 있습니다.

### <a name="cache-tokens"></a>토큰 캐시

앱은 Microsoft ID에서 받은 토큰을 제대로 캐시해야 합니다. 앱에서 토큰을 수신할 때 토큰을 포함하는 HTTP 응답에는 토큰을 캐시하고 다시 사용하는 데 걸리는 시간을 애플리케이션에 알리는 "expires_in" 속성도 포함됩니다. 애플리케이션은 "expires_in" 속성을 사용하여 토큰의 수명을 확인해야 합니다. 애플리케이션은 API 액세스 토큰을 디코딩하려고 해서는 안 됩니다.

![Microsoft ID를 호출하지만 애플리케이션을 실행하는 디바이스에서 토큰 캐시를 통해 호출되는 애플리케이션](media/resilience-client-app/token-cache.png)

캐시된 토큰을 사용하면 앱과 Microsoft ID 사이에 불필요한 트래픽이 방지되고 토큰 획득 호출 수를 줄여 토큰 획득 오류에 대한 앱의 공격에 덜 취약해집니다. 캐시된 토큰을 통해 애플리케이션의 성능도 향상됩니다. 사용자는 해당 토큰 수명의 길이만큼 애플리케이션에 로그인 상태를 유지할 수 있습니다.

### <a name="serialize-and-persist-tokens"></a>토큰 직렬화 및 유지

앱은 토큰 캐시를 안전하게 직렬화하여 앱 인스턴스 간에 토큰을 유지해야 합니다. 토큰은 유효한 수명 내에 있는 동안 다시 사용할 수 있습니다. [새로 고침 토큰](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token) 및 [액세스 토큰](../develop/access-tokens.md)은 여러 시간 동안 실행됩니다. 이 유효한 시간은 애플리케이션을 시작하는 사용자에게 여러 번에 걸쳐 있을 수 있습니다. 앱이 시작되면 사용할 수 있는 유효한 액세스 또는 새로 고침 토큰이 있는지 확인해야 합니다. 이렇게 하면 Microsoft ID에 대한 불필요한 호출을 피할 수 있으므로 앱의 복원력과 성능이 향상됩니다.

![Microsoft ID를 호출하지만 애플리케이션을 실행하는 디바이스에서 토큰 캐시 및 토큰 저장소를 통해 호출되는 애플리케이션](media/resilience-client-app/token-store.png)

영구 토큰 스토리지에 대한 액세스는 소유 사용자 또는 프로세스 ID로 제어되고 암호화되어야 합니다. 모바일, Windows, Mac 등의 플랫폼에서 개발자는 자격 증명을 저장하기 위한 기본 제공 기능을 활용해야 합니다.

### <a name="acquire-tokens-silently"></a>토큰 자동 획득

사용자를 인증하거나 API를 호출하기 위한 권한 부여를 검색하는 프로세스에는 Microsoft ID의 여러 단계가 필요할 수 있습니다. 예를 들어 사용자가 처음으로 로그인하는 경우 자격 증명을 입력하고 문자 메시지를 통해 다단계 인증을 수행해야 할 수 있습니다. 각 단계에서는 해당 서비스를 제공하는 리소스에 대한 종속성을 추가합니다. 종속성이 가장 낮고 사용자에게 가장 적합한 환경은 사용자 상호 작용을 요청하기 전에 이러한 추가 단계를 방지하기 위해 토큰을 자동으로 획득하는 것입니다.

![사용자를 인증하거나 권한을 부여하는 프로세스를 완료하기 위해 실행해야 할 수 있는 Microsoft ID 내의 다양한 서비스를 보여 주는 다이어그램](media/resilience-client-app/external-dependencies.png)

토큰을 자동으로 획득하는 것은 앱의 토큰 캐시에서 유효한 토큰을 사용하는 것으로 시작합니다. 사용할 수 있는 유효한 토큰이 없는 경우 앱은 새로 고침 토큰(사용 가능한 경우) 및 토큰 엔드포인트를 사용하여 토큰을 획득하려고 시도해야 합니다. 이러한 옵션을 모두 사용할 수 없는 경우 앱은 "prompt=none" 매개 변수를 사용하여 토큰을 획득해야 합니다. 이렇게 하면 권한 부여 엔드포인트가 사용되지만 사용자에게 UI가 표시되지 않습니다. Microsoft ID가 사용자와 상호 작용하지 않고 앱에 토큰을 제공할 수 있는 경우 해당 토큰을 제공합니다. 이러한 메서드로 인해 토큰이 생성되지 않는 경우 사용자는 대화형으로 다시 인증해야 합니다.

> [!NOTE]
> 일반적으로 앱은 상호 작용이 필요하지 않은 경우에도 사용자 상호 작용을 강제로 수행하므로 "로그인" 및 "동의"와 같은 프롬프트를 사용하지 않아야 합니다.

## <a name="handle-service-responses-properly"></a>서비스 응답을 올바르게 처리

애플리케이션에서 모든 오류 응답을 처리하는 동안 복원력에 영향을 줄 수 있는 몇 가지 응답이 있습니다. 애플리케이션이 HTTP 429 응답 코드(너무 많은 요청)를 수신하는 경우 Microsoft ID에서 요청을 제한합니다. 앱은 계속해서 너무 많은 요청을 수신하는 경우 앱이 토큰을 수신하지 못하도록 계속 제한됩니다. 애플리케이션은 다음 시간 후 다시 시도 응답 필드의 시간(초)이 경과할 때까지 토큰을 다시 획득하려고 시도하면 안 됩니다. 429 응답을 수신하는 것은 애플리케이션에서 토큰을 올바르게 캐싱하지 않고 다시 사용하고 있기 때문인 경우가 많습니다. 개발자는 애플리케이션에서 토큰을 캐싱하고 다시 사용하는 방법을 검토해야 합니다.

애플리케이션에서 HTTP 5xx 응답 코드를 수신하는 경우 앱은 빠른 다시 시도 루프를 시작하면 안 됩니다. 해당하는 경우 애플리케이션은 429 응답에 대해 수행하는 것과 동일한 다음 시간 후 다시 시도 처리를 적용해야 합니다. 응답에서 다음 시간 후 다시 시도 헤더를 제공하지 않는 경우에는 응답 후 5초 이상이 지나고 처음 다시 시도하여 지수 백오프 재시도를 구현하는 것이 좋습니다.

요청 시간을 초과한 애플리케이션은 즉시 다시 시도하면 안 됩니다. 응답 후 5초 이상이 지났을 때 다시 시도하여 지수 백오프 재시도를 구현합니다.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>권한 부여 관련 정보를 검색하는 옵션 평가

많은 애플리케이션 및 API에는 권한 부여 결정을 내리는 사용자에 대한 특정 정보가 필요합니다. 애플리케이션에서 이 정보를 가져오는 몇 가지 방법이 있습니다. 각 메서드에는 장점과 단점이 있습니다. 개발자는 이러한 전략을 평가하여 앱의 복원력에 가장 적합한 전략을 결정해야 합니다.

### <a name="tokens"></a>토큰

ID 토큰 및 액세스 토큰은 주체에 대한 정보를 제공하는 표준 클레임을 포함합니다. 이러한 정보는 [Microsoft ID 플랫폼 ID 토큰](../develop/id-tokens.md) 및 [Microsoft ID 플랫폼 액세스 토큰](../develop/access-tokens.md)에 설명되어 있습니다. 앱에 필요한 정보가 토큰에 이미 있는 경우 해당 데이터를 검색하는 가장 효율적인 방법은 추가 네트워크 호출의 오버헤드를 저장하여 정보를 별도로 검색하는 것처럼 토큰 클레임을 사용하는 것입니다. 네트워크 호출 수가 적을수록 애플리케이션에 대한 전반적인 복원력이 높아집니다.

> [!NOTE]
> 일부 애플리케이션은 사용자 정보 엔드포인트를 호출하여 인증된 사용자에 대한 클레임을 검색합니다. 앱에서 받을 수 있는 ID 토큰에서 사용할 수 있는 정보는 UserInfo 엔드포인트에서 얻을 수 있는 정보의 상위 집합입니다. 앱은 사용자 정보 엔드포인트를 호출하는 대신 ID 토큰을 사용하여 사용자에 대한 정보를 가져와야 합니다.

앱 개발자는 [선택적 클레임](../develop/active-directory-optional-claims.md)으로 표준 토큰 클레임을 보강할 수 있습니다. 일반적인 선택적 클레임 중 하나는 [그룹](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims)입니다. 그룹 클레임을 추가하는 방법에는 여러 가지가 있습니다. "애플리케이션 그룹" 옵션에는 애플리케이션에 할당된 그룹만 포함됩니다. "모두" 또는 "보안 그룹" 옵션에는 토큰에 많은 그룹을 추가할 수 있는 동일한 테넌트 내 모든 앱의 그룹이 포함됩니다. 그룹의 전체 목록을 얻기 위해 토큰 블로트를 유발하고 추가 호출을 요구함으로써 토큰에서 그룹을 요청하여 얻은 효율성을 부정할 수 있기 때문에 이 경우 효과를 평가하는 것이 중요합니다.

토큰에서 그룹을 사용하는 대신 앱 역할을 대신 사용하고 포함할 수 있습니다. 개발자는 고객이 포털 또는 API를 사용하여 고객이 디렉터리에서 관리할 수 있는 앱 및 API에 대한 [앱 역할](../develop/howto-add-app-roles-in-azure-ad-apps.md)을 정의할 수 있습니다. IT Pros는 다른 사용자 및 그룹에 역할을 할당하여 누가 어떤 컨텐츠와 기능에 액세스 권한을 갖는지를 제어할 수 있습니다. 토큰이 애플리케이션 또는 API에 대해 발급되면 사용자에게 할당된 역할을 토큰의 역할 클레임에서 사용할 수 있습니다. 토큰에서 직접 이 정보를 가져오면 추가 API 호출을 저장할 수 있습니다.

마지막으로 IT 관리자는 테넌트의 특정 정보에 따라 클레임을 추가할 수도 있습니다. 예를 들어 엔터프라이즈에는 엔터프라이즈별 사용자 ID를 갖는 확장이 있을 수 있습니다.

모든 경우에, 디렉터리의 정보를 토큰에 직접 추가하는 것이 효율적이고 앱의 종속성 수를 줄임으로써 앱 복원력을 높일 수 있습니다. 반면에 토큰을 획득할 수 없는 복원력 문제는 해결되지 않습니다. 애플리케이션의 주요 시나리오에 대해서만 선택적 클레임을 추가해야 합니다. 앱에 관리자 기능에 대한 정보만 필요한 경우 애플리케이션이 필요에 따라 해당 정보를 얻는 것이 가장 좋습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph는 Microsoft 365 데이터에 액세스하는 통합 API 엔드포인트를 제공하며 조직의 생산성, ID 및 보안 패턴을 설명합니다. Microsoft Graph를 사용하는 애플리케이션은 권한 부여 결정을 위해 Microsoft 365에서 모든 정보를 사용할 수 있습니다.

모든 Microsoft 365 액세스하려면 앱에 단일 토큰만 필요합니다. 이는 여러 토큰이 필요한 Microsoft Exchange 또는 Microsoft SharePoint와 같은 Microsoft 365 구성 요소에만 적용되는 이전 API를 사용하는 것보다 복원력이 높습니다.

Microsoft Graph API를 사용하는 경우 [Microsoft Graph SDK](/graph/sdks/sdks-overview)를 사용하는 것이 좋습니다. Microsoft Graph SDK는 Microsoft Graph에 액세스하는 고품질의 효율적이고 복원력 있는 애플리케이션 구축을 간소화하도록 설계되었습니다.

권한 부여 결정을 위해 개발자는 토큰에서 사용할 수 있는 클레임을 일부 Microsoft Graph 호출 대신 사용할 시기를 고려해야 합니다. 위에서 언급한 바와 같이 개발자는 토큰에서 그룹, 앱 역할 및 선택적 클레임을 요청할 수 있습니다. 복원력 측면에서 권한 부여를 위해 Microsoft Graph를 사용하려면 Microsoft Graph 자체뿐만 아니라 Microsoft Graph에 액세스하는 토큰을 가져오는 데 Microsoft ID를 사용하는 추가 네트워크 호출이 필요합니다. 그러나 애플리케이션이 이미 Microsoft Graph를 데이터 계층으로 사용하는 경우에는 권한 부여를 위해 Graph를 사용해도 추가 위험이 없습니다.

## <a name="use-broker-authentication-on-mobile-devices"></a>모바일 디바이스에서 broker 인증 사용

모바일 디바이스에서 Microsoft Authenticator와 같은 인증 broker를 사용하면 복원력이 향상됩니다. broker는 시스템 브라우저나 포함된 웹 보기 등의 다른 옵션으로 사용할 수 있는 기능보다 우수합니다. 인증 broker는 사용자 및 디바이스에 대한 클레임을 포함하며 디바이스에서 다른 애플리케이션에 액세스하기 위한 인증 토큰을 가져오는 데 사용할 수 있는 [주 새로 고침 토큰](../devices/concept-primary-refresh-token.md)을 활용할 수 있습니다. PRT를 사용하여 애플리케이션에 대한 액세스를 요청하는 경우 해당 디바이스 및 MFA 클레임이 Azure AD에서 신뢰됩니다. 이렇게 하면 디바이스를 다시 인증하는 추가 단계를 방지하여 복원력이 높아집니다. 사용자는 동일한 디바이스에서 여러 MFA 프롬프트 문제를 방지하여 외부 서비스에 대한 종속성을 줄이고 사용자 환경을 개선하여 복원력을 높일 수 있습니다.

![Microsoft ID를 호출하지만 애플리케이션을 실행하는 디바이스에서 토큰 캐시,토큰 저장소 및 인증 broker를 통해 호출되는 애플리케이션](media/resilience-client-app/authentication-broker.png)

Broker 인증은 MSAL에서 자동으로 지원됩니다. 조정된 인증을 사용하는 방법에 대한 자세한 내용은 다음 페이지에서 확인할 수 있습니다.

- [macOS 및 iOS에서 SSO 구성](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [방법: MSAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>지속적인 액세스 권한 평가 채택

[CAE(지속적인 액세스 권한 평가)](../conditional-access/concept-continuous-access-evaluation.md)는 수명이 긴 토큰으로 애플리케이션 보안과 복원력을 높일 수 있는 최근 개발입니다. CAE는 OpenID Foundation의 Shared Signals and Events 작업 그룹에서 개발되고 있는 새로운 업계 표준입니다. CAE를 사용하면 짧은 토큰 수명을 사용하는 대신 [중요한 이벤트](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) 및 [정책 평가](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)에 따라 액세스 토큰을 해지할 수 있습니다. 일부 리소스 API의 경우 위험 및 정책이 실시간으로 평가되기 때문에 CAE는 토큰 수명을 최대 28시간까지 크게 늘릴 수 있습니다. 리소스 API 및 애플리케이션이 CAE를 채택함에 따라 Microsoft ID는 취소할 수 있고 오랜 기간 동안 유효한 액세스 토큰을 발급할 수 있습니다. 이러한 수명이 긴 토큰은 MSAL을 통해 사전에 새로 고쳐집니다.

CAE는 초기 단계에 있지만 애플리케이션에서 사용하는 리소스(API)가 [CAE를 채택할 때 CAE를 활용하는 클라이언트 애플리케이션](../develop/app-resilience-continuous-access-evaluation.md)을 개발할 수 있습니다. 더 많은 리소스가 CAE을 채택함에 따라 애플리케이션은 해당 리소스에 대한 CAE 사용 토큰도 획득할 수 있습니다. Microsoft Graph API 및 [Microsoft Graph SDK](/graph/sdks/sdks-overview)의 CAE 미리 보기 기능이 2021년 초에 제공될 예정입니다. CAE가 포함된 Microsoft Graph의 공개 미리 보기에 참여하려면 [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview)에서 관심이 있음을 알려주세요.

리소스 API를 개발하는 경우 [Shared Signals and Events WG](https://openid.net/wg/sse/)에 참여하는 것이 좋습니다. Microsoft는 이 그룹과 협력하여 Microsoft ID와 리소스 공급자 간에 보안 이벤트를 공유할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법](../develop/app-resilience-continuous-access-evaluation.md)
- [디먼 애플리케이션에 복원력 구축](resilience-daemon-app.md)
- [ID 및 액세스 관리 인프라의 복원력 빌드](resilience-in-infrastructure.md)
- [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)

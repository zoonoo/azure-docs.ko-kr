---
title: 개발 하는 클라이언트 응용 프로그램에서 인증 및 권한 부여의 복원 력 향상
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼을 사용 하 여 클라이언트 응용 프로그램에서 인증 및 권한 부여의 복원 력을 높이기 위한 지침
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 69967035f98d7ec2fcedff173dcf481455014ac2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920040"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>개발 하는 클라이언트 응용 프로그램에서 인증 및 권한 부여의 복원 력 향상

이 섹션에서는 Microsoft id 플랫폼 Azure Active Directory을 사용 하는 클라이언트 응용 프로그램에 복원 력을 구축 하 고 사용자를 대신 하 여 사용자를 로그인 하 고 작업을 수행 하는 방법에 대 한 지침을 제공 합니다.

## <a name="use-the-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리) 사용

[MSAL (Microsoft 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) 은 [microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop)의 핵심 부분입니다. 토큰 획득, 관리, 캐싱 및 새로 고침을 간소화 하 고 관리 하며 복원 력을 위한 모범 사례를 사용 합니다. MSAL은 개발자가 구현 세부 정보를 걱정 하지 않아도 안전한 솔루션을 사용할 수 있도록 설계 되었습니다.

MSAL은 토큰을 캐시 하 고 자동 토큰 획득 패턴을 사용 합니다. 또한 Windows UWP, iOS 및 Android와 같은 보안 저장소를 기본적으로 제공 하는 플랫폼에서 토큰 캐시를 자동으로 직렬화 합니다. 개발자는 [MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-token-cache-serialization), [Java 용 Msal](https://docs.microsoft.com/azure/active-directory/develop/msal-java-token-cache-serialization)및 [Python 용 msal](https://docs.microsoft.com/azure/active-directory/develop/msal-python-token-cache-serialization) [을 사용 하](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)는 경우 serialization 동작을 사용자 지정할 수 있습니다.

![MSAL을 사용 하 여 Microsoft Id를 호출 하는 및 응용 프로그램을 사용 하는 장치 이미지](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

MSAL을 사용 하는 경우 다음 패턴을 사용 하 여 토큰 캐싱, 새로 고침 및 자동 토큰 가져오기를 가져옵니다.

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

MSAL은 경우에 따라 사전에 토큰을 새로 고칠 수 있습니다. Microsoft Id가 수명이 긴 토큰을 발급 하는 경우 토큰을 새로 고치는 최적의 시간 ("새로 고침")으로 정보를 클라이언트에 보낼 수 있습니다 \_ . MSAL은이 정보에 따라 토큰을 사전에 새로 고칩니다. 이전 토큰이 올바르지만 응용 프로그램은 계속 실행 되지만 토큰을 획득 하는 데는 시간이 더 오래 걸립니다.

### <a name="stay-up-to-date"></a>최신 소식을 받아보세요.

개발자는 최신 MSAL 릴리스로 업데이트 하는 프로세스를 해야 합니다. 인증은 앱 보안의 일부 이며 앱은 새로운 MSAL 릴리스에 포함 된 보안 향상 기능을 사용 하 여 최신 상태로 유지 해야 합니다. 이는 일반적으로 연속 개발 중인 라이브러리에서 좋은 방법 이며, 이렇게 하면 앱 복원 력과 관련 하 여 최신 코드를 사용할 수 있습니다. Microsoft Id가 응용 프로그램의 복원 력이 향상 되는 방식으로 혁신을 계속 하는 경우 최신 MSAL을 사용 하는 앱이 이러한 혁신을 기반으로 구축 하는 데 가장 적합 합니다.

[최신 MSAL.js 버전 및 릴리스 정보 확인](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[최신 MSAL .NET 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[최신 MSAL Python 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[최신 MSAL Java 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[최신 MSAL iOS 및 macOS 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[최신 MSAL Android 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[최신 MSAL 각도 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[최신 Microsoft. Identity. 웹 버전 및 릴리스 정보를 확인 합니다.](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="if-not-using-msal-use-these-resilient-patterns-for-token-handling"></a>MSAL을 사용 하지 않는 경우 토큰 처리에 이러한 복원 력 패턴을 사용 합니다.

일반적으로 최신 인증을 사용 하는 응용 프로그램은 끝점을 호출 하 여 사용자를 인증 하는 토큰을 검색 하거나 응용 프로그램에 보호 된 Api를 호출 하는 권한을 부여 합니다. MSAL은 인증의 세부 정보를 처리 하 고이 프로세스의 복원 력을 향상 시키기 위해 여러 패턴을 구현 하는 것을 의미 합니다. MSAL 이외의 라이브러리를 사용 하도록 선택 하는 경우이 섹션의 지침을 사용 하 여 모범 사례를 구현 합니다. MSAL을 사용 하는 경우 MSAL이 자동으로 구현 하므로 이러한 모범 사례를 모두 무료로 활용할 수 있습니다.

### <a name="cache-tokens"></a>캐시 토큰

앱은 Microsoft Id에서 받은 토큰을 적절 하 게 캐시 해야 합니다. 앱에서 토큰을 수신 하는 경우 토큰을 포함 하는 HTTP 응답에는 토큰을 캐시 하 고 다시 사용 하는 데 걸리는 시간을 응용 프로그램에 알려 주는 "expires_in" 속성도 포함 됩니다. 응용 프로그램에서는 "expires_in" 속성을 사용 하 여 토큰의 수명을 확인 하는 것이 중요 합니다. 응용 프로그램은 API 액세스 토큰을 디코딩하 려 면 안 됩니다.

![Microsoft id를 호출 하지만 응용 프로그램을 실행 하는 장치에서 토큰 캐시를 통해 호출 되는 응용 프로그램](media/resilience-client-app/token-cache.png)

캐시 된 토큰을 사용 하면 앱과 Microsoft Id 사이에 불필요 한 트래픽이 방지 되 고 토큰 획득 호출 수를 줄여 토큰 획득 오류에 대 한 앱의 공격에 취약 해질 수 있습니다. 캐시 된 토큰을 통해 응용 프로그램의 성능도 향상 됩니다. 사용자는 해당 토큰 수명의 길이에 맞게 응용 프로그램에 로그인 상태를 유지할 수 있습니다.

### <a name="serialize-and-persist-tokens"></a>토큰 Serialize 및 유지

앱은 토큰 캐시를 안전 하 게 직렬화 하 여 앱 인스턴스 간에 토큰을 유지 해야 합니다. 토큰은 유효한 수명 내에 있는 동안 다시 사용할 수 있습니다. [새로 고침 토큰](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)및 [액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)은 여러 시간 동안 실행 됩니다. 이 유효한 시간은 응용 프로그램을 시작 하는 사용자에 게 여러 번 걸쳐 있을 수 있습니다. 앱이 시작 되 면 사용할 수 있는 유효한 액세스 또는 새로 고침 토큰이 있는지 확인 해야 합니다. 이렇게 하면 Microsoft Id에 대 한 불필요 한 호출을 피할 수 있으므로 앱의 복원 력과 성능이 향상 됩니다.

![Microsoft id를 호출 하지만 응용 프로그램을 실행 하는 장치에서 토큰 저장소 뿐만 아니라 토큰 캐시를 통해 호출 하는 응용 프로그램](media/resilience-client-app/token-store.png)

영구 토큰 저장소는 소유 사용자 또는 프로세스 id에 대 한 액세스 제어 및 암호화 여야 합니다. 모바일, Windows, Mac 등의 플랫폼에서 개발자는 자격 증명을 저장 하기 위한 기본 제공 기능을 활용 해야 합니다.

### <a name="acquire-tokens-silently"></a>토큰 자동 획득

사용자를 인증 하거나 API를 호출 하기 위한 권한 부여를 검색 하는 프로세스에는 Microsoft Id의 여러 단계가 필요할 수 있습니다. 예를 들어 사용자가 처음으로 로그인 하는 경우 자격 증명을 입력 하 고 문자 메시지를 통해 multi-factor authentication을 수행 해야 할 수 있습니다. 각 단계는 해당 서비스를 제공 하는 리소스에 대 한 종속성을 추가 합니다. 사용자에 게 가장 적합 한 환경 및 종속성이 가장 높은 경우 사용자 조작을 요청 하기 전에 이러한 추가 단계를 방지 하기 위해 토큰을 자동으로 얻으려고 시도 하는 것입니다.

![사용자 인증 또는 권한 부여 프로세스를 완료 하기 위해 실행 해야 할 수 있는 Microsoft Id 내의 다양 한 서비스를 보여 주는 다이어그램](media/resilience-client-app/external-dependencies.png)

토큰을 가져오는 것은 앱의 토큰 캐시에서 유효한 토큰을 사용 하 여 자동으로 시작 됩니다. 사용할 수 있는 유효한 토큰이 없으면 앱은 새로 고침 토큰을 사용 하 여 토큰을 획득 하 고 (사용 가능한 경우) 토큰 끝점을 시도해 야 합니다. 이러한 옵션을 모두 사용할 수 없는 경우 앱은 "prompt = none" 매개 변수를 사용 하 여 토큰을 획득 해야 합니다. 권한 부여 끝점을 사용 하지만 사용자에 게 UI를 표시 하지 않습니다. Microsoft Id가 사용자와 상호 작용 하지 않고도 앱에 토큰을 제공할 수 있는 경우에는 해당 됩니다. 이러한 메서드가 토큰을 생성 하지 않으면 사용자가 대화형으로 다시 인증 해야 합니다.

> [!NOTE]
> 일반적으로 앱은 상호 작용이 필요 하지 않은 경우에도 사용자 상호 작용을 강제로 수행 하므로 "로그인" 및 "동의"와 같은 프롬프트를 사용 하지 않아야 합니다.

## <a name="handle-service-responses-properly"></a>서비스 응답을 올바르게 처리 합니다.

응용 프로그램에서 모든 오류 응답을 처리 해야 하는 반면 복원 력에 영향을 줄 수 있는 몇 가지 응답이 있습니다. 응용 프로그램이 HTTP 429 응답 코드, 너무 많은 요청을 수신 하는 경우 Microsoft Id에서 요청을 제한 합니다. 앱이 계속 해 서 너무 많은 요청을 하는 경우 앱이 토큰을 수신 하지 못하도록 계속 제한 됩니다. 응용 프로그램은 Retry-After 응답 필드의 시간 (초)이 경과 될 때까지 토큰을 다시 얻으려고 시도 하지 않아야 합니다. 429 응답을 수신 하는 것은 응용 프로그램에서 토큰을 올바르게 캐시 하지 않고 다시 사용 하 고 있음을 나타내는 경우가 많습니다. 개발자는 응용 프로그램에서 토큰을 캐시 하 고 다시 사용 하는 방법을 검토 해야 합니다.

응용 프로그램에서 HTTP 5xx 응답 코드를 수신 하는 경우 앱은 빠른 다시 시도 루프를 시작 하면 안 됩니다. 응용 프로그램이 있는 경우 429 응답에 대해 수행 되는 것과 동일한 Retry-After 처리를 적용 해야 합니다. 응답에서 Retry-After 헤더를 제공 하지 않는 경우 응답 후 최소 5 초 이상 다시 시도 하 여 지 수 백오프 재시도를 구현 하는 것이 좋습니다.

요청 시간이 초과 되는 응용 프로그램은 즉시 다시 시도 하면 안 됩니다. 응답 후 5 초 이상 다시 시도 하 여 지 수 백오프 다시 시도를 구현 합니다.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>권한 부여 관련 정보를 검색 하는 옵션 평가

많은 응용 프로그램 및 Api에는 권한 부여 결정을 내리는 사용자에 대 한 특정 정보가 필요 합니다. 응용 프로그램에서이 정보를 가져오는 몇 가지 방법이 있습니다. 각 방법에는 장점과 단점이 있습니다. 개발자는 이러한 전략을 평가 하 여 앱의 복원 력에 가장 적합 한 전략을 결정 해야 합니다.

### <a name="tokens"></a>토큰

Id (ID) 토큰 및 액세스 토큰은 주체에 대 한 정보를 제공 하는 표준 클레임을 포함 합니다. 이러한 문서는 [microsoft id 플랫폼 id 토큰](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 및 [microsoft id 플랫폼 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)에 설명 되어 있습니다. 앱에 필요한 정보가 토큰에 이미 있는 경우 해당 데이터를 검색 하는 가장 효율적인 방법은 추가 네트워크 호출의 overheard를 저장 하 여 정보를 별도로 검색 하는 것 처럼 토큰 클레임을 사용 하는 것입니다. 네트워크 호출 수가 적을수록 응용 프로그램에 대 한 전반적인 복원 력이 높아집니다.

> [!NOTE]
> 일부 응용 프로그램은 사용자 정보 끝점을 호출 하 여 인증 된 사용자에 대 한 클레임을 검색 합니다. 앱에서 받을 수 있는 ID 토큰에서 사용할 수 있는 정보는 UserInfo 끝점에서 얻을 수 있는 정보의 상위 집합입니다. 앱은 사용자 정보 끝점을 호출 하는 대신 ID 토큰을 사용 하 여 사용자에 대 한 정보를 가져와야 합니다.

앱 개발자는 [선택적 클레임](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)을 사용 하 여 표준 토큰 클레임을 강화할 수 있습니다. 하나의 일반적인 선택적 클레임은 [그룹](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-groups-optional-claims)입니다. 그룹 클레임을 추가 하는 방법에는 여러 가지가 있습니다. "응용 프로그램 그룹" 옵션에는 응용 프로그램에 할당 된 그룹만 포함 됩니다. "모든" 또는 "보안 그룹" 옵션은 동일한 테 넌 트에 있는 모든 앱의 그룹을 포함 하 여 토큰에 많은 그룹을 추가할 수 있습니다. 토큰의 수를 유발 하 고 추가 호출을 통해 전체 그룹 목록을 가져오기 때문에 토큰의 그룹을 요청 하 여 얻은 효율성을 무시할 수 있으므로 사용자의 사례에 대 한 효과를 평가 하는 것이 중요 합니다.

토큰에서 그룹을 사용 하는 대신 앱 역할을 대신 사용 하 고 포함할 수 있습니다. 개발자는 사용자가 포털 또는 Api를 사용 하 여 디렉터리에서 관리할 수 있는 앱 및 Api에 대 한 [앱 역할](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 을 정의할 수 있습니다. 그러면 IT 전문가는 다른 사용자 및 그룹에 역할을 할당 하 여 어떤 콘텐츠와 기능에 액세스할 수 있는지를 제어할 수 있습니다. 토큰이 응용 프로그램 또는 API에 대해 발급 되 면 해당 사용자에 게 할당 된 역할을 토큰의 역할 클레임에서 사용할 수 있습니다. 토큰에서 직접이 정보를 가져오면 추가 Api 호출을 저장할 수 있습니다.

마지막으로 IT 관리자는 테 넌 트의 특정 정보에 따라 클레임을 추가할 수도 있습니다. 예를 들어 기업에는 엔터프라이즈 관련 사용자 ID를 포함 하는 확장이 있을 수 있습니다.

모든 경우에, 디렉터리의 정보를 토큰에 직접 추가 하는 것이 효율적이 고 앱의 종속성 수를 줄임으로써 앱 복원 력을 높일 수 있습니다. 반면에 토큰을 획득할 수 없는 복원 력 문제는 해결 되지 않습니다. 응용 프로그램의 주요 시나리오에 대 한 선택적 클레임만 추가 해야 합니다. 앱에 관리자 기능에 대 한 정보만 필요한 경우에는 응용 프로그램에서 필요한 경우에만 해당 정보를 가져오는 것이 가장 좋습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph는 조직의 생산성, id 및 보안 패턴을 설명 하는 Microsoft 365 데이터에 액세스 하는 통합 API 끝점을 제공 합니다. Microsoft Graph를 사용 하는 응용 프로그램은 권한 부여 결정을 위해 Microsoft 365에서 모든 정보를 사용할 수 있습니다.

앱에는 모든 Microsoft 365에 액세스 하기 위한 단일 토큰만 필요 합니다. 이는 여러 토큰이 필요한 Microsoft Exchange 또는 Microsoft SharePoint와 같은 Microsoft 365 구성 요소와 관련 된 이전 Api를 사용 하는 것 보다 복원 력이 뛰어납니다.

Microsoft Graph Api를 사용 하는 경우 [MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)를 사용 하는 것이 좋습니다. Microsoft Graph Sdk는 Microsoft Graph에 액세스 하는 고품질의 효율적이 고 복원 력 있는 응용 프로그램을 간편 하 게 빌드할 수 있도록 설계 되었습니다.

권한 부여 결정을 위해 개발자는 토큰에서 사용할 수 있는 클레임을 사용 하 여 일부 Microsoft Graph 호출 대신 사용할 시기를 고려해 야 합니다. 위에서 언급 한 바와 같이 개발자는 토큰에 그룹, 앱 역할 및 선택적 클레임을 요청할 수 있습니다. 복원 력 측면에서 권한 부여를 위해 Microsoft Graph를 사용 하려면 Microsoft Id를 사용 하는 추가 네트워크 호출이 필요 합니다 (Microsoft Graph에 액세스 하는 토큰을 가져오는 데에 Microsoft Graph도). 그러나 응용 프로그램이 이미 Microsoft Graph를 데이터 계층으로 사용 하는 경우에는 권한 부여에 대 한 그래프를 사용 하는 것은 추가 위험이 없습니다.

## <a name="use-broker-authentication-on-mobile-devices"></a>모바일 장치에서 broker 인증 사용

모바일 장치에서 Microsoft Authenticator와 같은 인증 브로커를 사용 하면 복원 력이 향상 됩니다. 브로커는 시스템 브라우저나 포함 된 웹 보기 등의 다른 옵션을 사용 하 여 사용할 수 있는 기능을 위에 추가 합니다. 인증 브로커가 사용자 및 장치에 대 한 클레임이 포함 된 PRT ( [주 새로 고침 토큰](https://docs.microsoft.com/azure/active-directory/devices/concept-primary-refresh-token) )를 활용할 수 있으며, 장치에서 다른 응용 프로그램에 액세스 하기 위해 인증 토큰을 가져오는 데 사용할 수 있습니다. 응용 프로그램에 대 한 액세스를 요청 하는 데 PRT를 사용 하면 Azure AD에서 해당 장치 및 MFA 클레임을 신뢰할 수 있습니다. 이렇게 하면 장치를 다시 인증 하는 추가 단계를 방지 하 여 복원 력이 높아집니다. 사용자는 동일한 장치에서 여러 MFA 프롬프트에 문제가 있으므로 외부 서비스에 대 한 종속성을 줄이고 사용자 환경을 개선 하 여 복원 력을 높일 수 있습니다.

![Microsoft id를 호출 하지만 응용 프로그램을 실행 하는 장치에서 토큰 저장소 및 인증 브로커를 통해 호출 하는 응용 프로그램](media/resilience-client-app/authentication-broker.png)

Broker 인증은 MSAL에서 자동으로 지원 됩니다. 조정 된 인증을 사용 하는 방법에 대 한 자세한 내용은 다음 페이지에서 확인할 수 있습니다.

- [MacOS 및 iOS에서 SSO 구성](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-macos-ios#sso-through-authentication-broker-on-ios)
- [MSAL을 사용 하 여 Android에서 앱 간 SSO를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/develop/msal-android-single-sign-on)

## <a name="adopt-continuous-access-evaluation"></a>연속 액세스 평가 채택

[CAE (연속 액세스 평가)](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 는 수명이 긴 토큰을 사용 하 여 응용 프로그램 보안 및 복원 력을 높일 수 있는 최근 개발입니다. CAE는 Openid connect Foundation의 공유 신호 및 이벤트 작업 그룹에서 개발 되는 새로운 산업 표준입니다. CAE를 사용 하면 짧은 토큰 수명에 의존 하는 대신 [중요 한 이벤트](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#critical-event-evaluation) 및 [정책 평가](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#conditional-access-policy-evaluation-preview)에 따라 액세스 토큰을 해지할 수 있습니다. 일부 리소스 Api의 경우 위험 및 정책이 실시간으로 평가 되므로 CAE는 토큰 수명을 최대 28 시간까지 크게 늘릴 수 있습니다. 리소스 Api 및 응용 프로그램은 CAE을 채택 하므로 Microsoft Id는 취소 가능한 되 고 오랜 시간 동안 유효한 액세스 토큰을 발급할 수 있습니다. 이러한 수명이 긴 토큰은 MSAL을 통해 사전에 새로 고쳐집니다.

CAE는 초기 단계에 있지만 응용 프로그램에서 채택 CAE를 사용 하는 리소스 (Api)를 사용 하는 경우 [CAE를 활용 하는 클라이언트 응용 프로그램을 개발](../develop/app-resilience-continuous-access-evaluation.md) 하는 것이 가능 합니다. 더 많은 리소스가 CAE을 채택 하면 응용 프로그램은 해당 리소스에 대 한 CAE 활성화 된 토큰을 얻을 수 있습니다. Microsoft Graph API 및 [Microsoft Graph sdk](https://docs.microsoft.com/graph/sdks/sdks-overview)는 2021 초기에 CAE 기능을 미리 봅니다. CAE를 사용 하 여 Microsoft Graph의 공개 미리 보기에 참여 하려면에 관심이 있는 것을 알려주세요 [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

리소스 Api를 개발 하는 경우 [공유 신호 및 이벤트 WG](https://openid.net/wg/sse/)에 참여 하는 것이 좋습니다. Microsoft는이 그룹과 협력 하 여 Microsoft Id와 리소스 공급자 간에 보안 이벤트를 공유할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계

- [응용 프로그램에서 지속적인 액세스 평가를 사용 하도록 설정 된 Api를 사용 하는 방법](../develop/app-resilience-continuous-access-evaluation.md)
- [디먼 응용 프로그램에 복원 력 빌드](resilience-daemon-app.md)
- [Id 및 액세스 관리 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [Azure Active Directory B2C를 사용 하 여 고객 id 및 액세스 관리에서 복원 력 빌드](resilience-b2c.md)
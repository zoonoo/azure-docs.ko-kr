---
title: MSAL.NET 클라이언트 응용 프로그램 초기화 | Azure
titleSuffix: Microsoft identity platform
description: NET용 MSAL(Microsoft 인증 라이브러리)을 사용하는 퍼블릭 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771365"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET을 사용하여 클라이언트 응용 프로그램 초기화
이 문서에서는 NET용 MSAL(Microsoft 인증 라이브러리)을 사용하는 퍼블릭 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화하는 방법을 설명합니다.  클라이언트 응용 프로그램 형식에 대한 자세한 내용은 [퍼블릭 클라이언트 및 기밀 클라이언트 응용 프로그램](msal-client-applications.md)을 참조하세요.

MSAL.NET 3.x를 사용하는 경우 애플리케이션 작성기(`PublicClientApplicationBuilder` 및 `ConfidentialClientApplicationBuilder`)를 사용하여 애플리케이션을 인스턴스화하는 것이 좋습니다. 코드 또는 구성 파일에서 애플리케이션을 구성하는 강력한 메커니즘을 제공하거나 두 방법을 모두 혼합하여 제공합니다.

[API 참조 설명서](/dotnet/api/microsoft.identity.client) | [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [라이브러리 소스 코드](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [코드 샘플](sample-v2-code.md)

## <a name="prerequisites"></a>사전 요구 사항
애플리케이션을 초기화하려면 먼저 앱이 Microsoft ID 플랫폼과 통합될 수 있도록 해당 애플리케이션을 [등록](quickstart-register-app.md)해야 합니다.  등록 후에는 다음 정보가 필요할 수 있으며 해당 정보는 Azure Portal에서 찾을 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- ID 공급자 URL(인스턴스 이름) 및 애플리케이션의 로그인 대상. 이러한 두 매개 변수를 통칭하여 권한이라고 합니다.
- 조직 전용 사업 부문 애플리케이션(단일 테넌트 애플리케이션이라고도 함)을 작성하는 경우 테넌트 ID.
- 기밀 클라이언트 앱인 경우 애플리케이션 비밀(클라이언트 암호 문자열) 또는 인증서(X509Certificate2 형식).
- 웹앱의 경우와 때로 퍼블릭 클라이언트 앱의 경우(특히 앱에서 broker를 사용해야 하는 경우) ID 공급자가 애플리케이션에 보안 토큰을 다시 연결할 redirectUri도 설정해야 합니다.

## <a name="ways-to-initialize-applications"></a>애플리케이션 초기화 방법
클라이언트 응용 프로그램을 인스턴스화하는 방법에는 여러 가지가 있습니다.

### <a name="initializing-a-public-client-application-from-code"></a>코드에서 퍼블릭 클라이언트 응용 프로그램 초기화

다음 코드는 퍼블릭 클라이언트 응용 프로그램을 인스턴스화하고, Microsoft Azure 퍼블릭 클라우드의 사용자는 다음 코드를 사용하여 회사 및 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>코드에서 기밀 클라이언트 응용 프로그램 초기화

동일한 방식으로 다음 코드는 회사 및 학교 계정 또는 개인 Microsoft 계정을 사용하여 Microsoft Azure 퍼블릭 클라우드에 있는 사용자의 토큰을 처리하는 기밀 애플리케이션(`https://myapp.azurewebsites.net`에 있는 웹 앱)을 인스턴스화합니다. 애플리케이션은 클라이언트 암호를 공유하여 ID 공급자로 식별됩니다.

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

프로덕션에서는 클라이언트 암호를 사용하는 대신 Azure AD 인증서를 사용하여 공유하는 것이 좋습니다. 그러면 코드가 다음과 같이 됩니다.

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>구성 옵션에서 퍼블릭 클라이언트 응용 프로그램 초기화

다음 코드는 구성 개체에서 퍼블릭 클라이언트 응용 프로그램을 인스턴스화합니다. 이 응용 프로그램은 프로그래밍 방식으로 채우거나 구성 파일에서 읽을 수 있습니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>구성 옵션에서 기밀 클라이언트 응용 프로그램 초기화

기밀 클라이언트 응용 프로그램에는 동일한 종류의 패턴이 적용됩니다. `.WithXXX` 한정자(여기서는 인증서)를 사용하여 다른 매개 변수를 추가할 수도 있습니다.

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>작성기 한정자

애플리케이션 작성기를 사용하는 코드 조각에서는 여러 `.With` 메서드를 한정자로 적용할 수 있습니다(예: `.WithCertificate` 및 `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>퍼블릭 및 기밀 클라이언트 응용 프로그램에 공통되는 한정자

퍼블릭 클라이언트나 기밀 클라이언트 응용 프로그램 작성기에서 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithAuthority()` 7 재정의 | Azure 클라우드, 대상 그룹, 테넌트(테넌트 ID 또는 도메인 이름)를 선택하거나 직접 인증 기관 URI를 제공하여 애플리케이션 기본 기관을 Azure AD 기관으로 설정합니다.|
|`.WithAdfsAuthority(string)` | 애플리케이션 기본 기관을 ADFS 기관으로 설정합니다.|
|`.WithB2CAuthority(string)` | 애플리케이션 기본 기관을 Azure AD B2C 기관으로 설정합니다.|
|`.WithClientId(string)` | 클라이언트 ID를 재정의합니다.|
|`.WithComponent(string)` | 원격 분석을 위해 MSAL.NET을 사용하여 라이브러리의 이름을 설정합니다. |
|`.WithDebugLoggingCallback()` | 호출되는 경우 애플리케이션은 단순히 디버깅 추적을 사용하도록 하는 `Debug.Write`를 호출합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 모든 인증 요청에서 전송될 애플리케이션 수준 추가 쿼리 매개 변수를 설정합니다. 이는 동일한 `.WithExtraQueryParameters pattern`을 사용하여 각 토큰 획득 방법 수준에서 재정의할 수 있습니다.|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | HTTP 프록시 구성 등의 고급 시나리오를 사용하도록 설정하거나 MSAL이 특정 HttpClient(ASP.NET Core 웹앱/API)를 사용하도록 강제합니다.|
|`.WithLogging()` | 호출되는 경우 애플리케이션은 디버깅 추적과 함께 콜백을 호출합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithRedirectUri(string redirectUri)` | 기본 리디렉션 URI를 재정의합니다. 퍼블릭 클라이언트 응용 프로그램의 경우 broker를 포함하는 시나리오에 유용합니다.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 원격 분석을 보내는 데 사용되는 대리자를 설정합니다.|
|`.WithTenantId(string tenantId)` | 테넌트 ID 또는 테넌트 설명을 재정의합니다.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS 애플리케이션에만 적용되는 한정자

Xamarin.iOS에서 퍼블릭 클라이언트 응용 프로그램 작성기에 대해 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS만 해당**: iOS 키 체인 보안 그룹(캐시 지속성)을 설정합니다.|

### <a name="modifiers-specific-to-confidential-client-applications"></a>기밀 클라이언트 응용 프로그램에만 적용되는 한정자

기밀 클라이언트 응용 프로그램 작성기에서 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD를 사용하여 애플리케이션을 식별하는 인증서를 설정합니다.|
|`.WithClientSecret(string clientSecret)` | Azure AD를 사용하여 애플리케이션을 식별하는 클라이언트 암호(앱 암호)를 설정합니다.|

이러한 한정자는 함께 사용할 수 없습니다. 둘 다 제공하는 경우 MSAL은 의미 있는 예외를 throw 합니다.

### <a name="example-of-usage-of-modifiers"></a>한정자 사용 예제

애플리케이션이 조직에만 사용되는 LOB(기간 업무) 애플리케이션이라고 가정합니다.  그런 후에 다음과 같이 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

흥미로운 부분은 이제 국가별 클라우드의 프로그래밍이 간소화되었다는 것입니다. 애플리케이션을 국가별 클라우드의 다중 테넌트 애플리케이션으로 만들려면 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS에 대한 재정의도 있습니다(ADFS 2019은 현재 지원되지 않음).
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로 Azure AD B2C 개발자인 경우 다음과 같이 테넌트를 지정할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>다음 단계

클라이언트 응용 프로그램을 초기화한 후 다음 작업은 사용자 로그인, 권한 있는 API 액세스 또는 둘 다에 대한 지원을 추가하는 것입니다.

애플리케이션 시나리오 설명서에서는 사용자에게 로그인하고 액세스 토큰을 획득하여 해당 사용자를 대신해 API에 액세스하는 지침을 제공합니다.

- [사용자가 로그인하는 웹앱: 로그인 및 로그아웃](scenario-web-app-sign-user-sign-in.md)
- [웹 API를 호출하는 웹앱: 토큰 획득](scenario-web-app-call-api-acquire-token.md)

---
title: 클라이언트 응용 MSAL.NET 초기화 | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 공용 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083999"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET 사용하여 클라이언트 응용 프로그램 초기화
이 문서에서는 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 공용 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화하는 것에 대해 설명합니다.  클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대해 자세히 알아보려면 [개요를](msal-client-applications.md)참조하십시오.

MSAL.NET 3.x에서는 응용 프로그램을 인스턴스화하는 권장 방법은 응용 프로그램 `PublicClientApplicationBuilder` 빌더를 `ConfidentialClientApplicationBuilder`사용하는 것입니다. 코드 또는 구성 파일에서 또는 두 가지 방법을 혼합하여 응용 프로그램을 구성하는 강력한 메커니즘을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 초기화하기 전에 먼저 앱을 Microsoft ID 플랫폼과 통합할 수 있도록 응용 프로그램을 [등록해야](quickstart-register-app.md) 합니다.  등록 후 Azure 포털에서 찾을 수 있는 다음 정보가 필요할 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- 인스턴스라는 ID 공급자 URL과 응용 프로그램의 로그인 대상입니다. 이러한 두 매개 변수를 통칭하여 기관이라고 합니다.
- 조직에 대해서만 비즈니스 응용 프로그램을 작성하는 경우 테넌트 ID(단일 테넌트 응용 프로그램이라고도 함).
- 응용 프로그램 보안(클라이언트 비밀 문자열) 또는 인증서(X509Certificate2 형식)가 기밀 클라이언트 앱인 경우입니다.
- 웹 앱의 경우, 공용 클라이언트 앱(특히 앱이 브로커를 사용해야 하는 경우)의 경우 ID 공급자가 보안 토큰으로 응용 프로그램에 다시 연락하는 리디렉션Uri도 설정해야 합니다.

## <a name="ways-to-initialize-applications"></a>응용 프로그램을 초기화하는 방법
클라이언트 응용 프로그램을 인스턴스화하는 방법에는 여러 가지가 있습니다.

### <a name="initializing-a-public-client-application-from-code"></a>코드에서 공용 클라이언트 응용 프로그램 초기화

다음 코드는 공용 클라이언트 응용 프로그램, Microsoft Azure 공용 클라우드의 로그인 사용자, 업무 및 학교 계정 또는 개인 Microsoft 계정을 인스턴스화합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>코드에서 기밀 클라이언트 응용 프로그램 초기화

마찬가지로 다음 코드는 Microsoft Azure 퍼블릭 클라우드의 사용자, `https://myapp.azurewebsites.net`회사 및 학교 계정 또는 개인 Microsoft 계정의 토큰을 처리하는 기밀 응용 프로그램(웹 앱)을 인스턴스화합니다. 응용 프로그램은 클라이언트 비밀을 공유하여 ID 공급자와 식별됩니다.

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

아시다시피 프로덕션 환경에서는 클라이언트 비밀을 사용하는 대신 Azure AD 인증서와 인증서를 공유할 수 있습니다. 그러면 코드는 다음과 같은 것입니다.

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>구성 옵션에서 공용 클라이언트 응용 프로그램 초기화

다음 코드는 프로그래밍 방식으로 채워지거나 구성 파일에서 읽을 수 있는 구성 개체에서 공용 클라이언트 응용 프로그램을 인스턴스화합니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>구성 옵션에서 기밀 클라이언트 응용 프로그램 초기화

기밀 클라이언트 응용 프로그램에도 동일한 종류의 패턴이 적용됩니다. 수정자(여기에 인증서)를 `.WithXXX` 사용하여 다른 매개 변수를 추가할 수도 있습니다.

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>빌더 수정자

응용 프로그램 빌더를 사용하는 코드 조각에서 `.With` 여러 가지 메서드를 수정자(예: `.WithCertificate` `.WithRedirectUri`)로 적용할 수 있습니다. 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>공용 및 기밀 클라이언트 응용 프로그램에 공통적인 수정사항

공용 클라이언트 또는 기밀 클라이언트 응용 프로그램 빌더에 설정할 수 있는 수정자는 다음과 같습니다.

|한정자 | 설명|
|--------- | --------- |
|`.WithAuthority()`7 재정의 | Azure Cloud, Audience, 테넌트(테넌트 ID 또는 도메인 이름)를 선택하거나 권한 URI를 직접 제공할 수 있는 응용 프로그램 기본 권한을 Azure AD 권한으로 설정합니다.|
|`.WithAdfsAuthority(string)` | 응용 프로그램 기본 권한을 ADFS 기관으로 설정합니다.|
|`.WithB2CAuthority(string)` | 응용 프로그램 기본 권한을 Azure AD B2C 기관으로 설정합니다.|
|`.WithClientId(string)` | 클라이언트 ID를 재정의합니다.|
|`.WithComponent(string)` | 원격 분석상의 이유로 MSAL.NET 사용하여 라이브러리이름을 설정합니다. |
|`.WithDebugLoggingCallback()` | 호출하는 경우 응용 `Debug.Write` 프로그램은 단순히 디버깅 추적을 사용하도록 요청합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 모든 인증 요청에서 전송되는 응용 프로그램 수준 추가 쿼리 매개 변수를 설정합니다. 이는 각 토큰 획득 방법 수준에서 재정의할 `.WithExtraQueryParameters pattern`수 있습니다(동일).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | HTTP 프록시를 구성하거나 MSAL이 특정 HttpClient(예: ASP.NET 코어 웹 앱/API)를 사용하도록 강제할 수 있습니다.|
|`.WithLogging()` | 호출된 경우 응용 프로그램은 디버깅 추적이 있는 콜백을 호출합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithRedirectUri(string redirectUri)` | 기본 리디렉션 URI를 재정의합니다. 공용 클라이언트 응용 프로그램의 경우 브로커와 관련된 시나리오에 유용합니다.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 원격 분석을 보내는 데 사용되는 대리자를 설정합니다.|
|`.WithTenantId(string tenantId)` | 테넌트 ID 또는 테넌트 설명을 재정의합니다.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS 응용 프로그램에 특정한 수정자

Xamarin.iOS의 공용 클라이언트 응용 프로그램 빌더에서 설정할 수 있는 수정자는 다음과 같습니다.

|한정자 | 설명|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS 전용**: iOS 키 체인 보안 그룹을 설정합니다(캐시 지속성).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>기밀 클라이언트 응용 프로그램과 관련된 수정사항

기밀 클라이언트 응용 프로그램 빌더에서 설정할 수 있는 수정자는 다음과 같습니다.

|한정자 | 설명|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD를 사용 하 고 응용 프로그램을 식별 하는 인증서를 설정 합니다.|
|`.WithClientSecret(string clientSecret)` | Azure AD를 사용 하 고 응용 프로그램을 식별 하는 클라이언트 암호 (응용 프로그램 암호)를 설정 합니다.|

이러한 수정자는 상호 배타적입니다. 둘 다 제공 하는 경우 MSAL 의미 있는 예외를 throw 합니다.

### <a name="example-of-usage-of-modifiers"></a>수정자 사용 예

응용 프로그램이 조직에만 적합한 한정 일용 응용 프로그램이라고 가정해 보겠습니다.  그런 다음 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

흥미로운 점은 이제 국가 클라우드에 대한 프로그래밍이 간소화되었다는 것입니다. 응용 프로그램이 국가 클라우드에서 다중 테넌트 응용 프로그램으로 지정하려면 다음과 같은 방식으로 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS에 대한 재정의도 있습니다(ADFS 2019는 현재 지원되지 않음).
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

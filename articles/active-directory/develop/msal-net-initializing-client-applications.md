---
title: 클라이언트 응용 프로그램 (Microsoft Authentication Library for.NET)를 초기화 합니다. | Azure
description: 공용 클라이언트 및.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 기밀 클라이언트 응용 프로그램을 초기화 하는 방법을 알아봅니다.
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544326"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET을 사용 하 여 클라이언트 응용 프로그램 초기화
이 문서에서는 공용 클라이언트 및.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 기밀 클라이언트 응용 프로그램 초기화를 설명 합니다.  클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대 한 자세한 내용은 참조는 [개요](msal-client-applications.md)합니다.

MSAL.NET을 사용 하 여 응용 프로그램 작성기를 사용 하 여 3.x 응용 프로그램을 인스턴스화할 수 있는 좋은: `PublicClientApplicationBuilder` 및 `ConfidentialClientApplicationBuilder`합니다. 코드 또는 구성 파일에서 또는 두 접근 방식 혼합 하 여도 응용 프로그램을 구성 하는 강력한 메커니즘을 제공 합니다.

## <a name="prerequisites"></a>필수 조건
응용 프로그램을 초기화 하기 전에 먼저 [등록](quickstart-register-app.md) Microsoft id 플랫폼을 사용 하 여 앱을 통합 될 수 있도록 합니다.  등록이 완료 되 면 다음 정보를 (Azure portal에서 찾을 수 있습니다) 해야 합니다.

- 클라이언트 ID (GUID를 나타내는 문자열)
- Id 공급자 URL (명명 된 인스턴스) 및 응용 프로그램에 대 한 로그인 대상 그룹입니다. 이 두 매개 변수를 기관으로 통칭 합니다.
- 조직 (또한 명명 된 단일 테 넌 트 응용 프로그램)에 대해 전적으로 비즈니스 응용 프로그램을 작성 하는 경우에 테 넌 트 ID입니다.
- 응용 프로그램 암호 (클라이언트 암호 문자열) 또는 유형의 X509Certificate2 인증서를 비밀 클라이언트 앱 인 경우.
- 웹 앱 및 경우에 따라 공용 클라이언트 앱 (특히 앱은 broker 사용 해야 하는 경우)에 대 한 것도 설정한 redirectUri는 id 공급자 백 연락을 보안 토큰을 사용 하 여 응용 프로그램.

## <a name="ways-to-initialize-applications"></a>응용 프로그램을 초기화 하는 방법
여러 가지 다른 클라이언트 응용 프로그램을 인스턴스화할 수 있습니다.

### <a name="initializing-a-public-client-application-from-code"></a>코드에서 공용 클라이언트 응용 프로그램을 초기화합니다.

다음 코드는 공용 클라이언트 응용 프로그램을 회사 및 학교 계정 또는 개인 Microsoft 계정이 Microsoft Azure 공용 클라우드, 사용자 로그인을 인스턴스화합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>기밀 클라이언트 응용 프로그램 코드에서 초기화

동일한 방법으로 다음 코드는 기밀 응용 프로그램을 인스턴스화합니다 (웹 앱에 있는 `https://myapp.azurewebsites.net`) 회사 및 학교 계정 또는 개인 Microsoft 계정이 Microsoft Azure 공용 클라우드, 사용자의 토큰을 처리 합니다. 응용 프로그램 클라이언트 암호를 공유 하 여 id 공급자를 사용 하 여 식별 됩니다.

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

아시다시피, 클라이언트 암호를 사용 하는 것이 아니라 프로덕션 환경에서 Azure AD를 사용 하 여 인증서를 공유 하는 것이 좋습니다. 다음 코드는 다음 됩니다.

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>구성 옵션에서 공용 클라이언트 응용 프로그램을 초기화합니다.

다음 코드는 되거나 수 있는 입력 기능에 프로그래밍 방식으로 구성 파일에서 읽을 구성 개체의 공용 클라이언트 응용 프로그램을 인스턴스화합니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>구성 옵션에서 기밀 클라이언트 응용 프로그램을 초기화합니다.

같은 종류의 패턴 기밀 클라이언트 응용 프로그램에 적용 됩니다. 사용 하 여 다른 매개 변수를 추가할 수도 있습니다 `.WithXXX` 한정자 (여기 인증서).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>작성기 한정자

다양 한 응용 프로그램 작성기를 사용 하 여 코드 조각에서 `.With` 한정자로 메서드를 적용할 수 있습니다 (예를 들어 `.WithCertificate` 고 `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>공용 및 기밀 클라이언트 응용 프로그램에 공통적으로 적용 되는 한정자

공용 클라이언트 또는 기밀 클라이언트 응용 프로그램 작성기에서 설정할 수 있습니다 한정자 다음과 같습니다.

|매개 변수 | 설명|
|--------- | --------- |
|`.WithAuthority()` 7 재정 | 응용 프로그램 기본 기관에서 Azure 클라우드로 대상 그룹, 테 넌 트 (테 넌 트 ID 또는 도메인 이름)을 선택 하거나 직접 기관 URI를 제공 하는 가능성을 사용 하 여 Azure AD 기관을 설정 합니다.|
|`.WithAdfsAuthority(string)` | 응용 프로그램 기본 기관을 ADFS 기관을 설정 합니다.|
|`.WithB2CAuthority(string)` | 응용 프로그램 기본 기관을 Azure AD B2C 기관을 설정 합니다.|
|`.WithClientId(string)` | 클라이언트 ID를 재정의합니다.|
|`.WithComponent(string)` | 이유로 원격 분석 MSAL.NET을 사용 하 여 라이브러리의 이름을 설정 합니다. |
|`.WithDebugLoggingCallback()` | 응용 프로그램 호출을 호출 하는 경우 `Debug.Write` 추적 디버깅 활성화 하기만 하면 됩니다. 참조 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) 자세한 내용은 합니다.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 모든 인증 요청에 전송 되는 응용 프로그램 수준 추가 쿼리 매개 변수를 설정 합니다. 이 재정의할 수 있는 각 토큰 획득 메서드 수준 (동일한 `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 고급 시나리오 (예를 들어 ASP.NET Core 웹 앱/a p i)의 특정 HttpClient를 사용 하 여 MSAL을 적용할 또는 HTTP 프록시를 구성 하는 등을 사용 합니다.|
|`.WithLogging()` | 호출 응용 프로그램 추적을 디버깅 하는 콜백을 호출 합니다. 참조 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) 자세한 내용은 합니다.|
|`.WithRedirectUri(string redirectUri)` | 기본 리디렉션 URI를 재정의합니다. 공용 클라이언트 응용 프로그램의 경우이 유용 broker와 관련 된 시나리오에 대 한 합니다.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 원격 분석을 보내는 데 대리자를 가져오거나 설정 합니다.|
|`.WithTenantId(string tenantId)` | 테 넌 트 ID 또는 테 넌 트 설명 보다 우선합니다.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS 응용 프로그램 전용 한정자

Xamarin.iOS에서 공용 클라이언트 응용 프로그램 작성기에서 설정할 수 있습니다 한정자 다음과 같습니다.

|매개 변수 | 설명|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS만**: (Cache 지 속성)에 대 한 iOS 키 보안 그룹을 설정합니다.|

### <a name="modifiers-specific-to-confidential-client-applications"></a>기밀 클라이언트 응용 프로그램에 특정 한정자

기밀 클라이언트 응용 프로그램 작성기에서 설정할 수 있습니다 한정자 다음과 같습니다.

|매개 변수 | 설명|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD 사용 하 여 응용 프로그램을 식별 하는 인증서를 설정 합니다.|
|`.WithClientSecret(string clientSecret)` | Azure AD 사용 하 여 응용 프로그램을 식별 하면 클라이언트 비밀 (앱 암호)을 설정 합니다.|

이러한 한정자는 함께 사용할 수 없습니다. 둘 다를 제공 하는 경우 MSAL 의미 있는 예외를 throw 합니다.

### <a name="example-of-usage-of-modifiers"></a>한정자의 사용법의 예

응용 프로그램은 조직에 대 한 기간 업무 응용 프로그램을 가정해 보겠습니다.  다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

흥미로운 하 되는 국내 클라우드에 대 한 프로그래밍에 이제 간소화 됩니다. 원하는 경우 응용 프로그램에 national 클라우드에서 다중 테 넌 트 응용 프로그램을 작성할 수 있습니다, 예를 들어:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS에 대 한 재정의 이기도 (ADFS 2019는 현재 지원 되지 않음).
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로, Azure AD B2C 개발자 인 경우 다음과 같은 테 넌 트를 지정할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

---
title: 기밀 클라이언트 앱 인스턴스화(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 구성 옵션으로 기밀 클라이언트 애플리케이션을 인스턴스화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583945"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET을 사용하여 구성 옵션으로 기밀 클라이언트 애플리케이션 인스턴스화

이 문서에서는 .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 [기밀 클라이언트 애플리케이션](msal-client-applications.md)을 인스턴스화하는 방법을 설명합니다.  애플리케이션은 설정 파일에 정의된 구성 옵션으로 인스턴스화됩니다.

애플리케이션을 초기화하려면 먼저 앱이 Microsoft ID 플랫폼과 통합될 수 있도록 해당 애플리케이션을 [등록](quickstart-register-app.md)해야 합니다. 등록 후에는 다음 정보가 필요할 수 있으며 해당 정보는 Azure Portal에서 찾을 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- ID 공급자 URL(인스턴스 이름) 및 애플리케이션의 로그인 대상. 이러한 두 매개 변수를 통칭하여 권한이라고 합니다.
- 조직 전용 기간 업무 애플리케이션(단일 테넌트 애플리케이션이라고도 함)을 작성하는 경우 테넌트 ID.
- 기밀 클라이언트 앱인 경우 애플리케이션 비밀(클라이언트 암호 문자열) 또는 인증서(X509Certificate2 형식).
- 웹앱의 경우와 때로 공용 클라이언트 앱의 경우(특히 앱에서 broker를 사용해야 하는 경우) ID 공급자가 애플리케이션에 보안 토큰을 다시 연결할 redirectUri도 설정해야 합니다.

## <a name="configure-the-application-from-the-config-file"></a>구성 파일에서 애플리케이션 구성
MSAL.NET에 있는 옵션의 속성 이름은 ASP.NET Core의 `AzureADOptions` 속성 이름과 일치하므로 glue 코드를 작성할 필요가 없습니다.

ASP.NET Core 애플리케이션 구성은 *appsettings.json* 파일에 설명되어 있습니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

MSAL.NET v3.x부터 구성 파일에서 기밀 클라이언트 애플리케이션을 구성할 수 있습니다.

애플리케이션을 구성하고 인스턴스화하려는 클래스에서 `ConfidentialClientApplicationOptions` 개체를 선언합니다.  [Microsoft.Extensions.Configuration.Binder NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)의 `IConfigurationRoot.Bind()` 메서드를 사용하여 원본(appconfig.json 파일 포함)에서 읽은 구성을 애플리케이션 옵션의 인스턴스에 바인딩합니다

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

이렇게 하면 *appsettings.json* 파일의 "AzureAD" 섹션의 콘텐츠가 `ConfidentialClientApplicationOptions` 개체의 해당 속성에 바인딩될 수 있습니다.  다음으로, `ConfidentialClientApplication` 개체를 빌드합니다.

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>런타임 구성 추가
기밀 클라이언트 애플리케이션에서는 일반적으로 사용자마다 캐시가 있습니다. 따라서 사용자와 연결된 캐시를 가져와서 사용하겠다는 것을 애플리케이션 작성기에 알려야 합니다. 동일한 방식으로 동적으로 계산된 리디렉션 URI가 있을 수 있습니다. 이 경우 코드는 다음과 같습니다.

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```


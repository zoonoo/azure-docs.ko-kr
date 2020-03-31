---
title: 기밀 클라이언트 앱(MSAL.NET) 인스턴스화 | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 구성 옵션을 사용하여 기밀 클라이언트 응용 프로그램을 인스턴스화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084742"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET 사용하여 구성 옵션을 사용하여 기밀 클라이언트 응용 프로그램을 인스턴스화합니다.

이 문서에서는 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 [기밀 클라이언트 응용 프로그램을](msal-client-applications.md) 인스턴스화하는 방법에 대해 설명합니다.  응용 프로그램은 설정 파일에 정의된 구성 옵션으로 인스턴스화됩니다.

응용 프로그램을 초기화하기 전에 먼저 앱을 Microsoft ID 플랫폼과 통합할 수 있도록 응용 프로그램을 [등록해야](quickstart-register-app.md) 합니다. 등록 후 Azure 포털에서 찾을 수 있는 다음 정보가 필요할 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- 인스턴스라는 ID 공급자 URL과 응용 프로그램의 로그인 대상입니다. 이러한 두 매개 변수를 통칭하여 기관이라고 합니다.
- 조직에 대해서만 비즈니스 응용 프로그램을 작성하는 경우 테넌트 ID(단일 테넌트 응용 프로그램이라고도 함).
- 응용 프로그램 보안(클라이언트 비밀 문자열) 또는 인증서(X509Certificate2 형식)가 기밀 클라이언트 앱인 경우입니다.
- 웹 앱의 경우, 공용 클라이언트 앱(특히 앱이 브로커를 사용해야 하는 경우)의 경우 ID 공급자가 보안 토큰으로 응용 프로그램에 다시 연락하는 리디렉션Uri도 설정해야 합니다.

## <a name="configure-the-application-from-the-config-file"></a>구성 파일에서 응용 프로그램 구성
MSAL.NET 옵션의 속성 이름이 ASP.NET Core의 `AzureADOptions` 속성 이름과 일치하므로 접착제 코드를 작성할 필요가 없습니다.

ASP.NET 핵심 응용 프로그램 구성은 *appsettings.json* 파일에 설명되어 있습니다.

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

MSAL.NET v3.x부터 는 구성 파일에서 기밀 클라이언트 응용 프로그램을 구성할 수 있습니다.

응용 프로그램을 구성하고 인스턴스화하려는 클래스에서는 개체를 `ConfidentialClientApplicationOptions` 선언해야 합니다.  `IConfigurationRoot.Bind()` [Microsoft.Extension.Configuration.Binder nuget 패키지의 메서드를](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)사용하여 소스에서 읽은 구성(appconfig.json 파일 포함)을 응용 프로그램 옵션의 인스턴스에 바인딩합니다.

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

이렇게 하면 *appsettings.json* 파일의 "AzureAD" 섹션의 내용을 `ConfidentialClientApplicationOptions` 개체의 해당 속성에 바인딩할 수 있습니다.  다음으로 개체를 `ConfidentialClientApplication` 빌드합니다.

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>런타임 구성 추가
기밀 클라이언트 응용 프로그램에서는 일반적으로 사용자당 캐시가 있습니다. 따라서 사용자와 연결된 캐시를 얻고 응용 프로그램 작성자에게 캐시를 사용하려는 것을 알려야 합니다. 같은 방법으로 동적으로 계산된 리디렉션 URI가 있을 수 있습니다. 이 경우 코드는 다음과 같은 것입니다.

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```


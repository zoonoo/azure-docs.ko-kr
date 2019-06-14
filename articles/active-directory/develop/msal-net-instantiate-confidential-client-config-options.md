---
title: 옵션 (Microsoft Authentication Library for.NET)를 사용 하 여 기밀 클라이언트 앱을 인스턴스화하고 | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 구성 옵션을 사용 하 여 기밀 클라이언트 응용 프로그램을 인스턴스화하는 방법에 알아봅니다.
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544078"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET을 사용 하 여 구성 옵션을 사용 하 여 기밀 클라이언트 응용 프로그램 인스턴스화

이 문서를 인스턴스화하는 방법을 설명 합니다는 [기밀 클라이언트 응용 프로그램](msal-client-applications.md) .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 합니다.  응용 프로그램 설정 파일에 정의 된 구성 옵션을 사용 하 여 인스턴스화됩니다.

응용 프로그램을 초기화 하기 전에 먼저 [등록](quickstart-register-app.md) 해당 Microsoft id 플랫폼을 사용 하 여 앱을 통합 될 수 있도록 합니다. 등록이 완료 되 면 다음 정보를 (Azure portal에서 찾을 수 있습니다) 해야 합니다.

- 클라이언트 ID (GUID를 나타내는 문자열)
- Id 공급자 URL (명명 된 인스턴스) 및 응용 프로그램에 대 한 로그인 대상 그룹입니다. 이 두 매개 변수를 기관으로 통칭 합니다.
- 조직 (또한 명명 된 단일 테 넌 트 응용 프로그램)에 대해 전적으로 비즈니스 응용 프로그램을 작성 하는 경우에 테 넌 트 ID입니다.
- 응용 프로그램 암호 (클라이언트 암호 문자열) 또는 유형의 X509Certificate2 인증서를 비밀 클라이언트 앱 인 경우.
- 웹 앱 및 경우에 따라 공용 클라이언트 앱 (특히 앱은 broker 사용 해야 하는 경우)에 대 한 것도 설정한 redirectUri는 id 공급자 백 연락을 보안 토큰을 사용 하 여 응용 프로그램.

## <a name="configure-the-application-from-the-config-file"></a>구성 파일에서 응용 프로그램 구성
MSAL.NET의 옵션 속성의 이름이 속성 이름과 일치 합니다 `AzureADOptions` ASP.NET Core에서 따라서 필요가 글 루 코드를 작성 합니다.

ASP.NET Core 응용 프로그램 구성에 설명 되어는 *appsettings.json* 파일:

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

MSAL.NET v3.x부터 구성 파일에서 기밀 클라이언트 응용 프로그램을 구성할 수 있습니다. 앱 구성에 관련 된 클래스에는 `Microsoft.Identity.Client.AppConfig` 네임 스페이스입니다.

구성 하 고 응용 프로그램을 인스턴스화할 하려는 클래스에서 선언 해야 하는 `ConfidentialClientApplicationOptions` 개체입니다.  (Appconfig.json 파일 포함) 원본에서 읽은 구성이 응용 프로그램 옵션의 인스턴스에 바인딩하십시오.

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

이렇게 하면 "AzureAD" 섹션의 콘텐츠를 *appsettings.json* 파일의 해당 속성에 바인딩할 수는 `ConfidentialClientApplicationOptions` 개체.  다음으로 빌드를 `ConfidentialClientApplication` 개체:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>런타임 구성 추가
기밀 클라이언트 응용 프로그램에서는 일반적으로 해야 사용자 당 캐시 합니다. 따라서 사용자와 연결 된 캐시를 가져오고 사용 하려는 응용 프로그램 작성기에 알리기 위해 해야 합니다. 마찬가지로, 리디렉션 URI를 동적으로 계산된 해야 합니다. 이 경우 코드는 다음과 같습니다.

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```


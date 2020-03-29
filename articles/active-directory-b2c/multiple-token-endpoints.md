---
title: OWIN 기반 웹 API마이그레이션을 통해 b2clogin.com
titleSuffix: Azure AD B2C
description: 응용 프로그램을 b2clogin.com 마이그레이션하는 동안 여러 토큰 발급자가 발급한 토큰을 지원하도록 .NET 웹 API를 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184097"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>OWIN 기반 웹 API를 마이그레이션하여 b2clogin.com

이 문서에서는 [.NET(OWIN)에 대한 개방형 웹 인터페이스를](http://owin.org/)구현하는 웹 API에서 여러 토큰 발급자를 지원하기 위한 기술을 설명합니다. 여러 토큰 끝점을 지원하는 것은 Azure Active Directory B2C(Azure AD B2C) API 및 해당 응용 프로그램을 *login.microsoftonline.com* *b2clogin.com*마이그레이션할 때 유용합니다.

b2clogin.com login.microsoftonline.com 발급된 토큰을 수락하기 위한 지원을 API에 추가하면 API에서 login.microsoftonline.com 발급된 토큰에 대한 지원을 제거하기 전에 단계별로 웹 응용 프로그램을 마이그레이션할 수 있습니다.

다음 섹션에서는 [Microsoft OWIN][katana] 미들웨어 구성 요소(Katana)를 사용하는 웹 API에서 여러 발급자를 사용하도록 설정하는 방법에 대한 예제를 제공합니다. 코드 예제는 Microsoft OWIN 미들웨어에만 해당되지만 일반적인 기술은 다른 OWIN 라이브러리에 적용되어야 합니다.

> [!NOTE]
> 이 문서는 현재 배포된 API 및 참조하고 `login.microsoftonline.com` 권장 `b2clogin.com` 끝점으로 마이그레이션하려는 응용 프로그램을 가진 Azure AD B2C 고객을 위한 것입니다. 새 응용 프로그램을 설정하는 경우 지시대로 [b2clogin.com](b2clogin.md) 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 계속하기 전에 다음 Azure AD B2C 리소스가 필요합니다.

* 테넌트에서 만든 [사용자 흐름](tutorial-create-user-flows.md) 또는 사용자 [지정 정책](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>토큰 발급자 끝점 받기

먼저 API에서 지원하려는 각 발급사에 대한 토큰 발급자 끝점 URI를 받아야 합니다. Azure AD B2C 테넌트에서 지원하는 *b2clogin.com* 및 *login.microsoftonline.com* 끝점을 얻으려면 Azure Portal에서 다음 절차를 사용합니다.

기존 사용자 흐름 중 하나를 선택하여 시작합니다.

1. [Azure 포털에서](https://portal.azure.com) Azure AD B2C 테넌트로 이동
1. **정책에서** **사용자 흐름(정책)을 선택합니다.**
1. 기존 정책을 선택(예: *B2C_1_signupsignin1)* **사용자 흐름 실행을** 선택합니다.
1. 페이지 상단 근처의 **사용자 흐름 실행** 제목에서 하이퍼링크를 선택하여 해당 사용자 흐름에 대한 OpenID Connect 검색 끝점으로 이동합니다.

    ![Azure 포털의 지금 실행 페이지에서 잘 알려진 URI 하이퍼링크](media/multi-token-endpoints/portal-01-policy-link.png)

1. 브라우저에서 열리는 페이지에서 `issuer` 다음과 같은 값을 기록합니다.

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 도메인 **선택** 드롭다운을 사용하여 다른 도메인을 선택한 다음 이전 두 단계를 `issuer` 다시 수행하고 해당 값을 기록합니다.

이제 다음과 유사한 두 개의 URI가 기록되어야 합니다.

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>사용자 지정 정책

사용자 흐름 대신 사용자 지정 정책이 있는 경우 유사한 프로세스를 사용하여 발급자 URI를 얻을 수 있습니다.

1. Azure AD B2C 테넌트로 이동
1. **ID 환경 프레임워크** 선택
1. 예를 들어, B2C_1A_signup_signin *B2C_1A_signup_signin*
1. 도메인 **선택** 드롭다운을 사용하여 도메인을 선택합니다(예: *yourtenant.b2clogin.com*
1. **OpenID Connect 검색 끝점** 아래에 표시되는 하이퍼링크를 선택합니다.
1. `issuer` 값 기록
1. 다른 도메인에 대해 4-6단계(예: *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

토큰 끝점 URI가 모두 있으므로 코드를 업데이트하여 두 끝점이 모두 유효한 발급자임을 지정해야 합니다. 예제를 진행하려면 샘플 응용 프로그램을 다운로드하거나 복제한 다음 샘플을 업데이트하여 두 끝점을 모두 유효한 발급자로 지원합니다.

아카이브 다운로드: [액티브 디렉토리-b2c-도트넷-웹앱-및 웹api-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>웹 API에서 여러 발급자 활성화

이 섹션에서는 두 토큰 발급자 끝점이 모두 유효한지 지정하도록 코드를 업데이트합니다.

1. 비주얼 스튜디오에서 **B2C-WebAPI-DotNet.sln** 솔루션 열기
1. **TaskService** 프로젝트에서 *작업 서비스\\App_Start\\**Startup.Auth.cs*** 파일을 편집기에서 엽니다.
1. 파일 맨 `using` 위에 다음 지시문을 추가합니다.

    `using System.Collections.Generic;`
1. 정의에 [`ValidIssuers`][validissuers] [`TokenValidationParameters`][tokenvalidationparameters] 속성을 추가하고 이전 섹션에서 기록한 두 URI를 모두 지정합니다.

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`MSAL.NET 의해 제공되며 *Startup.Auth.cs*코드의 다음 섹션에서 OWIN 미들웨어에 의해 사용됩니다. 여러 유효한 발급자가 지정되면 OWIN 응용 프로그램 파이프라인은 두 토큰 끝점이 유효한 발급자임을 인식하게 됩니다.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

앞에서 설명한 것처럼 다른 OWIN 라이브러리는 일반적으로 여러 발급자를 지원하기 위한 유사한 기능을 제공합니다. 모든 라이브러리에 대한 예제를 제공하는 것은 이 문서의 범위를 벗어나지만 대부분의 라이브러리에 대해 유사한 기술을 사용할 수 있습니다.

## <a name="switch-endpoints-in-web-app"></a>웹 앱에서 끝점 전환

이제 웹 API에서 두 URI를 모두 지원하므로 b2clogin.com 끝점에서 토큰을 검색할 수 있도록 웹 응용 프로그램을 업데이트해야 합니다.

예를 들어 *TaskWebApp\\**Web.config*** **TaskWebApp** `ida:AadInstance` 프로젝트의 파일에서 값을 수정하여 새 끝점을 사용하도록 샘플 웹 응용 프로그램을 구성할 수 있습니다.

TaskWebApp의 `ida:AadInstance` *Web.config에서* 값을 변경하여 `{your-b2c-tenant-name}.b2clogin.com` `login.microsoftonline.com`대신 참조할 수 있습니다.

이전:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

후(B2C 테넌트의 이름으로 바꾸기): `{your-b2c-tenant}`

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

웹 앱을 실행하는 동안 끝점 문자열이 생성되면 토큰을 요청할 때 b2clogin.com 기반 끝점이 사용됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 발급자 끝점에서 토큰을 수락하기 위해 Microsoft OWIN 미들웨어(Katana)를 구현하는 웹 API를 구성하는 방법을 제시했습니다. 알 수 있듯이, 자신의 테넌트에 대해 이러한 프로젝트를 빌드하고 실행하려는 경우 변경해야 하는 TaskService 및 TaskWebApp 프로젝트의 *Web.Config* 파일에는 여러 개의 다른 문자열이 있습니다. 프로젝트를 제대로 수행하려면 프로젝트를 적절하게 수정할 수 있지만 이 작업을 수행하는 전체 연습은 이 문서의 범위를 벗어납니다.

Azure AD B2C에서 내보내는 다양한 유형의 보안 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요를](tokens-overview.md)참조하십시오.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters

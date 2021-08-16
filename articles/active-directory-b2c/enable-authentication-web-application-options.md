---
title: Azure Active Directory B2C를 사용하여 웹 애플리케이션 옵션을 사용하도록 설정합니다
description: 여러 가지 방법을 사용하여 웹 애플리케이션 옵션을 사용하도록 설정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3335e035a2d36cc7830d8bc93db82a7d318d26b3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110482851"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c-options"></a>Azure Active Directory B2C 옵션을 사용하여 샘플 웹 애플리케이션에서 인증을 구성합니다

이 문서에서는 웹 애플리케이션에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용자 지정하고 개선할 수 있는 방법을 설명합니다. 시작하기 전에 [샘플 웹 애플리케이션에서 인증을 구성](configure-authentication-sample-web-app.md)하거나 [웹 애플리케이션에서 인증을 사용하도록 설정](enable-authentication-web-application.md)하는 다음 문서를 숙지합니다.

## <a name="use-a-custom-domain"></a>사용자 지정 도메인 사용

애플리케이션의 리디렉션 URL에 [사용자 지정 도메인](custom-domain.md)을 사용하면 보다 원활한 사용자 환경을 제공할 수 있습니다. 사용자의 관점에서, 사용자들은 로그인 과정에서 Azure AD B2C 기본 도메인인 .b2clogin.com으로 리디렉션되지 않고 도메인에 그대로 남아 있습니다.

사용자 지정 도메인을 사용하려면, [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따르세요. 프로젝트 루트 폴더 아래에서 `appsettings.json` 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 사용자 지정 도메인을 사용하여 `Instance` 항목을 업데이트합니다.

다음 JSON은 변경 전의 앱 설정을 보여 줍니다. 

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  ...
}
```  

다음 JSON은 변경 후의 앱 설정을 보여 줍니다. 

```JSon
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  ...
}
``` 

## <a name="use-your-tenant-id"></a>테넌트 ID 사용

URL의 “b2c”에 대한 모든 참조를 제거하기 위해 URL의 B2C 테넌트 이름을 테넌트 ID GUID로 바꿀 수 있습니다.  예를 들어, `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/`을 `https://account.contosobank.co.uk/<tenant ID GUID>/`로 변경할 수 있습니다.

테넌트 ID를 사용하려면 [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md#optional-use-tenant-id)의 참고 자료를 따르세요. 프로젝트 루트 폴더 아래에서 `appsettings.json` 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 사용자 지정 도메인을 사용하여 `Domain` 항목을 업데이트합니다.

다음 JSON은 변경 전의 앱 설정을 보여 줍니다. 

```JSon
"AzureAdB2C": {
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

다음 JSON은 변경 후의 앱 설정을 보여 줍니다.

```JSon
"AzureAdB2C": {
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>고급 시나리오 지원

개발자는 Microsoft ID 플랫폼 API의 `AddMicrosoftIdentityWebAppAuthentication` 메서드를 사용하여 고급 인증 시나리오에 대한 코드를 추가하거나 OpenIdConnect 이벤트를 구독할 수 있습니다. 예를 들어, 앱이 Azure AD B2C로 보내는 인증 요청을 사용자 지정할 수 있는 OnRedirectToIdentityProvider를 구독할 수 있습니다.

고급 시나리오를 지원하려면, `Startup.cs`을 열고 `ConfigureServices` 함수에서 `AddMicrosoftIdentityWebAppAuthentication`을 다음 코드 조각으로 바꿉니다. 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

위의 코드는 *OnRedirectToIdentityProviderFunc* 메서드에 대한 참조를 사용하여 OnRedirectToIdentityProvider 이벤트를 추가합니다. `Startup.cs` 클래스에 다음 코드 조각을 추가합니다.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

컨텍스트 매개 변수를 사용하여 컨트롤러와 *OnRedirectToIdentityProvider* 함수 간에 매개 변수를 전달할 수 있습니다. 


## <a name="prepopulate-the-sign-in-name"></a>로그인 이름 미리 채우기

사용자가 로그인하는 동안, 앱은 특정 사용자를 대상으로 지정할 수 있습니다. 사용자를 대상으로 지정할 경우 애플리케이션은 권한 부여 요청에 `login_hint` 쿼리 매개 변수와 사용자 로그인 이름을 지정할 수 있습니다. Azure AD B2C가 자동으로 로그인 이름을 채우며, 사용자는 암호만 입력하면 됩니다. 

로그인 이름을 미리 입력하려면 다음 단계를 수행합니다.

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. 사용자 지정 정책을 사용하는 경우, [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. *OnRedirectToIdentityProvider* 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="redirect-sign-in-to-an-external-identity-provider"></a>외부 ID 공급자에게 로그인 리디렉션

Facebook, LinkedIn 또는 Google 등과 같은 소셜 계정을 포함하도록 애플리케이션에 대한 로그인 과정을 구성한 경우 `domain_hint` 매개 변수를 지정할 수 있습니다. 이 쿼리 매개 변수는 로그인에 사용해야 하는 소셜 ID 공급자에 대한 힌트를 Azure AD B2C에 제공합니다. 예를 들어, 애플리케이션이 `domain_hint=facebook.com`을 지정하는 경우, 로그인 흐름을 통해 Facebook 로그인 페이지로 직접 이동됩니다. 

로그인을 외부 ID 공급자로 리디렉션하려면 다음 단계를 수행합니다.

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. *OnRedirectToIdentityProviderFunc* 함수에서 *OnRedirectToIdentityProvider* 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="specify-the-ui-language"></a>UI 언어 지정

Azure AD B2C의 언어 사용자 지정을 사용하면 사용자 흐름 상에서 고객의 요구 사항에 맞게 다양한 언어를 수용할 수 있습니다. 자세한 내용은 [언어 사용자 지정](language-customization.md)을 참조하세요.

기본 설정 언어를 설정하려면 다음 단계를 수행합니다.

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. *OnRedirectToIdentityProvider* 함수에 다음 코드 줄을 추가합니다.

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="pass-a-custom-query-string-parameter"></a>사용자 지정 쿼리 문자열 매개 변수 전달

사용자 지정 정책을 사용하면 [페이지 콘텐츠를 동적으로 변경하려는 경우](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)와 같이 사용자 지정 쿼리 문자열 매개 변수를 전달할 수 있습니다.


사용자 지정 쿼리 문자열 매개 변수를 전달하려면 다음 단계를 수행합니다.

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. *OnRedirectToIdentityProvider* 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="pass-id-token-hint"></a>ID 토큰 힌트 전달

Azure AD B2C를 사용하면 신뢰 당사자 애플리케이션이 OAuth2 권한 부여 요청의 일부로 인바운드 JWT를 보낼 수 있습니다. JWT 토큰은 신뢰 당사자 애플리케이션 또는 ID 공급자가 발급할 수 있으며 사용자 또는 권한 부여 요청에 대한 힌트를 전달할 수 있습니다. Azure AD B2C는 서명, 발급자 이름 및 토큰 대상의 유효성을 검사하고 인바운드 토큰에서 클레임을 추출합니다.

인증 요청에 ID 토큰 힌트를 포함하려면 다음 단계를 수행합니다. 

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. *OnRedirectToIdentityProvider* 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>계정 컨트롤러

**로그인**, **등록**, **로그아웃** 작업을 사용자 지정하려는 경우, 사용자 고유의 컨트롤러를 만드는 것이 좋습니다. 사용자 고유의 컨트롤러를 사용하면 컨트롤러와 인증 라이브러리 간에 매개 변수를 전달할 수 있습니다. `AccountController`은 로그인 및 로그아웃 작업을 처리하는 `Microsoft.Identity.Web.UI` NuGet 패키지의 일부입니다. [Microsoft ID 웹 라이브러리](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)에서 해당 기능이 구현된 것을 확인하실 수 있습니다. 

다음 코드 조각에서는 **SignIn** 작업을 사용하는 사용자 지정 `MyAccountController`을 보여 줍니다. 해당 작업은 `campaign_id`이라는 매개 변수를 인증 라이브러리에 전달합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            properties.Items["campaign_id"] = "1234";
            return Challenge(properties, scheme);
        }

    }
}

```

`_LoginPartial.cshtml` 보기에서 컨트롤러에 대한 로그인 링크를 변경합니다

```
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

`Startup.cs` 호출의 `OnRedirectToIdentityProvider`에서 사용자 지정 매개 변수를 읽을 수 있습니다.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[ASP.NET Core 권한 부여](/aspnet/core/security/authorization/introduction)를 사용하면, [역할 기준 권한 부여](/aspnet/core/security/authorization/roles), [클레임 기반 권한 부여](/aspnet/core/security/authorization/claims), [정책 기반 권한 부여](/aspnet/core/security/authorization/policies)를 통해 사용자에게 보호된 리소스에 액세스할 수 있는 권한이 있는지 확인할 수 있습니다.

*ConfigureServices* 메서드에서 권한 부여 모델을 추가하는 *AddAuthorization* 메서드를 추가합니다. 다음 예시에서는 `EmployeeOnly`이라는 정책을 생성합니다. 해당 정책은 클레임 `EmployeeNumber`이 존재하는지 여부를 확인합니다. 클레임 값은 1, 2, 3, 4, 5와 같은 ID 중 하나여야 합니다.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core의 권한 부여는 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 및 다양한 매개 변수를 사용하여 제어됩니다. 가장 기본적인 형태에서, 컨트롤러, 작업, Razor 페이지에 `[Authorize]` 특성을 적용하면, 해당 구성 요소의 인증된 사용자에 대한 액세스를 제한합니다.

정책은 정책 이름으로 `[Authorize]` 특성을 사용하여 컨트롤러에 적용됩니다. 다음 코드는 `EmployeeOnly` 정책에 따라 권한이 부여된 사용자의 `Claims` 작업에 대한 액세스를 제한합니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>다음 단계

- 자세한 정보: [ASP.NET Core의 권한 부여 소개](/aspnet/core/security/authorization/introduction)
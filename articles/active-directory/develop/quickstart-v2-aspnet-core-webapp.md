---
title: Azure AD v2.0 ASP.NET Core 웹앱 빠른 시작 | Microsoft Docs
description: OpenID Connect를 사용하여 ASP.NET Core 웹앱에서 Microsoft 로그인을 구현하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: ba67acec778a48c084897095aa457e5637240a57
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227437"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱에 Microsoft로 로그인 추가

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

이 빠른 시작에는 ASP.NET Core 웹앱에서 Azure Active Directory 인스턴스로부터 개인(hotmail.com, live.com 등) 및 회사/학교 계정에 로그인하는 방법을 보여 주는 코드 샘플이 포함되어 있습니다.

![빠른 시작에서 생성된 샘플 앱의 작동 방식](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>응용 프로그램을 등록 및 빠른 시작 앱 다운로드
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>응용 프로그램 및 코드 샘플 등록 및 구성
> #### <a name="step-1-register-your-application"></a>1단계: 응용 프로그램 등록
> 
> 1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
> 1. 응용 프로그램에 대한 이름을 입력하고, **단계별 설정**에 대한 옵션이 선택 취소되었는지 확인한 다음, **만들기**를 클릭합니다.
> 1. `Add Platform`을 클릭한 다음, `Web`을 선택합니다.
> 1. **암시적 흐름 허용**이 *선택*되어 있는지 확인합니다.
> 1. **리디렉션 URL**에서 `https://localhost:3110/`을 입력합니다.
> 1. 페이지의 아래쪽으로 스크롤하여 **저장**을 클릭합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1단계: Azure Portal에서 응용 프로그램 구성
> 이 빠른 시작용 코드 샘플이 작동하려면 회신 URL을 `http://localhost:3110/`으로 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-core-webapp/green-check.png) 이 특성을 사용하여 응용 프로그램을 구성합니다.

#### <a name="step-2-download-your-aspnet-core-project"></a>2단계: ASP.NET Core 프로젝트 다운로드

- [ASP.NET Core 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3단계: 프로젝트 구성

1. zip 파일을 로컬 폴더(예: **C:\Azure-Samples**)에 추출합니다.
1. Visual Studio 2017을 사용하는 경우 Visual Studio에서 프로젝트를 엽니다(선택 사항).
1. **appsettings.json**을 편집하고, `ClientId` 값을 방금 등록한 응용 프로그램의 응용 프로그램 ID로 바꿉니다.

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. 응용 프로그램이 현재 디렉터리의 계정만을 대상으로 하는 *단일 테넌트 응용 프로그램*인 경우, **appsettings.json** 파일에서 `TenantId` 값을 찾고 `common`을 **테넌트 ID** 또는 **테넌트 이름**(예: contoso.microsoft.com)으로 바꿉니다. **개요 페이지**에서 테넌트 이름을 가져올 수 있습니다.

## <a name="more-information"></a>자세한 정보

이 섹션에서는 로그인 사용자에 필요한 코드에 대한 개요를 제공합니다. 이는 코드가 작동하는 방식과 기본 인수를 이해하려는 경우뿐만 아니라 기존 ASP.NET Core 응용 프로그램에 로그인을 추가하려는 경우에도 유용할 수 있습니다.

### <a name="startup-class"></a>시작 클래스

*Microsoft.AspNetCore.Authentication* 미들웨어는 호스팅 프로세스가 초기화될 때 실행되는 시작 클래스를 사용합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

`AddAuthentication` 메서드는 브라우저 시나리오에 사용되는 쿠키 기반 인증을 추가하고 인증 질문을 OpenIdConnect로 설정하도록 서비스를 구성합니다. 

`.AddAzureAd`가 포함된 줄은 Azure Active Directory 인증을 응용 프로그램에 추가합니다.

그 외에도 **AzureAdAuthenticationBuilderExtensions.cs** 파일은 확장 메서드를 AzureAd 인증 파이프라인에 추가합니다. 이 확장 메서드는 Azure AD 인증에 필요한 특성을 구성합니다. `IConfigureNamedOptions` 인터페이스의 `Configure` 메서드에 포함되는 항목은 다음과 같습니다.

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |clientid     |Azure Portal에 등록된 응용 프로그램의 응용 프로그램 ID|
> |Authority | 사용자가 인증할 STS 엔드포인트입니다. 일반적으로 공용 클라우드의 경우 https://login.microsoftonline.com/{tenant}/v2.0입니다. 여기서 {tenant}는 테넌트 이름, 테넌트 ID, 또는 공통 엔드포인트(다중 테넌트 응용 프로그램에 사용)에 대한 참조인 경우 *common*입니다.|
> |UseTokenLifetime |인증 쿠키가 인증 토큰의 쿠키와 일치해야 함을 나타냅니다.|
> |RequireHttpsMetadata     |메타데이터 주소 또는 인증 기관에 대한 HTTPS가 필요합니다. 이 값은 `True`로 변경하는 것이 좋습니다.|
> |TokenValidationParameters     | 토큰 유효성 검사에 대한 매개 변수 목록입니다. 이 경우 `ValidateIssuer`는 개인, 회사 또는 학교 계정의 로그인을 수락할 수 있음을 나타내기 위해 `false`로 설정됩니다.|

### <a name="initiate-an-authentication-challenge"></a>인증 질문 시작

**AccountController.cs**처럼 컨트롤러에서 인증 질문을 요청하여 사용자가 로그인하도록 강제할 수 있습니다.

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> 위의 메서드를 사용하여 인증 질문을 요청하는 것은 선택 사항이며, 일반적으로 인증된 사용자와 인증되지 않은 사용자 모두에 대해 보기를 사용할 수 있도록 하려는 경우에 사용됩니다. 다음 섹션에서 설명하는 메서드를 사용하여 컨트롤러를 보호할 수 있습니다.

### <a name="protect-a-controller-or-a-controllers-method"></a>컨트롤러 또는 컨트롤러 메서드 보호

`[Authorize]` 특성을 사용하여 컨트롤러 또는 컨트롤러 메서드를 보호할 수 있습니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러에 대한 액세스를 제한합니다. 즉, 사용자가 인증되지 않은 경우 인증 질문을 시작하여 컨트롤러에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

완전히 새로운 ASP.NET Core 웹 응용 프로그램에 인증을 추가하는 방법에 대한 지침을 포함하여 자세한 내용은 이 ASP.NET Core 빠른 시작에 대한 GitHub 리포지토리를 확인합니다.

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 코드 샘플](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
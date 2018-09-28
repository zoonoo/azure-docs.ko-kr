---
title: Azure AD v2.0 ASP.NET 웹 서버 빠른 시작 | Microsoft Docs
description: OpenID Connect를 사용하여 ASP.NET 웹 앱에서 Microsoft 로그인을 구현하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7196cd6c18680583494a485c136081d90960464d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991286"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>빠른 시작: ASP.NET 웹 앱에 Microsoft 로그인 추가

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

이 빠른 시작에서는 ASP.NET 웹 앱이 모든 Azure Active Directory(Azure AD) 인스턴스에서 개인 계정(hotmail.com, outlook.com, 기타)과 회사 및 학교 계정에 로그인하는 방법을 배웁니다.

![빠른 시작에서 생성된 샘플 앱의 작동 방식](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>응용 프로그램 등록 및 빠른 시작 앱 다운로드
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>응용 프로그램 및 코드 샘플 등록 및 구성
> #### <a name="step-1-register-your-application"></a>1단계: 응용 프로그램 등록
> 
> 1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
> 1. 응용 프로그램에 이름을 입력하고 **안내 설정**의 옵션이 선택 취소되었는지 확인 후 **만들기**를 클릭합니다.
> 1. `Add Platform`을 클릭한 다음, `Web`을 선택합니다.
> 1. **암시적 흐름 허용**이 *표시된* 채로 둡니다.
> 1. **리디렉션 URL**에 `https://localhost:44368/`를 입력합니다.
> 1. 페이지 아래로 스크롤하여 **저장**을 클릭합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1단계: Azure Portal에서 응용 프로그램 구성
> 이 빠른 시작용 코드 샘플을 작동하려면 `https://localhost:44368/`로서 회신 URL을 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성된](media/quickstart-v2-aspnet-webapp/green-check.png) 응용 프로그램이 이 특성을 사용하여 구성됩니다.

#### <a name="step-2-download-your-project"></a>2단계: 프로젝트 다운로드

[Visual Studio 2017 솔루션 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3단계: Visual Studio 프로젝트 구성

1. 로컬 폴더(예를 들어 **C:\Azure-Samples**)에 Zip 파일 추출하기
1. Visual Studio(AppModelv2-WebApp-OpenIDConnect-DotNet.sln)에서 솔루션 열기
1. **Web.config**를 편집하고 방금 등록한 응용 프로그램의 응용 프로그램 ID로 `Enter_the_Application_Id_here` 바꾸기

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    ```
    
> [!div class="sxs-lookup" renderon="portal"]
> [!IMPORTANT]
> 응용 프로그램이 **Web.config** 파일에서 이 디렉터리 계정만을 대상으로 하는 *단일 테넌트 응용 프로그램*이라면, `Tenant`의 값을 찾아 `common`을 **테넌트 ID**나 **테넌트 이름**(예를 들어 contoso.microsoft.com)으로 바꿉니다. **개요 페이지**에서 테넌트 이름을 가져올 수 있습니다.

## <a name="more-information"></a>자세한 정보

이 섹션에서는 로그인 사용자에게 필요한 코드 개요를 제공합니다. 이는 작동 방식과 기본 인수를 이해하고 기존 ASP.NET 응용 프로그램에 로그인을 추가하려는 경우에도 유용합니다.

### <a name="owin-middleware-nuget-packages"></a>OWIN 미들웨어 NuGet 패키지

OWIN 미들웨어 패키지로 ASP.NET의 OpenID Connect를 사용하는 쿠키 기반 인증을 가지고 인증 파이프라인을 설정할 수 있습니다. Visual Studio의 **패키지 관리자 콘솔**에서 다음 명령을 실행하여 이 패키지를 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN 시작 클래스

OWIN 미들웨어는 호스팅 프로세스를 초기화하는 경우 실행되는 *시작 클래스*(빠른 시작의 경우, 루트 폴더의 *startup.cs* 파일)를 사용합니다. 다음 코드는 빠른 시작에서 사용하는 매개 변수를 보여줍니다.

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  |  |
> |---------|---------|
> | `ClientId`     | Azure Portal에 등록된 응용 프로그램의 응용 프로그램 ID |
> | `Authority`    | 사용자가 인증하는 STS 엔드포인트 일반적으로 공용 클라우드의 경우 https://login.microsoftonline.com/{tenant}/v2.0입니다. {tenant}는 테넌트 이름, 테넌트 ID, 또는 공통 엔드포인트(다중 테넌트 응용 프로그램에 사용)에 대한 참조인 경우 *common*입니다. |
> | `RedirectUri`  | Azure AD v2.0 엔드포인트에 대한 인증 후 사용자가 전송되는 URL |
> | `PostLogoutRedirectUri`     | 서명 해제 후 사용자가 전송되는 URL |
> | `Scope`     | 요청되는 범위 목록이며 공백으로 구분 |
> | `ResponseType`     | 인증에서의 응답이 ID 토큰을 포함하는 요청 |
> | `TokenValidationParameters`     | 토큰 유효성 검사에 대한 매개 변수 목록. 이 경우, `ValidateIssuer`는 `false`로 설정되어 모든 개인이나 회사 또는 학교 계정 형식에서 로그인을 사용할 수 있음을 나타냄 |
> | `Notifications`     | 여러 *OpenIdConnect* 메시지에서 실행할 수 있는 대리자 목록 |

### <a name="initiate-an-authentication-challenge"></a>인증 질문 시작하기

컨트롤러에서 다른 사용자에게 인증 질문을 요청하여 로그인하게 할 수 있습니다.

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> 위의 메서드를 사용하여 인증 질문을 요청하는 것은 선택 사항이며 일반적으로 보기에서 인증되거나 인증되지 않은 모든 사용자가 액세스할 수 있도록 하려는 경우 사용됩니다. 또는 다음 섹션에 설명된 메서드를 사용하여 컨트롤러를 보호할 수 있습니다.

### <a name="protect-a-controller-or-a-controllers-method"></a>컨트롤러 또는 컨트롤러 메서드 보호하기

`[Authorize]` 특성을 사용하여 컨트롤러나 컨트롤러 작업을 보호할 수 있습니다. 이 특성은 컨트롤러나 작업에 액세스를 제한하여, *인증되지 않은* 사용자가 `[Authorize]` 특성으로 데코레이팅된 작업이나 컨트롤러 중 하나에 액세스하려고 할 때 인증 질문이 자동으로 발생하여, 인증된 사용자만 컨트롤러의 작업에 액세스하게 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작의 전체 설명이 포함된 응용 프로그램 및 새로운 기능 구축의 완전한 단계별 가이드를 위한 ASP.NET 자습서를 사용해 보세요.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>빠른 시작에 사용되는 응용 프로그램을 만드는 단계 알아보기

> [!div class="nextstepaction"]
> [로그인 자습서](.\tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
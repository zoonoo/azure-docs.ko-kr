---
title: Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 Web API를 호출하는 웹 응용 프로그램을 빌드하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C: .NET 웹앱에서 Web API 호출
Azure AD(Azure Active Directory) B2C를 사용하여 몇 가지 간단한 단계로 강력한 셀프 서비스 ID 관리 기능을 웹앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 전달자 토큰을 사용하여 Web API를 호출하는 .NET MVC(모델-뷰-컨트롤러) "할 일 모음" 웹앱을 만드는 방법을 보여 줍니다.

이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작하여 Azure AD B2C의 기본 사항에 대해 알아보아야 합니다.

## Azure AD B2C 디렉터리 가져오기
Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

## 응용 프로그램 만들기
다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 웹앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

* 응용 프로그램에서 **웹앱/웹 API**를 포함합니다.
* **회신 URL**로 `https://localhost:44316/`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
* 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 ID는 나중에도 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 정책 만들기
Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 웹앱은 등록, 로그인 및 편집 프로필 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

* 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
* 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작합니다.

## 코드 다운로드
이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). 진행하면서 샘플을 작성하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

완성된 앱도 [.zip 파일로 가능](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)하거나 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다.

## 작업 웹앱 구성
`TaskWebApp`이 Azure AD B2C와 통신하도록 하려면 몇 가지 공통 매개 변수를 제공해야 합니다. `TaskWebApp` 프로젝트에서 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다. `AadInstance`, `RedirectUri`, 및 `TaskServiceUrl` 값을 있는 그대로 둘 수 있습니다.

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" /> <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" /> <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="b2c\_1_sign\_up" /> <add key="ida:SignInPolicyId" value="b2c_1_sign\_in" /> <add key="ida:UserProfilePolicyId" value="b2c_1\_edit\_profile" /> <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" /> </appSettings>

## 액세스 토큰을 가져오고 작업 API를 호출합니다.
이 섹션에서는 또한 Azure AD B2C를 사용하여 보안되는 웹 API에 액세스하기 위해 Azure AD B2C를 사용하여 로그인 중에 받은 토큰을 사용하는 방법을 설명합니다.

이 문서에서는 API의 보안을 유지하는 방법에 대한 세부 정보를 다루지 않습니다. Web API가 Azure AD B2C를 사용하여 요청을 안전하게 인증하는 방법을 알아보려면 [Web API 시작 문서](active-directory-b2c-devquickstarts-api-dotnet.md)를 확인하세요.

### 로그인 토큰 저장
먼저, (정책 중 하나를 사용하여) 사용자를 인증하고 Azure AD B2C에서 로그인 토큰을 받습니다. 정책을 실행하는 방법을 잘 모를 경우, 돌아가 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작하여 Azure AD B2C의 기본 사항에 대해 알아보아야 합니다.

`App_Start\Startup.Auth.cs` 파일을 엽니다. `OpenIdConnectAuthenticationOptions`에 반드시 수행해야 할 중요한 변경이 있습니다 - `SaveSignInToken = true`을 설정해야 합니다.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### 컨트롤러에서 토큰 가져오기
`TasksController`은 HTTP 요청을 API로 보내 작업을 읽고 만들고 삭제하도록 웹 API와 통신을 담당합니다. API는 Azure AD B2C에 의해 보안이 되므로, 먼저 위의 단계에서 저장한 토큰을 검색해야 합니다.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

`BootstrapContext`은 B2C 정책 중 하나를 실행하여 획득한 로그인 토큰을 포함합니다.

### Web API로부터 작업 읽기
이제 토큰이 있으므로 `Authorization` 헤더에서 HTTP `GET` 요청에 토큰을 연결하여 `TaskService`를 안전하게 호출할 수 있습니다.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### Web API에서 작업 만들기 및 삭제
로그인 토큰을 검색하기 위해 `POST` 및 `DELETE` 요청을 `BootstrapContext` 토큰을 사용하여 웹 API에 보낼 때 동일한 패턴을 따릅니다. 만들기 작업은 구현되어 있습니다. `TasksController.cs`에서 삭제 작업 완료를 시도해 볼 수 있습니다.

## 샘플 앱 실행
마지막으로 앱을 빌드하고 실행합니다. 등록하고 로그인하여, 로그인된 사용자에 대한 작업을 만듭니다. 로그아웃했다가 다른 사용자로 로그인합니다. 해당 사용자에 대한 작업을 만듭니다. API 가 받는 토큰에서 사용자의 ID를 추출하므로 API에 사용자별 작업이 저장됩니다.

참조를 위해 완료된 샘플은 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->
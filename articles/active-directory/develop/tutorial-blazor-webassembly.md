---
title: 자습서 - Blazor WebAssembly 앱에서 사용자를 로그인하고 보호된 API 호출
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 Microsoft ID 플랫폼을 사용하여 Blazor WebAssembly 앱에서 사용자를 로그인하고 보호된 API를 호출합니다.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: ba3607c522191644ec0cc63db118de285d297c48
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221513"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>자습서: Blazor WebAssembly 앱에서 사용자를 로그인하고 보호된 API 호출

[Blazor WebAssembly](/aspnet/core/blazor#blazor-webassembly)는 .NET으로 대화형 클라이언트 쪽 웹앱을 빌드할 수 있는 단일 페이지 앱 프레임워크입니다. 이 자습서에서는 [Microsoft ID 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/)을 사용하여 Blazor WebAssembly(Blazor WASM) 앱에서 사용자를 로그인하고 보호된 API의 데이터를 검색하는 앱을 만듭니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> * Microsoft ID 플랫폼을 사용하여 [인증 및 권한 부여](authentication-vs-authorization.md)에 Azure AD(Azure Active Directory)를 사용하도록 구성된 새 Blazor WebAssembly 앱 만들기
> * 보호된 웹 API(여기서는 [Microsoft Graph](https://docs.microsoft.com/graph/overview))에서 데이터 검색

## <a name="prerequisites"></a>필수 구성 요소

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* 앱을 등록할 수 있는 Azure AD 테넌트. Azure AD 테넌트에 대한 액세스 권한이 없는 경우 [Microsoft 365 개발자 프로그램](https://developer.microsoft.com/microsoft-365/dev-program)에 등록하거나 [Azure 체험 계정](https://azure.microsoft.com/free)을 만들어서 얻을 수 있습니다.

## <a name="register-the-app-in-the-azure-portal"></a>Azure Portal에서 앱 등록

인증을 위해 Azure AD(Azure Active Directory)를 사용하는 모든 앱은 Azure AD에 등록해야 합니다. 다음과 같은 사양이 포함된 [애플리케이션 등록](quickstart-register-app.md)의 지침을 따르세요.

- **지원되는 계정 유형** 의 경우 **이 조직 디렉터리 계정의 계정만** 을 선택합니다.
- **리디렉션 URI** 드롭다운 세트를 **웹** 으로 두고 `https://localhost:5001/authentication/login-callback`를 입력합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 다른 포트에서 앱을 사용할 수 있는 경우 `5001` 대신 해당 포트 번호를 지정합니다.

등록되었으면 **인증** > **암시적 부여** 에서 **액세스 토큰** 및 **ID 토큰** 에 대한 확인란을 선택한 다음, **저장** 단추를 선택합니다.

## <a name="create-the-app-using-the-net-core-cli"></a>.NET Core CLI를 사용하여 앱 만들기

앱을 만들려면 최신 Blazor 템플릿이 필요합니다. 다음 명령을 사용하여 .NET Core CLI용 템플릿을 설치할 수 있습니다.

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

그 후 다음 명령을 실행하여 애플리케이션을 만듭니다. 명령의 자리 표시자를 앱의 개요 페이지에 있는 적절한 정보로 바꾸고 명령 셸에서 명령을 실행합니다. `-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| 자리표시자   | Azure Portal 이름       | 예제                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | 애플리케이션(클라이언트) ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 디렉터리(테넌트) ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>앱 테스트

이제 앱을 빌드 및 실행할 수 있습니다. 이 템플릿 앱을 실행할 때 --framework를 사용하여 실행할 프레임워크를 지정해야 합니다. 이 자습서에서는 .NET Standard 2.1을 사용하지만, 이 템플릿은 다른 프레임워크도 지원합니다.

```dotnetcli
dotnet run --framework netstandard2.1
```

브라우저에서 `https://localhost:5001`로 이동하고, Azure AD 사용자 계정으로 로그인하여 앱이 실행 중이며 Microsoft ID 플랫폼을 사용하여 사용자를 로그인하는 것을 살펴봅니다.

Microsoft ID 플랫폼을 사용하여 Azure AD에 로그인을 사용하도록 설정하는 이 템플릿의 구성 요소에 대한 내용은 [이 토픽의 ASP.NET 문서](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package)에 설명되어 있습니다.

## <a name="retrieving-data-from-microsoft-graph"></a>Microsoft Graph에서 데이터 검색

[Microsoft Graph](/graph/overview)는 테넌트에 속한 사용자의 Microsoft 365 데이터에 액세스할 수 있는 다양한 API를 제공합니다. Microsoft Graph는 Microsoft ID 플랫폼에서 발급한 토큰을 직접 지원하므로, Microsoft ID 플랫폼을 앱에 대한 ID 공급자로 사용하면 이 정보에 손쉽게 액세스할 수 있습니다. 이 섹션에서 코드를 추가하면 로그인한 사용자의 이메일이 애플리케이션의 "데이터 페치" 페이지에 표시됩니다.

시작하기 전에 필요한 권한을 변경하고 현재 토큰은 작동하지 않으므로 앱에서 로그아웃합니다. 아직 로그아웃하지 않은 경우 다음 코드를 업데이트하기 전에 앱을 다시 실행하고 **로그아웃** 을 선택합니다.

이제 앱의 등록과 코드를 업데이트하여 사용자의 이메일을 끌어오고 앱 내에 메시지를 표시합니다.

앱이 사용자의 이메일에 대한 액세스 권한을 요청할 때 Azure AD에서 그것을 인식할 수 있도록, 먼저 앱 등록에 `Mail.Read` API 권한을 추가합니다.

1. Azure Portal의 **앱 등록** 에서 앱을 선택합니다.
1. **관리** 아래에서 **API 권한** 을 선택합니다.
1. **권한 추가** > **Microsoft Graph** 를 선택합니다.
1. **위임된 권한** 을 선택한 다음, **Mail.Read** 사용 권한을 검색하여 선택합니다.
1. **권한 추가** 를 선택합니다.

그리고 netstandard2.1 **ItemGroup** 에 있는 프로젝트의 *.csproj* 파일에 다음을 추가합니다. 이렇게 하면 다음 단계에서 사용자 지정 HttpClient를 만들 수 있습니다.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

그 후 다음 단계에서 지정한 대로 코드를 수정합니다. 이렇게 변경하면 Microsoft Graph API로 전송된 나가는 요청에 [액세스 토큰](access-tokens.md)이 추가됩니다. 이 패턴은 [ASP.NET Core Blazor WebAssembly 추가 보안 시나리오](/aspnet/core/blazor/security/webassembly/additional-scenarios)에서 자세히 다룹니다.

먼저 다음 코드를 사용하여 *GraphAuthorizationMessageHandler.cs* 라는 새 파일을 만듭니다. 이 처리기는 Microsoft Graph API로 전송되는 나가는 요청에 `User.Read` 및 `Mail.Read` 범위에 대한 액세스 토큰을 추가하는 데 사용됩니다.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

그런 다음, *Program.cs* 에 들어 있는 `Main` 메서드의 내용을 다음 코드로 바꿉니다. 이 코드는 새 `GraphAPIAuthorizationMessageHandler`를 사용하고, 사용자가 처음으로 로그인할 때 앱에서 요청하는 기본 범위로 `User.Read` 및 `Mail.Read`를 추가합니다.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

마지막으로 *FetchData.razor* 페이지의 내용을 다음 코드로 바꿉니다. 이 코드는 Microsoft Graph API의 사용자 이메일 데이터를 가져와서 목록으로 표시합니다. `OnInitializedAsync`에서, 적절한 액세스 토큰을 사용하는 새 `HttpClient`가 생성되어 Microsoft Graph API에 대한 요청을 수행하는 데 사용됩니다.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

이제 앱을 다시 시작합니다. 메일을 읽을 수 있도록 앱에 액세스 권한을 부여하라는 메시지가 표시됩니다. 이 동작은 앱이 `Mail.Read` 범위를 요청할 때 발생합니다.

동의를 부여한 후 “데이터 페치” 페이지로 이동하여 일부 이메일을 읽습니다.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="최종 앱 스크린샷 Hello Nicholas라는 제목이 있고, Nicholas에 속한 이메일의 목록이 표시됩니다.":::

## <a name="next-steps"></a>다음 단계

- [Microsoft ID 플랫폼 모범 사례 및 권장 사항](./identity-platform-integration-checklist.md)
- [ASP.NET Core Blazor 소개](/aspnet/core/blazor)

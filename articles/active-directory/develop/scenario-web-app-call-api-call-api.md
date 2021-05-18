---
title: 웹앱에서 Web API 호출 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹앱(보호된 웹 API 호출)을 빌드하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753288"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>웹 API를 호출하는 웹앱: 웹 API 호출

이제 토큰이 있으므로 보호된 웹 API를 호출할 수 있습니다. 일반적으로 웹앱의 컨트롤러나 페이지에서 다운스트림 API를 호출합니다.

## <a name="call-a-protected-web-api"></a>보호된 웹 API 호출

보호된 웹 API를 호출하는 과정은 선택한 언어 및 프레임워크에 따라 다릅니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* 을 사용하는 경우 API를 호출하기 위한 세 가지 사용 옵션이 있습니다.

- [옵션 1: Microsoft Graph SDK를 사용하여 Microsoft Graph 호출](#option-1-call-microsoft-graph-with-the-sdk)
- [옵션 2: helper 클래스를 사용하여 다운스트림 웹 API 호출](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [옵션 3: helper 클래스 없이 다운스트림 웹 API 호출](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>옵션 1: SDK를 사용하여 Microsoft Graph 호출

Microsoft Graph를 호출하려고 합니다. 이 시나리오에서는 [코드 구성](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)에 지정된 대로 *Startup.cs* 에 `AddMicrosoftGraph`를 추가했으며, 작업에 사용할 컨트롤러 또는 페이지 생성자에 `GraphServiceClient`를 직접 삽입할 수 있습니다. 다음 Razor 페이지 예에서는 로그인한 사용자의 사진이 표시됩니다.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>옵션 2: helper 클래스를 사용하여 다운스트림 웹 API 호출

Microsoft Graph가 아닌 웹 API를 호출하려고 합니다. 이 경우에는 [코드 구성](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)에 지정된 대로 *Startup.cs* 에 `AddDownstreamWebApi`를 추가했으며 컨트롤러 또는 페이지 생성자에 `IDownstreamWebApi` 서비스를 직접 삽입하고 작업에 사용할 수 있습니다.

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync`에는 직접 개체를 받을 수 있도록 하는 강력한 형식의 일반적인 재정의가 있습니다. 예를 들어 다음 메서드는 웹 API에서 반환된 JSON의 강력한 형식의 표현인 `Todo` 인스턴스를 수신합니다.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>옵션 3: helper 클래스 없이 다운스트림 웹 API 호출

`ITokenAcquisition` 서비스를 사용하여 토큰을 수동으로 획득하기로 결정했으므로 이제 토큰을 사용해야 합니다. 이 경우, 다음 코드는 계속해서 [웹 API를 호출하는 웹앱: 앱에 대한 토큰 획득](scenario-web-app-call-api-acquire-token.md)에 표시된 코드 예입니다. 이 코드는 웹앱 컨트롤러의 작업에서 호출됩니다.

토큰을 획득한 후에는 전달자 토큰으로 사용하여 다운스트림 API를 호출합니다. 이 경우에는 Microsoft Graph입니다.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> 동일한 원칙을 사용하여 웹 API를 호출할 수 있습니다.
>
> 대부분의 Azure 웹 API는 Microsoft Graph의 경우처럼 API 호출을 간소화하는 SDK를 제공합니다. 예를 들어 Microsoft.Identity.Web과 Azure Storage SDK를 사용하는 웹앱의 예를 보려면 [Azure AD를 사용하여 Blob 스토리지에 대한 액세스 권한을 부여하는 웹 애플리케이션 만들기](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [프로덕션으로 이동](scenario-web-app-call-api-production.md)합니다.
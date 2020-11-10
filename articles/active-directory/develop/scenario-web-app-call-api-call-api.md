---
title: 웹 앱에서 web api 호출 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법 알아보기 (보호 된 웹 API 호출)
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
ms.openlocfilehash: 846c47017ba2887c287dd080c44c46b372660d0e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443605"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>웹 api를 호출 하는 웹 앱: web API 호출

이제 토큰이 있으므로 보호 된 web API를 호출할 수 있습니다. 일반적으로 웹 앱의 컨트롤러나 페이지에서 다운스트림 API를 호출 합니다.

## <a name="call-a-protected-web-api"></a>보호 된 웹 API 호출

보호 된 웹 API를 호출 하는 것은 선택한 언어 및 프레임 워크에 따라 다릅니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity* 를 사용 하는 경우 API를 호출 하기 위한 세 가지 사용 옵션이 있습니다.

- [옵션 1: Microsoft Graph SDK를 사용 하 여 Microsoft Graph 호출](#option-1-call-microsoft-graph-with-the-sdk)
- [옵션 2: 도우미 클래스를 사용 하 여 다운스트림 웹 API 호출](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [옵션 3: 도우미 클래스 없이 다운스트림 웹 API 호출](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>옵션 1: SDK를 사용 하 여 Microsoft Graph 호출

Microsoft Graph를 호출 하려고 합니다. 이 시나리오에서는 `AddMicrosoftGraph` [코드 구성](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)에 지정 된 대로 *Startup.cs* 에를 추가 했으며, `GraphServiceClient` 작업에 사용할 컨트롤러 또는 페이지 생성자에를 직접 삽입할 수 있습니다. 다음 예제 Razor 페이지는 로그인 한 사용자의 사진을 표시 합니다.

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

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>옵션 2: 도우미 클래스를 사용 하 여 다운스트림 웹 API 호출

Microsoft Graph 아닌 웹 API를 호출 하려고 합니다. 이 경우 `AddDownstreamWebApi` [코드 구성](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)에 지정 된 대로 *Startup.cs* 에를 추가 하 고 `IDownstreamWebApi` 컨트롤러 또는 페이지 생성자에서 서비스를 직접 삽입 하 고 작업에 사용할 수 있습니다.

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

에는 직접 개체를 받을 수 있도록 하 `CallWebApiForUserAsync` 는 강력한 형식의 제네릭 재정의가 있습니다. 예를 들어 다음 메서드는 `Todo` 웹 API에서 반환 된 JSON의 강력한 형식의 표현인 인스턴스를 수신 합니다.

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

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>옵션 3: 도우미 클래스 없이 다운스트림 웹 API 호출

서비스를 사용 하 여 토큰을 수동으로 획득 하기로 결정 했으므로 `ITokenAcquisition` 이제 토큰을 사용 해야 합니다. 이 경우 다음 코드는 웹 Api를 호출 하는 웹 앱에 표시 된 예제 코드를 계속 합니다 [. 앱에 대 한 토큰을 가져옵니다](scenario-web-app-call-api-acquire-token.md). 코드는 웹 앱 컨트롤러의 작업에서 호출 됩니다.

토큰을 가져온 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다 .이 경우에는 Microsoft Graph 합니다.

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
> 동일한 원칙을 사용 하 여 web API를 호출할 수 있습니다.
>
> 대부분의 Azure web Api는 Microsoft Graph의 경우 처럼 API 호출을 간소화 하는 SDK를 제공 합니다. 예를 들어, Azure AD를 사용 하 여 [Blob 저장소에 대 한 액세스 권한을 부여 하는 웹 응용 프로그램을 만들어](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=dotnet) Azure Storage SDK를 사용 하는 웹 앱의 예제를 참조 하세요.

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

이 시나리오에서 다음 문서로 이동 하 여 [프로덕션으로 이동](scenario-web-app-call-api-production.md)합니다.

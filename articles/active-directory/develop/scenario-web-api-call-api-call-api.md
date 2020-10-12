---
title: 웹 Api를 호출 하는 web API-Microsoft id 플랫폼 | Microsoft
description: Web Api를 호출 하는 web API를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9212e99ae317a3abec4bebfc7fb131c6774f8e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396198"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>웹 api를 호출 하는 web API: API 호출

토큰이 있으면 보호 된 웹 API를 호출할 수 있습니다. 일반적으로 웹 API의 컨트롤러나 페이지에서 다운스트림 Api를 호출 합니다.

## <a name="controller-code"></a>컨트롤러 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web*을 사용 하면 세 가지 사용 시나리오가 있습니다.

- [옵션 1: Microsoft Graph SDK를 사용 하 여 Microsoft Graph 호출](#option-1-call-microsoft-graph-with-the-sdk)
- [옵션 2: 도우미 클래스를 사용 하 여 다운스트림 웹 API 호출](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [옵션 3: 도우미 클래스 없이 다운스트림 웹 API 호출](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>옵션 1: SDK를 사용 하 여 Microsoft Graph 호출

이 시나리오에서는 `.AddMicrosoftGraph()` [코드 구성](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)에 지정 된 대로 *Startup.cs* 에를 추가 했으며, `GraphServiceClient` 작업에 사용할 컨트롤러 또는 페이지 생성자에를 직접 삽입할 수 있습니다. 다음 예제 Razor 페이지는 로그인 한 사용자의 사진을 표시 합니다.

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

이 시나리오에서는 `.AddDownstreamWebApi()` [코드 구성](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)에 지정 된 대로 *Startup.cs* 에를 추가 했으며, `IDownstreamWebApi` 컨트롤러 또는 페이지 생성자에서 서비스를 직접 삽입 하 고 작업에 사용할 수 있습니다.

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
```

`CallWebApiForUserAsync`또한 메서드는 개체를 직접 받을 수 있도록 강력한 형식의 제네릭 재정의를 포함 합니다. 예를 들어, 다음 메서드는 `Todo` 웹 API에서 반환 된 JSON의 강력한 형식의 표현인 인스턴스를 수신 했습니다.

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

서비스를 사용 하 여 토큰을 수동으로 획득 하기로 결정 `ITokenAcquisition` 한 경우 이제 토큰을 사용 해야 합니다. 이 경우 다음 코드는 웹 api를 호출 하는 web API에 표시 된 예제 코드를 계속 합니다 [. 앱에 대 한 토큰을 가져옵니다](scenario-web-api-call-api-acquire-token.md). 코드는 API 컨트롤러의 작업에서 호출 됩니다. *Todolist*이라는 다운스트림 API를 호출 합니다.

 토큰을 가져온 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

다음 코드는 web Api를 호출 하는 web API에 표시 되는 예제 코드를 계속 합니다 [. 앱에 대 한 토큰을 가져옵니다](scenario-web-api-call-api-acquire-token.md). 코드는 API 컨트롤러의 작업에서 호출 됩니다. 다운스트림 API MS Graph를 호출 합니다.

토큰을 가져온 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
MSAL Python을 사용 하 여이 흐름을 보여 주는 샘플은 아직 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 프로덕션으로 이동](scenario-web-api-call-api-production.md)

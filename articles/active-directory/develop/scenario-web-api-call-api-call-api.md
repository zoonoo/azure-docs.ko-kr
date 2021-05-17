---
title: 웹 API를 호출하는 웹 API | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹 API를 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: d4526446b657b46f97306f51b2db325feefc8eff
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166490"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>웹 API를 호출하는 웹 API: API 호출

토큰이 있으면 보호된 웹 API를 호출할 수 있습니다. 일반적으로 웹 API의 컨트롤러나 페이지에서 다운스트림 API를 호출합니다.

## <a name="controller-code"></a>컨트롤러 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* 을 사용하면 세 가지 사용 시나리오가 제공됩니다.

- [옵션 1: SDK를 사용하여 Microsoft Graph 호출](#option-1-call-microsoft-graph-with-the-sdk)
- [옵션 2: 도우미 클래스를 사용하여 다운스트림 웹 API 호출](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [옵션 3: 도우미 클래스 없이 다운스트림 웹 API 호출](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>옵션 1: SDK를 사용하여 Microsoft Graph 호출

이 시나리오에서는 [코드 구성](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)에 지정된 대로 *Startup.cs* 에 `.AddMicrosoftGraph()`를 추가했으며 작업에 사용할 컨트롤러 또는 페이지 생성자에 `GraphServiceClient`를 직접 삽입할 수 있습니다. 다음 Razor 페이지 예에서는 로그인한 사용자의 사진이 표시됩니다.

```csharp
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

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>옵션 2: 도우미 클래스를 사용하여 다운스트림 웹 API 호출

이 시나리오에서는 [코드 구성](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)에 지정된 대로 *Startup.cs* 에 `.AddDownstreamWebApi()`를 추가했으며 컨트롤러 또는 페이지 생성자에 `IDownstreamWebApi` 서비스를 직접 삽입하고 작업에 사용할 수 있습니다.

```csharp
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

`CallWebApiForUserAsync` 메서드에는 직접 개체를 받을 수 있도록 하는 강력한 형식의 일반적인 재정의가 있습니다. 예를 들어 다음 메서드는 웹 API에서 반환된 JSON의 강력한 형식의 표현인 `Todo` 인스턴스를 수신합니다.

```csharp
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

`ITokenAcquisition` 서비스를 사용하여 토큰을 수동으로 획득하기로 결정한 경우에는 이제 토큰을 사용해야 합니다. 이 경우, 다음 코드는 계속해서 [웹 API를 호출하는 웹 API: 앱에 대한 토큰 획득](scenario-web-api-call-api-acquire-token.md)에 표시된 코드 예입니다. 코드는 API 컨트롤러의 작업에서 호출됩니다. *todolist* 라는 다운스트림 API를 호출합니다.

 토큰을 가져온 후에는 이 토큰을 전달자 토큰으로 사용하여 다운스트림 API를 호출합니다.

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

다음 코드는 계속해서 [웹 API를 호출하는 웹 API: 앱에 대한 토큰 획득](scenario-web-api-call-api-acquire-token.md)에 표시된 코드 예입니다. 코드는 API 컨트롤러의 작업에서 호출됩니다. 다운스트림 API MS Graph를 호출합니다.

토큰을 가져온 후에는 이 토큰을 전달자 토큰으로 사용하여 다운스트림 API를 호출합니다.

```java
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

MSAL Python을 사용하여 이 흐름을 보여 주는 샘플은 [ms-identity-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of)에서 사용할 수 있습니다.

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [프로덕션으로 이동](scenario-web-api-call-api-production.md)으로 이동합니다.

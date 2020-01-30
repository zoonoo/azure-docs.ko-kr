---
title: 범위 및 앱 역할의 보호 된 Web API 확인 | Microsoft
titleSuffix: Microsoft identity platform
description: 보호 된 웹 API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768123"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>보호 된 웹 API: 범위 및 앱 역할 확인

이 문서에서는 web API에 권한 부여를 추가할 수 있는 방법을 설명 합니다. 이 보호는 API가에 의해서만 호출 되도록 합니다.

- 올바른 범위를 가진 사용자를 대신 하는 응용 프로그램
- 올바른 응용 프로그램 역할이 있는 디먼 앱입니다.

> [!NOTE]
> 이 문서의 코드 조각은 완전히 작동 하는 다음 샘플에서 추출 됩니다.
>
> - GitHub의 [ASP.NET Core WEB API 증분 자습서](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

ASP.NET 또는 ASP.NET Core web API를 보호 하려면 `[Authorize]` 특성을 다음 항목 중 하나에 추가 해야 합니다.

- 모든 컨트롤러 작업을 보호 하려는 경우 컨트롤러 자체
- API에 대 한 개별 컨트롤러 작업

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

그러나이 보호는 충분 하지 않습니다. ASP.NET 및 ASP.NET Core 토큰의 유효성을 검사 하도록 보장 합니다. Api는 API를 호출 하는 데 사용 되는 토큰이 필요한 클레임으로 요청 되는지 확인 해야 합니다. 특히 다음과 같은 클레임을 확인 해야 합니다.

- 사용자를 대신 하 여 API를 호출 하는 경우 *범위* 입니다.
- 디먼 앱에서 API를 호출할 수 있는 경우 *앱 역할*

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>사용자를 대신 하 여 Api의 범위 확인

클라이언트 앱이 사용자를 대신 하 여 API를 호출 하는 경우 api는 API에 대 한 특정 범위를 가진 전달자 토큰을 요청 해야 합니다. 자세한 내용은 코드 구성을 참조 하세요. [ 전달자 토큰](scenario-protected-web-api-app-configuration.md#bearer-token)입니다.

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` 메서드는 다음 단계와 같은 작업을 수행 합니다.

- `http://schemas.microsoft.com/identity/claims/scope` 또는 `scp`라는 클레임이 있는지 확인 합니다.
- 클레임에 API에서 예상 하는 범위를 포함 하는 값이 있는지 확인 합니다.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

위의 [샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) 는 ASP.NET Core에 대 한 것입니다. ASP.NET의 경우 `HttpContext.User` `ClaimsPrincipal.Current`로 바꾸고 클레임 유형 `"http://schemas.microsoft.com/identity/claims/scope"`를 `"scp"`으로 바꿉니다. 또한이 문서의 뒷부분에 나오는 코드 조각을 참조 하세요.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>디먼 앱에서 호출 하는 Api의 앱 역할 확인

웹 API가 [디먼 앱](scenario-daemon-overview.md)에 의해 호출 되는 경우 해당 앱에는 web api에 대 한 응용 프로그램 권한이 있어야 합니다. [응용 프로그램 사용 권한 (앱 역할) 노출](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)에 표시 된 것 처럼 API는 이러한 사용 권한을 노출 합니다. 한 가지 예는 `access_as_application` 앱 역할입니다.

이제 API에서 받은 토큰이 `roles` 클레임을 포함 하 고이 클레임에 필요한 값이 있는지 확인 해야 합니다. 확인 코드는 컨트롤러 작업이 범위 대신 역할을 테스트 하는 경우를 제외 하 고 위임 된 권한을 확인 하는 코드와 비슷합니다.

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole` 메서드는 다음과 같이 지정할 수 있습니다.

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

이번에는 ASP.NET 용 코드 조각입니다. ASP.NET Core의 경우 `ClaimsPrincipal.Current`을 `HttpContext.User`로 바꾸고 `"roles"` 클레임 이름을 `"http://schemas.microsoft.com/identity/claims/roles"`으로 바꿉니다. 또한이 문서의 앞부분에 나오는 코드 조각을 참조 하세요.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>웹 API가 디먼 앱 에서만 호출 되어야 하는 경우 앱 전용 토큰 수락

사용자 [는 응용 프로그램에 앱 역할을 추가 하 고 토큰에서 수신 하는 방법](howto-add-app-roles-in-azure-ad-apps.md)에 나와 있는 것 처럼 사용자 할당 패턴에서 역할 클레임을 사용할 수도 있습니다. 역할을 둘 다에 할당할 수 있는 경우 역할을 확인 하면 앱이 사용자 및 사용자로 로그인 하 여 앱으로 로그인 할 수 있습니다. 이러한 혼동을 방지 하기 위해 사용자와 앱에 대해 서로 다른 역할을 선언 하는 것이 좋습니다.

디먼 앱만 웹 API를 호출 하도록 하려면 앱 역할의 유효성을 검사할 때 토큰이 앱 전용 토큰 이라고 하는 조건을 추가 합니다.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

역 조건을 확인 하면 사용자가 로그인 하는 앱만 API를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-protected-web-api-production.md)

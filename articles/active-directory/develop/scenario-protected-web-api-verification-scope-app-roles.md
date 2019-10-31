---
title: 보호 된 web API를 사용 하 여 범위 및 앱 역할 확인
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4340d92bdfe871010021edcbefcde62ab8202462
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149225"
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

ASP.NET/ASP.NET Core web API를 보호 하려면 다음 중 하나에 `[Authorize]` 특성을 추가 해야 합니다.

- 컨트롤러 자체 (컨트롤러의 모든 작업을 보호 하려는 경우)
- API에 대 한 개별 컨트롤러 작업

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

그러나이 보호는 충분 하지 않습니다. ASP.NET/ASP.NET Core가 토큰의 유효성을 검사 하도록 보장 합니다. API는 web API를 호출 하는 데 사용 되는 토큰이 필요한 클레임을 사용 하 여 요청 되었는지 확인 해야 합니다. 특히 다음과 같은 작업을 수행 해야 합니다.

- 사용자를 대신 하 여 API를 호출 하는 경우 *범위*입니다.
- *앱 역할*(API가 디먼 앱에서 호출 될 수 있는 경우)

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>사용자를 대신 하 여 Api의 범위 확인

사용자를 대신 하 여 클라이언트 앱에서 API를 호출 하는 경우 API에 대 한 특정 범위의 전달자 토큰을 요청 해야 합니다. ( [코드 구성 참조 | 전달자 토큰](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

`VerifyUserHasAnyAcceptedScope` 메서드는 다음과 같은 작업을 수행 합니다.

- `http://schemas.microsoft.com/identity/claims/scope` 또는 `scp`라는 클레임이 있는지 확인 합니다.
- 클레임에 API가 예상 하는 범위를 포함 하는 값이 있는지 확인 합니다.

```CSharp
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

이 [샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) 는 ASP.NET Core에 대 한 것입니다. ASP.NET의 경우 `HttpContext.User` `ClaimsPrincipal.Current`로 바꾸고 클레임 유형 `"http://schemas.microsoft.com/identity/claims/scope"`를 `"scp"`으로 바꿉니다. 이 문서의 뒷부분에 나오는 코드 조각을 참조 하세요.

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>디먼 앱에서 호출 하는 Api의 앱 역할 확인

웹 API가 [디먼 앱](scenario-daemon-overview.md)에 의해 호출 되는 경우 해당 앱에는 web api에 대 한 응용 프로그램 권한이 있어야 합니다. API가 이러한 사용 권한을 노출 하는 [응용 프로그램 권한 (앱 역할) (](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) 예: `access_as_application` 앱 역할)을 노출 하는 방법을 살펴보았습니다.
이제 Api에서 받은 토큰이 `roles` 클레임을 포함 하 고이 클레임에 필요한 값이 있는지 확인 해야 합니다. 이 확인을 수행 하는 코드는 위임 된 권한을 확인 하는 코드와 비슷합니다. 단, `scopes`을 테스트 하는 대신 컨트롤러 작업에서 `roles`를 테스트 합니다.

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` 메서드는 다음과 같이 지정할 수 있습니다.

```CSharp
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

이번에는 ASP.NET 용 코드 조각입니다. ASP.NET Core의 경우 `ClaimsPrincipal.Current`을 `HttpContext.User`로 바꾸고 `"roles"` 클레임 이름을 `"http://schemas.microsoft.com/identity/claims/roles"`으로 바꿉니다. 이 문서 앞부분에 있는 코드 조각도 참조 하세요.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>웹 API가 디먼 앱 에서만 호출 되어야 하는 경우 앱 전용 토큰 수락

`roles` 클레임은 사용자 할당 패턴의 사용자 에게도 사용 됩니다. ( [방법: 응용 프로그램에서 응용 프로그램 역할을 추가 하 고 토큰에서 수신 하는 방법을](howto-add-app-roles-in-azure-ad-apps.md)참조 하세요.) 따라서 역할을 확인 하면 앱이 둘 다에 할당할 수 있는 경우 앱이 사용자로 로그인 하 고 다른 방식으로 로그인 할 수 있습니다. 이러한 혼동을 방지 하기 위해 사용자와 앱에 대해 서로 다른 역할을 선언 하는 것이 좋습니다.

디먼 앱만 웹 API를 호출할 수 있도록 허용 하려면 앱 역할의 유효성을 검사할 때 토큰이 앱 전용 토큰 임을 나타내는 조건을 추가 합니다.

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

역 조건을 확인 하면 사용자가 로그인 하는 앱만 API를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-protected-web-api-production.md)

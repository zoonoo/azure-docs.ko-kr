---
title: 앱 코드 구성-web API 보호 | Azure Active Directory
description: 보호 된 Web API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법을 알아봅니다.
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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116455"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>보호 된 web API-API에 인증 추가

이 문서에서는 Web API에 권한 부여를 추가 하는 방법을 설명 합니다. 이 보호에서만 호출 됨을 보장 합니다.

- 올바른 범위를 사용 하 여 사용자 대신 응용 프로그램 
- 또는 적절 한 응용 프로그램 역할을 사용 하 여 디먼 앱.

ASP.NET에 대 한 ASP.NET Core Web API를 보호 해야 추가 /는 `[Authorize]` 특성:

- 컨트롤러 자체 보호할 컨트롤러의 모든 작업을 하려는 경우
- 또는 API에 대 한 개별 컨트롤러 작업을 추가 합니다.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

하지만이 보호는 부족 합니다. 해당 ASP.NET를 guaranties만 / ASP.NET Core에는 토큰 유효성을 검사 합니다. API는 특히 것 클레임을 사용 하 여 Web API를 호출 하는 데 토큰을 요청한 있는지 확인 해야 합니다.

- 합니다 **범위** API 사용자를 대신 하 여 호출 되는 경우
- 합니다 **앱 역할** 디먼 앱에서 API를 호출할 수 있는 경우.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>사용자를 대신 하 여 호출 되는 Api에서 범위를 확인 합니다.

API 사용자를 대신 하 여 클라이언트 앱을 호출한 경우 API에 대 한 특정 범위를 사용 하 여 전달자 토큰을 요청 해야 (참조 [코드 구성 | 전달자 토큰](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` 메서드는 다음과 같이 수행 됩니다.

- 이라는 클레임 인지 확인 `http://schemas.microsoft.com/identity/claims/scope` 또는 `scp`
- 클레임 API에서 예상 범위를 포함 하는 값에 있는지 확인 합니다.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

이 샘플 코드는 ASP.NET Core 용. ASP.NET만 바꾸기에 대 한 `HttpContext.User` 하 여 `ClaimsPrincipal.Current`, 및 클레임 유형 `"http://schemas.microsoft.com/identity/claims/scope"` 여 `"scp"` (아래 코드 조각 참조)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>디먼 앱에서 호출 되는 Api에서 앱 역할 확인

Web API를 호출을 [디먼 응용 프로그램](scenario-daemon-overview.md), 해당 응용 프로그램에 Web API에 응용 프로그램 권한 해야 합니다. 그런 다음입니다. 살펴본 [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-]에서 이러한 권한의 노출 하는 API (예를 들어으로 `access_as_application` 앱 역할).
수신한 토큰에 포함 되어 있는지 확인 하 여 Api가 이제 해야는 `roles` 클레임 하 고이 클레임에 예상 값입니다. 이 확인을 수행 하는 코드는 위임 된 권한만 제외 하 고 테스트 하는 대신 확인 하는 코드와 유사 `scopes`, 컨트롤러 작업에 대 한 테스트 `roles`:

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

`ValidateAppRole()` 메서드는 다음과 같은 일 수 있습니다.

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case we look for a `role` value of `access_as_application`
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

이 샘플 코드는 ASP.NET 용입니다. ASP.NET Core에 대 한 바꾸기만 `ClaimsPrincipal.Current` 하 여 `HttpContext.User` 하며 `"roles"` 이름으로 클레임 `"http://schemas.microsoft.com/identity/claims/roles"` (위의 코드 조각 참조)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>디먼 앱에서 Web API만 호출 해야 하는 경우 앱 전용 토큰을 수락 합니다.

합니다 `roles` 사용자 할당 패턴에는 사용자에 대 한 클레임도 사용 됩니다 (참조 [방법: 응용 프로그램에서 앱 역할을 추가 하 고 토큰에 수신](howto-add-app-roles-in-azure-ad-apps.md)). 있으므로 역할을 검사 하면 사용자와, 다른 방법으로로 로그인 하려면 앱 경우 역할 둘 다에 할당할 수 있습니다. 이러한 혼동을 방지 하기 위해 사용자 및 앱에 대해 선언 된 다른 역할을 하는 것이 좋습니다.

Web API를 호출 하는 디먼 응용 프로그램 에서만 사용할 수 있도록 하려는 경우 조건을 추가 하려면, 앱 역할 유효성을 검사할 때 토큰은 앱 전용 토큰을 하는 것이 좋습니다.

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

역 조건 확인 API를 호출 하는 사용자를 로그인 하는 앱만 허용 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-protected-web-api-production.md)

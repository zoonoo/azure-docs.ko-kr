---
title: 로그아웃 시 토큰 캐시에서 계정 제거 - Microsoft ID 플랫폼 | Azure
description: 로그아웃 시 토큰 캐시에서 계정을 제거하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881615"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>웹 API를 호출하는 웹 앱: 전역 로그아웃시 토큰 캐시에서 계정 제거

[로그인 및 로그아웃](scenario-web-app-sign-user-sign-in.md): 로그인 및 로그 아웃 : 사용자가 로그인 웹 앱에서 웹 앱에 로그인을 추가하는 방법을 배웠습니다.

웹 API를 호출하는 웹 앱의 경우 로그아웃이 다릅니다. 사용자가 응용 프로그램 또는 응용 프로그램에서 로그아웃하면 해당 사용자와 연결된 토큰을 토큰 캐시에서 제거해야 합니다.

## <a name="intercept-the-callback-after-single-sign-out"></a>단일 로그아웃 후 콜백 가로채기

로그아웃한 계정과 연결된 토큰 캐시 항목을 지우려면 응용 `logout` 프로그램에서 after 이벤트를 가로챌 수 있습니다. 웹 앱은 토큰 캐시에 각 사용자에 대한 액세스 토큰을 저장합니다. 콜백 후를 `logout` 가로채면 웹 응용 프로그램이 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어의 경우 가로채기 메커니즘은 `AddMsal()` [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)의 메서드에 설명되어 있습니다.

응용 프로그램에 대해 이전에 등록한 로그아웃 URL을 사용하면 단일 로그아웃을 구현할 수 있습니다. Microsoft ID `logout` 플랫폼 끝점에서 로그아웃 URL을 호출합니다. 이 호출은 웹 앱또는 다른 웹 앱 또는 브라우저에서 로그아웃이 시작된 경우에 발생합니다. 자세한 내용은 [Single 로그아웃](v2-protocols-oidc.md#single-sign-out)을 참조하십시오.

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

에 대 `RemoveAccountAsync` 한 코드는 [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)사용할 수 있습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 전역 로그아웃시 캐시에서 계정을 제거하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플은 전역 로그아웃시 캐시에서 계정을 제거하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플은 전역 로그아웃의 캐시에서 계정을 제거하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [웹 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [웹 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [웹 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [웹 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

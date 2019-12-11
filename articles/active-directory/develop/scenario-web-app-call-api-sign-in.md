---
title: 로그 아웃 시 캐시에서 계정 제거-Microsoft identity platform | Microsoft
description: 로그 아웃 시 토큰 캐시에서 계정을 제거 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 375382b1245186197657c5067e3c5b4ec2b15655
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961661"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>전역 로그 아웃 시 캐시에서 계정 제거

웹 앱에 로그인을 추가 하는 방법을 이미 알고 있습니다. 사용자를 로그인 [하는 웹 앱에서 로그인을 추가](scenario-web-app-sign-user-sign-in.md)하는 방법에 대해 알아봅니다.

여기에서 다른 것은 사용자가 로그 아웃 하거나,이 응용 프로그램에서 또는 모든 응용 프로그램에서 사용자와 연결 된 토큰을 토큰 캐시에서 제거 하려는 경우입니다.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>로그 아웃 후 콜백 가로채기-단일 로그 아웃

응용 프로그램은 로그 아웃 된 계정과 연결 된 토큰 캐시의 항목을 지우기 위해 `logout` after 이벤트를 가로챌 수 있습니다. 웹 앱은 캐시에 사용자에 대 한 액세스 토큰을 저장 합니다. `logout` 후 콜백을 가로채 면 웹 응용 프로그램이 토큰 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

이 메커니즘은 [WebAppServiceCollectionExtensions # L151](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) 의 `AddMsal()` 메서드에 설명 되어 있습니다.

응용 프로그램에 대해 등록 한 **로그 아웃 Url** 을 사용 하 여 single sign-on을 구현할 수 있습니다. Microsoft id 플랫폼 `logout` 끝점은 응용 프로그램에 등록 된 **로그 아웃 URL** 을 호출 합니다. 이 호출은 웹 앱 또는 다른 웹 앱 또는 브라우저에서 로그 아웃을 시작한 경우에 발생 합니다. 자세한 내용은 [Single sign-on](v2-protocols-oidc.md#single-sign-out)을 참조 하세요.

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

RemoveAccountAsync에 대 한 코드는 [L264 # L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)에서 사용할 수 있습니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

Java 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

---
title: 호출 웹 Api (로그인)-Microsoft id 플랫폼에는 웹 앱
description: Web Api (로그인)를 호출 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074562"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web Api-로그인을 호출 하는 웹 앱

웹 앱에 로그인 추가 하는 방법을 이미 알고 있습니다. 에 대해 배웁니다 [웹 앱 로그인 사용자-로그인 추가](scenario-web-app-sign-user-sign-in.md)합니다.

새로운이 다른 여기에서 아웃,이 응용 프로그램 또는 응용 프로그램에서 사용자가 서명 하면 되도록 사용자와 연결 된 토큰을 토큰 캐시에서 제거 됩니다.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>로그 아웃-단일 로그 아웃 한 후 콜백을 차단

응용 프로그램을 가로챌 수 있습니다를 후 `logout` 이벤트 예를 들어 로그 아웃 하는 계정에 연결 된 토큰 캐시의 항목을 선택 취소 합니다. 웹 앱 사용자에 대 한 액세스 토큰을 캐시에 저장 됩니다는, (Web API를 호출 하는 웹 앱)에 대 한이 자습서의 2 부에서 살펴보겠습니다. 가로채기는 후 `logout` 콜백 웹 응용 프로그램은 토큰 캐시에서 사용자를 제거할 수 있습니다. 이 메커니즘에 설명 되어는 `AddMsal()` 메서드의 [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

합니다 **로그 아웃 Url** 응용 프로그램을 사용 하면 단일 로그 아웃을 구현할 수 있습니다에 대 한 등록 했습니다. Microsoft id 플랫폼 `logout` 끝점을 호출 하는 합니다 **로그 아웃 URL** 응용 프로그램을 등록 합니다. 이 호출 또는 다른 웹 앱 또는 브라우저에서 웹 앱에서 로그 아웃 진행 된 시작 하는 경우에 발생 합니다. 자세한 내용은 [Single sign-out](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) 개념 설명서에서.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 가져오기](scenario-web-app-call-api-acquire-token.md)

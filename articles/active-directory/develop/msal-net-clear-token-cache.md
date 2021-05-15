---
title: 토큰 캐시 지우기(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET(MSAL.NET)을 사용하여 토큰 캐시를 지우는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064762"
---
# <a name="clear-the-token-cache-using-msalnet"></a>MSAL.NET을 사용하여 토큰 캐시 지우기

Microsoft Authentication Library for .NET(MSAL.NET)을 사용하여 [액세스 토큰을 가져오면](msal-acquire-cache-tokens.md) 토큰이 캐시됩니다. 애플리케이션에 토큰이 필요한 경우 먼저 `AcquireTokenSilent` 메서드를 호출하여 허용되는 토큰이 캐시에 있는지 확인해야 합니다. 

캐시에서 계정을 제거하여 캐시를 지웁니다. 이 경우 브라우저에 있는 세션 쿠키는 제거되지 않습니다.  다음 예제에서는 공용 클라이언트 애플리케이션을 인스턴스화하고 애플리케이션에 대한 계정을 가져오고 계정을 제거합니다.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

토큰 획득 및 캐싱에 대한 자세한 내용은 [액세스 토큰 가져오기](msal-acquire-cache-tokens.md)를 참조하세요.

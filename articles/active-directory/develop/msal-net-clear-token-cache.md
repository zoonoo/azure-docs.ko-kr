---
title: 토큰 캐시 지우기(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 토큰 캐시를 지우는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084774"
---
# <a name="clear-the-token-cache-using-msalnet"></a>MSAL.NET 사용하여 토큰 캐시 지우기

.NET(MSAL.NET 대한 Microsoft 인증 라이브러리를 사용하여 [액세스 토큰을 획득하면](msal-acquire-cache-tokens.md) 토큰이 캐시됩니다. 응용 프로그램에 토큰이 필요한 경우 먼저 `AcquireTokenSilent` 메서드를 호출하여 허용 가능한 토큰이 캐시에 있는지 확인해야 합니다. 

캐시를 지우는 것은 캐시에서 계정을 제거하여 수행됩니다. 이 경우 브라우저에 있는 세션 쿠키는 제거되지 않습니다.  다음 예제에서는 공용 클라이언트 응용 프로그램을 인스턴스화하고 응용 프로그램의 계정을 가져옵니다.

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

토큰 획득 및 캐싱에 대해 자세히 알아보려면 액세스 토큰 을 [획득하십시오.](msal-acquire-cache-tokens.md)

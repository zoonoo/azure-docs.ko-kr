---
title: Microsoft Authentication Library for.NET-Azure 사용 하 여 토큰 캐시를 지우려면
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 토큰 캐시를 지우는 방법에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544113"
---
# <a name="clear-the-token-cache-using-msalnet"></a>MSAL.NET을 사용 하 여 토큰 캐시를 지우려면

경우 있습니다 [액세스 토큰을 획득](msal-acquire-cache-tokens.md) .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리 사용 하 여 토큰이 캐시 됩니다. 먼저 호출 해야 응용 프로그램에서 토큰에는 `AcquireTokenSilent` 허용 되는 토큰 캐시에 있는지 확인 하는 방법입니다. 

캐시를 지우면 캐시에서 계정을 제거 하 여 이루어집니다. 이 경우 브라우저에 있는 세션 쿠키는 제거되지 않습니다.  다음 예제에서는 공용 클라이언트 응용 프로그램, 응용 프로그램에 대 한 계정을 가져옵니다 인스턴스화하고 계정을 제거 합니다.

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

자세한 정보 획득 및 토큰 캐시에 대해 알아보려면 [액세스 토큰을 획득](msal-acquire-cache-tokens.md)합니다.

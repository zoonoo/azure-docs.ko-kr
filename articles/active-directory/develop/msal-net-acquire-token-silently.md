---
title: 캐시에서 토큰 획득(MSAL.NET)
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 토큰 캐시에서 자동으로 액세스 토큰을 획득하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084841"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET 사용하여 토큰 캐시에서 토큰 을 가져옵니다.

.NET(MSAL.NET 대한 Microsoft 인증 라이브러리를 사용하여 액세스 토큰을 획득하면 토큰이 캐시됩니다. 응용 프로그램에 토큰이 필요한 경우 먼저 `AcquireTokenSilent` 메서드를 호출하여 허용 가능한 토큰이 캐시에 있는지 확인해야 합니다. 대부분의 경우 캐시의 토큰을 기반으로 더 많은 범위를 가진 다른 토큰을 획득할 수 있습니다. 토큰 캐시에 새로 고침 토큰이 포함되어 있으므로 만료가 가까워지면 토큰을 새로 고칠 수도 있습니다.

권장되는 패턴은 메서드를 먼저 호출하는 `AcquireTokenSilent` 것입니다.  실패하면 `AcquireTokenSilent` 다른 방법을 사용하여 토큰을 획득합니다.

다음 예제에서 응용 프로그램은 먼저 토큰 캐시에서 토큰을 획득하려고 시도합니다.  예외가 `MsalUiRequiredException` throw된 경우 응용 프로그램은 대화식으로 토큰을 수집합니다. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```

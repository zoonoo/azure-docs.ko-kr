---
title: 자동으로 토큰을 획득 (Microsoft Authentication Library for.NET) | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 자동으로 (토큰 캐시에서) 액세스 토큰을 획득 하는 방법에 알아봅니다.
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544224"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET을 사용 하 여 토큰 캐시에서 토큰 가져오기

.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 액세스 토큰을 획득 하는 경우 토큰이 캐시 됩니다. 먼저 호출 해야 응용 프로그램에서 토큰에는 `AcquireTokenSilent` 허용 되는 토큰 캐시에 있는지 확인 하는 방법입니다. 대부분의 경우에서 캐시에서 토큰을 기반으로 하는 더 많은 범위를 사용 하 여 다른 토큰을 획득 하는 것이 같습니다. (토큰 캐시도 포함 되어 있으므로 새로 고침 토큰) 만료 가져올 때 토큰을 새로 고칠 수 이기도 합니다.

권장 되는 패턴을 호출 하는 것은 `AcquireTokenSilent` 메서드 첫 번째입니다.  경우 `AcquireTokenSilent` 실패 하면 다른 메서드를 사용 하 여 토큰을 획득 합니다.

다음 예제에서는 응용 프로그램은 토큰 캐시에서 토큰을 획득 하려면 먼저 시도 합니다.  경우는 `MsalUiRequiredException` 예외가, 응용 프로그램 토큰을 대화형으로 가져옵니다. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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
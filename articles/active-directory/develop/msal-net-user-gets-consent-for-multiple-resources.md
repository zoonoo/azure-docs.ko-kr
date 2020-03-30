---
title: 여러 리소스에 대한 동의를 얻기(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 사용자가 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 여러 리소스에 대한 사전 동의를 얻는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085847"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>사용자는 MSAL.NET 사용하여 여러 리소스에 대한 동의를 얻습니다.
Microsoft ID 플랫폼 끝점에서는 여러 리소스에 대한 토큰을 한 번에 얻을 수 없습니다. .NET(MSAL.NET Microsoft 인증 라이브러리를 사용하는 경우 acquire token 메서드의 범위 매개 변수에는 단일 리소스에 대한 범위만 포함되어야 합니다. 그러나 builder 메서드를 사용 하 여 추가 범위를 지정 하 `.WithExtraScopeToConsent` 여 미리 여러 리소스에 동의할 수 있습니다.

> [!NOTE]
> 여러 리소스에 대한 동의를 얻는 것은 Microsoft ID 플랫폼에 는 적용되지만 Azure AD B2C에서는 사용할 수 없습니다. Azure AD B2C는 사용자 동의가 아닌 관리자 동의만 지원합니다.

예를 들어, 각각 2개의 범위가 있는 두 개의 리소스가 있는 경우:

- https:\//mytenant.onmicrosoft.com/customerapi (범위 `customer.read` 2 `customer.write`개 및)
- https:\//mytenant.onmicrosoft.com/vendorapi (2 `vendor.read` 범위 `vendor.write`및)

다음 예제와 `.WithExtraScopeToConsent` 같이 *extraScopesToConsent* 매개 변수가 있는 수정자를 사용해야 합니다.

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

그러면 첫 번째 웹 API에 대한 액세스 토큰이 제공됩니다. 그런 다음 두 번째 웹 API에 액세스해야 하는 경우 토큰 캐시에서 토큰을 자동으로 획득할 수 있습니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

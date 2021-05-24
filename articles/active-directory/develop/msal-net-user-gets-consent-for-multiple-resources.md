---
title: 여러 리소스에 대한 동의 얻기(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 사용자가 .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 여러 리소스에 대한 사전 동의를 얻는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d22b4109cdcdc965b1b2e03aba592022c58f773b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583793"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>사용자가 MSAL.NET을 사용하여 여러 리소스에 대한 동의를 얻습니다.
Microsoft ID 플랫폼에서는 여러 리소스에 대한 토큰을 한 번에 가져올 수 없습니다. .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하는 경우 토큰 획득 메서드의 범위 매개 변수에는 단일 리소스에 대한 범위만 포함되어야 합니다. 그러나 `.WithExtraScopeToConsent` 작성기 메서드를 사용해 추가 범위를 지정하여 사전에 여러 리소스에 동의할 수 있습니다.

> [!NOTE]
> 여러 리소스에 대한 동의를 얻는 것은 Microsoft ID 플랫폼에는 적용되지만 Azure AD B2C에는 적용되지 않습니다. Azure AD B2C는 사용자 동의가 아닌 관리자 동의만 지원합니다.

예를 들어 각각 범위가 2개인 리소스가 2개가 있는 경우 다음을 수행합니다.

- https:\//mytenant.onmicrosoft.com/customerapi(2개 범위 `customer.read` 및 `customer.write` 포함)
- https:\//mytenant.onmicrosoft.com/vendorapi(2개 범위 `vendor.read` 및 `vendor.write` 포함)

다음 예제에 표시된 대로 *extraScopesToConsent* 매개 변수를 포함하는 `.WithExtraScopeToConsent` 한정자를 사용해야 합니다.

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

이렇게 하면 첫 번째 웹 API에 대한 액세스 토큰을 가져올 수 있습니다. 그러면 두 번째 웹 API에 액세스하기 위해 토큰 캐시에서 토큰을 자동으로 가져올 수 있습니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

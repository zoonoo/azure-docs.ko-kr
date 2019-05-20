---
title: 여러 리소스 (Microsoft Authentication Library for.NET)에 대 한 동의 가져올 | Azure
description: 사용자.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 여러 리소스에 대 한 사전 동의 얻을 수 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 8f7d24a1e14cfbb1163ab78b94dd36ec288dce50
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544042"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>사용자가 MSAL.NET을 사용 하 여 여러 리소스에 대 한 동의
Microsoft id 플랫폼 끝점 한 번에 여러 리소스에 대 한 토큰을 가져올 수 없도록 합니다. Microsoft 인증 라이브러리.NET (MSAL.NET)를 사용 하는 경우 토큰 획득 메서드 범위 매개 변수는 단일 리소스에 대 한 범위만 포함 해야 합니다. 그러나 수 사전에 동의 하는 몇 가지 리소스 사전 사용 하 여 추가 범위를 지정 하 여는 `.WithExtraScopeToConsent` 작성기 메서드.

> [!NOTE]
> Azure AD B2C 아니라 Microsoft id 플랫폼에 대 한 몇 가지 리소스 작동에 대 한 동의 가져오는 중입니다. Azure AD B2C 사용자 동의 하지 유일한 관리자 동의 지원합니다.

예를 들어 있는 두 개의 리소스가 있는 경우 2 각 범위:

- https://mytenant.onmicrosoft.com/customerapi (2 범위를 사용 하 여 `customer.read` 고 `customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (2 범위를 사용 하 여 `vendor.read` 고 `vendor.write`)

사용 해야 합니다 `.WithExtraScopeToConsent` 있는 한정자를 *extraScopesToConsent* 다음 예와에서 같이 매개 변수:

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

이 하면 액세스 토큰을 가져온 첫 번째 web API에 대 한 합니다. 그런 다음 두 번째 웹 API에 액세스 해야 할 경우에 자동으로 토큰 캐시에서 토큰을 얻을 수 있습니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

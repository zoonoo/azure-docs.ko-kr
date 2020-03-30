---
title: 웹 API를 프로덕션으로 호출하는 데스크톱 앱 이동 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 프로덕션 프로덕션으로 호출하는 데스크톱 앱을 이동하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262569"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 데스크톱 앱: 프로덕션으로 이동

이 문서에서는 웹 API를 프로덕션 으로 호출하는 데스크톱 앱을 이동하는 방법을 배웁니다.

## <a name="handle-errors-in-desktop-applications"></a>데스크톱 응용 프로그램의 오류 처리

다른 흐름에서 코드 조각에 표시된 것처럼 자동 흐름에 대한 오류를 처리하는 방법을 배웠습니다. 또한 증분 동의 및 조건부 액세스와 같이 상호 작용이 필요한 경우도 있음을 알 수 있습니다.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>여러 리소스에 대한 사용자 동의를 선행으로 유지

> [!NOTE]
> 여러 리소스에 대한 동의를 얻는 것은 Microsoft ID 플랫폼에 는 적용되지만 Azure Active Directory(Azure AD) B2C에는 적용되지 않습니다. Azure AD B2C는 사용자 동의가 아닌 관리자 동의만 지원합니다.

Microsoft ID 플랫폼(v2.0) 끝점에서는 여러 리소스에 대한 토큰을 한 번에 얻을 수 없습니다. 매개 `scopes` 변수에는 단일 리소스에 대한 범위만 포함될 수 있습니다. `extraScopesToConsent` 사용자가 매개 변수를 사용하여 여러 리소스에 미리 동의하도록 할 수 있습니다.

예를 들어 각각 두 개의 범위가 있는 두 개의 리소스가 있을 수 있습니다.

- `https://mytenant.onmicrosoft.com/customerapi``customer.read` 범위와`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi``vendor.read` 범위와`vendor.write`

이 예제에서는 `.WithAdditionalPromptToConsent` `extraScopesToConsent` 매개 변수가 있는 수정자를 사용합니다.

예:

### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

이 호출은 첫 번째 웹 API에 대한 액세스 토큰을 가져옵니다.

두 번째 웹 API를 호출해야 `AcquireTokenSilent` 하는 경우 API를 호출합니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsoft 개인 계정은 앱을 실행할 때마다 다시 동의해야 합니다.

Microsoft 개인 계정 사용자의 경우 각 기본 클라이언트(데스크톱 또는 모바일 앱) 호출에 대한 동의를 다시 묻는 메시지가 의도된 동작입니다. 네이티브 클라이언트 ID는 본질적으로 안전하지 않습니다. 기밀 클라이언트 응용 프로그램은 Microsoft ID 플랫폼과 비밀을 교환하여 ID를 증명합니다. Microsoft ID 플랫폼은 응용 프로그램이 승인될 때마다 사용자에게 동의를 요청하여 소비자 서비스에 대한 이러한 불안을 완화하기로 결정했습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

---
title: 호출 웹 Microsoft id 플랫폼 Api (프로덕션으로 이동)-되는 데스크톱 앱
description: 데스크톱 앱을 빌드하는 방법을 알아봅니다 호출 웹 Api (프로덕션으로 이동) 되는
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111195"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>웹을 호출 하는 데스크톱 앱 Api-프로덕션 환경으로 이동

이 문서에서는 응용 프로그램을 추가로 개선 하 고 프로덕션으로 이동 하려면 세부 정보를 제공 합니다.

## <a name="handling-errors-in-desktop-applications"></a>데스크톱 응용 프로그램에서 오류 처리

다른 흐름 (에서처럼 코드 조각)에 대 한 자동 흐름 오류를 처리 하는 방법을 알아보았습니다. 또한 살펴보았습니다 (증분 동 및 조건부 액세스)에 필요한 상호 작용이 있는 경우가 많습니다.

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>선불 여러 리소스에 대 한 사용자 동의가 하는 방법

> [!NOTE]
> Azure Active Directory (Azure AD) B2C 아니라 Microsoft id 플랫폼에 대 한 몇 가지 리소스 작동에 대 한 동의 가져오는 중입니다. Azure AD B2C 사용자 동의 하지 유일한 관리자 동의 지원합니다.

Microsoft id 플랫폼 (v2.0) 끝점에 한 번에 여러 리소스에 대 한 토큰을 가져올 수를 허용 하지 않습니다. 따라서는 `scopes` 매개 변수는 단일 리소스에 대 한 범위만 포함할 수 있습니다. 사용자 미리 동의 몇 가지 리소스를 사용 하 여 확인할 수 있습니다는 `extraScopesToConsent` 매개 변수입니다.

예를 들어 두 개의 리소스에 있는 경우 각 범위는 두 개:

- `https://mytenant.onmicrosoft.com/customerapi` -2 범위를 사용 하 여 `customer.read` 및 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -2 범위를 사용 하 여 `vendor.read` 및 `vendor.write`

사용 해야 합니다 `.WithAdditionalPromptToConsent` 있는 한정자를 `extraScopesToConsent` 매개 변수입니다.

예:

```CSharp
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

이 호출은 첫 번째 web API에 대 한 액세스 토큰을 얻게 됩니다.

두 번째 web API를 호출 해야 할 경우 호출할 수 있습니다.

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Microsoft 개인 계정에 앱이 실행 될 때마다 reconsenting 필요

사용자의 Microsoft 개인 계정에 대 한 권한을 부여 하려면 각 네이티브 클라이언트 (데스크톱/모바일 앱) 호출에서 동의 reprompting 의도 한 동작입니다. 네이티브 클라이언트 id (신원을 증명 하기 위해 Microsoft Id 플랫폼을 사용 하 여 비밀을 교환 하는 비밀 클라이언트 응용 프로그램)에 위배 본질적으로 안전 하지 않습니다. Microsoft id 플랫폼 동의 응용 프로그램에 권한이 부여 된 각 시간에 대 한 사용자에 게 소비자 서비스에 대 한이 세상을 완화 하도록 선택 했습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

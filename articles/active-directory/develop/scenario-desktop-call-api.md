---
title: 데스크톱 앱 웹 Api (web API 호출-)를 호출 되는 Microsoft id 플랫폼
description: 웹 Api (web API 호출)를 호출 하는 데스크톱 앱을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abaf234d3b216e0f67501e5d2f2f5c3f874c5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111238"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Web API를 호출 하는 웹 Api를 호출 하는 데스크톱 앱

토큰을 설정 했으므로 보호 된 web API를 호출할 수 있습니다.

## <a name="calling-a-web-api-from-net"></a>.NET에서 web API 호출

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>증분 동 및 조건부 액세스-여러 Api를 호출합니다.

첫 번째 API에 대 한 토큰을 가져온 후 동일한 사용자에 대 한 여러 Api를 호출 해야 할 경우 호출할 수 있습니다만 `AcquireTokenSilent`, 서 토큰을 다른 Api에 대 한 자동으로 대부분의 시간입니다.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

상호 작용이 필요한 경우는 다음과 같은 경우

- 사용자는 첫 번째 API에 대 한 동의 했지만 이제 더 많은 범위 (증분 동의)에 대 한 동의 해야
- 첫 번째 API 다중 요소 인증을 요구 하지 않지만 다음 합니다.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-desktop-production.md)

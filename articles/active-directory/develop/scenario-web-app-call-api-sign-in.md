---
title: 로그 아웃 시 토큰 캐시에서 계정 제거-Microsoft identity platform | Microsoft
description: 로그 아웃 시 토큰 캐시에서 계정을 제거 하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b7f59f235f4baa270b36b01cc4532227ab23fbc8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442534"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>웹 Api를 호출 하는 웹 앱: 전역 로그 아웃 시 토큰 캐시에서 계정 제거

[로그인 및 로그 아웃 사용자를 로그인 하는 웹 앱](scenario-web-app-sign-user-sign-in.md)의 웹 앱에 로그인을 추가 하는 방법을 배웠습니다.

웹 api를 호출 하는 웹 앱의 경우에는 로그 아웃이 다릅니다. 사용자가 응용 프로그램에서 로그 아웃 하거나 응용 프로그램에서 로그 아웃 하면 토큰 캐시에서 해당 사용자와 연결 된 토큰을 제거 해야 합니다.

## <a name="intercept-the-callback-after-single-sign-out"></a>단일 로그 아웃 후 콜백을 가로챕니다.

로그 아웃 한 계정에 연결 된 토큰 캐시 엔트리를 지우기 위해 응용 프로그램은 after 이벤트를 가로챌 수 있습니다 `logout` . Web apps는 토큰 캐시에 있는 각 사용자에 대 한 액세스 토큰을 저장 합니다. `logout`웹 응용 프로그램은 after 콜백을 가로채 면 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity는 로그 아웃을 구현 하는 작업을 담당 합니다. 자세한 내용은 [Microsoft. Identity. 웹 소스 코드](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176) 를 참조 하세요.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 시나리오의 다음 문서로 이동 하 여 [웹 앱에 대 한 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 시나리오의 다음 문서로 이동 하 여 [웹 앱에 대 한 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서로 이동 하 여 [웹 앱에 대 한 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서로 이동 하 여 [웹 앱에 대 한 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---
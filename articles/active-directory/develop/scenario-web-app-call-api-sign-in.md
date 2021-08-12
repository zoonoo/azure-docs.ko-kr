---
title: 로그아웃 시 토큰 캐시에서 계정 제거 | Azure
titleSuffix: Microsoft identity platform
description: 로그아웃 시 토큰 캐시에서 계정을 제거하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756251"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>웹 API를 호출하는 웹앱: 글로벌 로그아웃 시 토큰 캐시에서 계정 제거

[사용자를 로그인하는 웹앱: 로그인과 로그아웃](scenario-web-app-sign-user-sign-in.md)에서 웹앱에 로그인을 추가하는 방법을 배웠습니다.

웹 API를 호출하는 웹앱의 경우에는 로그아웃이 다릅니다. 사용자가 자신의 응용 프로그램에서 로그아웃 하거나 혹은 다른 응용 프로그램에서 로그아웃하면 토큰 캐시에서 해당 사용자와 연결된 토큰을 제거해야 합니다.

## <a name="intercept-the-callback-after-single-sign-out"></a>단일 로그아웃 후 콜백을 가로챕니다.

로그아웃 한 계정에 연결 된 토큰 캐시 엔트리를 지우기 위해 응용 프로그램은 이후의 `logout` 이벤트를 가로챌 수 있습니다. 웹앱은 토큰 캐시에 있는 각 사용자에 대한 액세스 토큰을 저장합니다. 웹 응용 프로그램은 이후의 `logout` 콜백을 가로채면 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web은 로그아웃을 구현하는 작업을 담당합니다. 자세한 내용은 [Microsoft.Identity.Web 소스 코드를 참조하세요](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 글로벌 로그아웃 시 캐시에서 계정을 제거하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플은 글로벌 로그아웃 시 캐시에서 계정을 제거하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플은 글로벌 로그아웃 시 캐시에서 계정을 제거하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 시나리오의 다음 문서로 이동하여 [웹앱의 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 시나리오의 다음 문서로 이동하여 [웹앱의 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서로 이동하여 [웹앱의 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서로 이동하여 [웹앱의 토큰을 가져옵니다](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---
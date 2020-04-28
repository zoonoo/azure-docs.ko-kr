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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181650"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>웹 Api를 호출 하는 웹 앱: 전역 로그 아웃 시 토큰 캐시에서 계정 제거

[로그인 및 로그 아웃 사용자를 로그인 하는 웹 앱](scenario-web-app-sign-user-sign-in.md)의 웹 앱에 로그인을 추가 하는 방법을 배웠습니다.

웹 api를 호출 하는 웹 앱의 경우에는 로그 아웃이 다릅니다. 사용자가 응용 프로그램에서 로그 아웃 하거나 응용 프로그램에서 로그 아웃 하면 토큰 캐시에서 해당 사용자와 연결 된 토큰을 제거 해야 합니다.

## <a name="intercept-the-callback-after-single-sign-out"></a>단일 로그 아웃 후 콜백을 가로챕니다.

로그 아웃 한 계정에 연결 된 토큰 캐시 엔트리를 지우기 위해 응용 프로그램은 after `logout` 이벤트를 가로챌 수 있습니다. Web apps는 토큰 캐시에 있는 각 사용자에 대 한 액세스 토큰을 저장 합니다. 웹 응용 프로그램은 `logout` after 콜백을 가로채 면 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity는 로그 아웃을 구현 하는 작업을 담당 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

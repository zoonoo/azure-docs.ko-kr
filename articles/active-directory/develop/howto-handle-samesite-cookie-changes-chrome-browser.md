---
title: Chrome 브라우저에서 SameSite 쿠키 변경을 처리 하는 방법 | Microsoft
titleSuffix: Microsoft identity platform
description: Chrome 브라우저에서 SameSite 쿠키 변경을 처리 하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534604"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리

## <a name="what-is-samesite"></a>SameSite 란?

`SameSite`는 웹 응용 프로그램에서 CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 HTTP 쿠키에 설정할 수 있는 속성입니다.

- 가 `SameSite` **느슨한**로 설정 되 면 쿠키는 동일한 사이트 내의 요청 및 다른 사이트의 GET 요청에서 전송 됩니다. 도메인 간 가져오기 요청에서 전송 되지 않습니다.
- 값이 **Strict** 이면 쿠키는 동일한 사이트 내 에서만 요청에서 전송 됩니다.

기본적으로이 `SameSite` 값은 브라우저에서 설정 되지 않으므로 요청에서 전송 되는 쿠키에 대 한 제한이 없습니다. 응용 프로그램은 요구 사항에 따라 **느슨한** 또는 **Strict** 를 설정 하 여 csrf 보호를 옵트인 (opt in) 해야 합니다.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 변경 내용 및 인증에 미치는 영향

[SameSite의 표준에 대](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) 한 최신 업데이트는 값이 낮게 설정 되지 않은 경우 `SameSite` 의 기본 동작을 만들어 앱 보호를 제안 합니다. 이러한 완화는 다른 사이트에서 가져오기를 제외 하 고는 HTTP 요청에 대 한 쿠키가 제한 됨을 의미 합니다. 또한 **None** 값을 추가 하 여 전송 되는 쿠키에 대 한 제한을 제거 합니다. 이러한 업데이트는 곧 Chrome 브라우저의 이후 버전에서 출시 될 예정입니다.

웹 앱이 "form_post" 응답 모드를 사용 하 여 Microsoft Id 플랫폼으로 인증 하는 경우 로그인 서버는 토큰 또는 인증 코드를 전송 하는 HTTP POST를 사용 하 여 응용 프로그램에 응답 합니다. 이 요청은 도메인 간 요청 `login.microsoftonline.com` `https://contoso.com/auth`이기 때문에 앱에 의해 설정 된 쿠키가 이제 Chrome의 새 규칙 아래에 포함 됩니다. 사이트 간 시나리오에서 사용 해야 하는 쿠키는 *상태* 및 *nonce* 값을 보유 하는 쿠키 이며 로그인 요청에도 전송 됩니다. 세션을 보류 하기 위해 Azure AD에 의해 삭제 된 다른 쿠키가 있습니다.

웹 앱을 업데이트 하지 않으면이 새로운 동작으로 인해 인증 오류가 발생 합니다.

## <a name="mitigation-and-samples"></a>완화 및 샘플

인증 오류를 해결 하기 위해 Microsoft id 플랫폼을 사용 하 여 인증 하는 웹 `SameSite` 앱은 `None` Chrome 브라우저에서 실행 될 때 도메인 간 시나리오에서 사용 되는 쿠키에 대해 속성을로 설정할 수 있습니다.
다른 브라우저 (전체 목록은 [여기](https://www.chromium.org/updates/same-site/incompatible-clients) 참조)의 `SameSite` 이전 동작을 따르고가 설정 된 경우 `SameSite=None` 쿠키를 포함 하지 않습니다.
그 이유는 여러 브라우저에서 인증을 지원 하기 위해 웹 앱은 Chrome 에서만 `SameSite` 값을로 `None` 설정 하 고 다른 브라우저에서는 값을 비워 두는 것입니다.

이 방법은 아래의 코드 샘플에 나와 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

아래 표에서는 ASP.NET 및 ASP.NET Core 샘플에서 SameSite 변경 사항을 해결 한 끌어오기 요청을 보여 줍니다.

| 예제 | 끌어오기 요청 |
| ------ | ------------ |
|  [ASP.NET Core 웹 앱 증분 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [동일한 사이트 쿠키 수정 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [동일한 사이트 쿠키 수정 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active directory-dotnet-admin-제한-v i v](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [동일한 사이트 쿠키 수정 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

ASP.NET 및 ASP.NET Core에서 SameSite 쿠키를 처리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [ASP.NET Core에서 SameSite 쿠키를 사용](https://docs.microsoft.com/aspnet/core/security/samesite) 합니다.
- [SameSite 문제에 대 한 ASP.NET 블로그](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 예제 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 예제 | 끌어오기 요청 |
| ------ | ------------ |
|  [webapp--id](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [동일한 사이트 쿠키 수정 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [webapi--id](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [동일한 사이트 쿠키 수정 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>다음 단계

SameSite 및 웹 앱 시나리오에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [SameSite의 Google Chrome FAQ](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite 페이지](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [시나리오: 사용자에 게 로그인 하는 웹 앱](scenario-web-app-sign-user-overview.md)
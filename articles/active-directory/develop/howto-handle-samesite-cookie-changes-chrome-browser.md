---
title: 크롬 브라우저에서 동일 사이트 쿠키 변경 내용을 처리하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: Chrome 브라우저에서 SameSite 쿠키 변경 사항을 처리하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534604"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리

## <a name="what-is-samesite"></a>동일 사이트란 무엇입니까?

`SameSite`은 웹 응용 프로그램에서 교차 사이트 요청 위조(CSRF) 공격을 방지하기 위해 HTTP 쿠키로 설정할 수 있는 속성입니다.

- `SameSite` **Lax로**설정되면 쿠키는 동일한 사이트 내에서 요청되고 다른 사이트의 GET 요청으로 전송됩니다. 교차 도메인인 GET 요청으로 전송되지 않습니다.
- **Strict** 값을 사용하면 쿠키가 동일한 사이트 내에서만 요청으로 전송됩니다.

기본적으로 `SameSite` 이 값은 브라우저에서 설정되지 않으며 요청에 전송되는 쿠키에 제한이 없습니다. 응용 프로그램은 요구 사항에 따라 **Lax** 또는 **Strict을** 설정하여 CSRF 보호에 옵트인해야 합니다.

## <a name="samesite-changes-and-impact-on-authentication"></a>동일사이트 변경 및 인증에 미치는 영향

[SameSite의 표준에 대한](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) 최근 업데이트는 값이 Lax로 설정되지 않은 경우의 `SameSite` 기본 동작을 만들어 앱 보호를 제안합니다. 이 완화는 다른 사이트에서 만든 GET을 제외한 HTTP 요청에 쿠키가 제한된다는 것을 의미합니다. 또한 전송되는 쿠키에 대한 제한을 제거하기 위해 **None** 값이 도입됩니다. 이러한 업데이트는 곧 Chrome 브라우저의 향후 버전에서 릴리스될 예정입니다.

웹 앱이 응답 모드 "form_post"를 사용하여 Microsoft Id 플랫폼으로 인증되면 로그인 서버는 HTTP POST를 사용하여 응용 프로그램에 응답하여 토큰 또는 인증 코드를 보냅니다. 이 요청은 도메인 간 요청(예: `login.microsoftonline.com` `https://contoso.com/auth`도메인에서)이므로 앱에서 설정한 쿠키는 이제 Chrome의 새 규칙에 해당됩니다. 교차 사이트 시나리오에서 사용해야 하는 쿠키는 로그인 요청에서 전송되는 *상태* 및 *nonce* 값을 보유하는 쿠키입니다. 세션을 보류하기 위해 Azure AD에서 삭제한 다른 쿠키가 있습니다.

웹 앱을 업데이트하지 않으면 이 새로운 동작으로 인해 인증 오류가 발생합니다.

## <a name="mitigation-and-samples"></a>완화 및 샘플

인증 실패를 극복하기 위해 Microsoft ID 플랫폼으로 인증하는 웹 `SameSite` 앱은 Chrome 브라우저에서 실행할 때 도메인 간 시나리오에서 사용되는 `None` 쿠키에 대한 속성을 설정할 수 있습니다.
다른 브라우저(전체 목록은 [여기](https://www.chromium.org/updates/same-site/incompatible-clients) 참조)는 이전 `SameSite` 동작을 따르며 설정된 `SameSite=None` 경우 쿠키를 포함하지 않습니다.
그렇기 때문에 여러 브라우저에서 인증을 지원하려면 웹 앱에서만 `SameSite` 값을 `None` Chrome에서만 설정하고 다른 브라우저에서는 값을 비워 두어야 합니다.

이 방법은 아래 코드 샘플에서 보여 주어도 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

아래 표는 ASP.NET 및 ASP.NET 코어 샘플의 SameSite 변경 사항을 해결한 끌어오기 요청을 제공합니다.

| 예제 | 끌어오기 요청 |
| ------ | ------------ |
|  [ASP.NET 코어 웹 앱 증분 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [동일한 사이트 쿠키 수정 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [동일한 사이트 쿠키 수정 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [활성 디렉토리-도트넷-관리자 제한 범위-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [동일한 사이트 쿠키 수정 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

ASP.NET 및 ASP.NET 코어에서 SameSite 쿠키를 처리하는 방법에 대한 자세한 내용은 다음을 참조하십시오.

- [ASP.NET 코어에서 동일 사이트 쿠키와 함께 작동합니다.](https://docs.microsoft.com/aspnet/core/security/samesite)
- [ASP.NET 블로그 에 동일사이트 문제](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 예제 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 예제 | 끌어오기 요청 |
| ------ | ------------ |
|  [MS-아이덴티티-자바 웹 앱](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [동일한 사이트 쿠키 수정 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-아이덴티티-자바-웹라피](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [동일한 사이트 쿠키 수정 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>다음 단계

SameSite 및 웹 앱 시나리오에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [같은에 구글 크롬의 자주 묻는 질문사이트](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [크롬 동일사이트 페이지](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [시나리오: 사용자에 서명하는 웹 앱](scenario-web-app-sign-user-overview.md)
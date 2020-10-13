---
title: Azure Static Web Apps의 경로
description: 백 엔드 라우팅 규칙 및 역할을 사용하여 경로를 보호하는 방법에 대해 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 8abbe575e855347714c19c40155d890af484d5d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822336"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Azure Static Web Apps의 경로 미리 보기

Azure 정적 Web Apps의 라우팅은 정적 콘텐츠와 Api<sup>1</sup>에 대 한 백 엔드 라우팅 규칙 및 권한 부여 동작을 정의 합니다. 규칙은 _routes.json_ 파일에서 규칙의 배열로 정의됩니다.

- _routes.json_ 파일은 앱의 빌드 아티팩트 폴더의 루트에 있어야 합니다.
- 규칙은 `routes` 배열에 표시되는 순서대로 실행됩니다.
- 첫 번째 일치 항목에서 규칙 평가를 중지합니다. 라우팅 규칙은 함께 연결되지 않습니다.
- 역할은 _routes.json_ 파일에 정의되고 사용자는 [초대](authentication-authorization.md)를 통해 역할에 연결됩니다.
- 역할 이름을 완전히 제어할 수 있습니다.

라우팅 항목은 인증 및 권한 부여 개념과 크게 겹칩니다. 이 문서와 함께 [인증 및 권한 부여](authentication-authorization.md) 가이드를 참조하세요.

자세한 내용은 [예제 경로 파일](#example-route-file) 을 참조 하세요.

## <a name="location"></a>위치

_routes.json_ 파일은 앱의 빌드 아티팩트 폴더의 루트에 있어야 합니다. 웹앱에 빌드된 파일을 특정 폴더에서 빌드 아티팩트 폴더로 복사하는 빌드 단계가 포함되어 있는 경우 _routes.json_ 파일은 해당 특정 폴더에 있어야 합니다.

다음 표에서는 다양 한 프런트 엔드 프레임 워크 및 라이브러리에 대 한 _routes.js파일에_ 배치할 수 있는 적절 한 위치를 보여 줍니다.

|프레임워크 / 라이브러리 | 위치  |
|---------|----------|
| Angular | _assets_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

위의 표는 Azure Static Web Apps와 호환 되는 몇 가지 프레임 워크 및 라이브러리를 대표 합니다. 자세한 내용은 [프런트 엔드 프레임 워크 및 라이브러리 구성](./front-end-frameworks.md) 을 참조 하세요.

## <a name="defining-routes"></a>경로 정의

경로는 _routes.json_ 파일에서 `routes` 속성의 경로 규칙 배열로 정의됩니다. 각 규칙은 하나 이상의 선택적 규칙 속성과 함께 경로 패턴으로 구성됩니다. 사용 예제는 [예제 경로 파일](#example-route-file)을 참조하세요.

| 규칙 속성  | 필수 | 기본값 | 주석                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | 예      | 해당 없음          | 호출자가 요청한 경로 패턴입니다.<ul><li>[와일드카드](#wildcards)는 경로 경로(route path)의 끝에서 지원됩니다. 예를 들어 경로 _admin/\*_ 은 _admin_ 경로 아래에 있는 경로와 일치합니다.<li>경로의 기본 파일은 _index.html_입니다.</ul>|
| `serve`        | 예       | 해당 없음          | 요청에서 반환된 파일 또는 경로를 정의합니다. 파일 경로 및 이름은 요청된 경로와 다를 수 있습니다. `serve`값이 정의 되지 않은 경우 요청 된 경로가 사용 됩니다. Querystring 매개 변수는 지원 되지 않습니다. `serve` 값은 실제 파일을 가리켜야 합니다.  |
| `allowedRoles` | 예       | 익명     | 역할 이름의 배열입니다. <ul><li>유효한 문자에는 `a-z`, `A-Z`, `0-9` 및 `_`가 포함됩니다.<li>기본 제공 역할 `anonymous`는 인증되지 않은 모든 사용자에게 적용됩니다.<li>기본 제공 역할 `authenticated`는 로그인한 사용자에게 적용됩니다.<li>사용자는 하나 이상의 역할에 속해야 합니다.<li>역할은 _OR_ 기준으로 일치합니다. 사용자가 나열된 역할 중 하나에 있는 경우 액세스 권한이 부여됩니다.<li>개별 사용자는 [초대](authentication-authorization.md)를 통해 역할에 연결됩니다.</ul> |
| `statusCode`   | 예       | 200           | 요청에 대한 [HTTP 상태 코드](https://wikipedia.org/wiki/List_of_HTTP_status_codes) 응답입니다. |

## <a name="securing-routes-with-roles"></a>역할을 사용하여 경로 보호

규칙의 `allowedRoles` 배열에 하나 이상의 역할 이름을 추가하여 경로를 보호합니다. 사용 예제는 [예제 경로 파일](#example-route-file)을 참조하세요.

기본적으로 모든 사용자는 기본 제공 `anonymous` 역할에 속하며 로그인된 모든 사용자는 `authenticated` 역할의 멤버입니다. 예를 들어 인증된 사용자로만 경로를 제한하려면 기본 제공 `authenticated` 역할을 `allowedRoles` 배열에 추가합니다.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

`allowedRoles` 배열에서 필요에 따라 새 역할을 만들 수 있습니다. 관리자로만 경로를 제한하기 위해 `allowedRoles` 배열에 `administrator` 역할을 정의할 수 있습니다.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- 역할 이름을 완전히 제어할 수 있습니다. 역할이 준수해야 하는 마스터 목록이 없습니다.
- 개별 사용자는 [초대](authentication-authorization.md)를 통해 역할에 연결됩니다.

## <a name="wildcards"></a>와일드카드

와일드카드 규칙은 지정된 경로 패턴의 모든 요청을 일치시킵니다. 규칙에 `serve` 값을 정의하는 경우 명명된 파일 또는 경로가 응답으로 제공됩니다.

예를 들어 일정 애플리케이션에 대한 경로를 구현하려면 _일정_ 경로에 속하는 모든 URL을 단일 파일에 매핑할 수 있습니다.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

그런 다음, _calendar.html_ 파일은 클라이언트 쪽 라우팅을 사용하여 `/calendar/january/1`, `/calendar/2020` 및 `/calendar/overview`와 같은 URL 변형에 대해 다른 보기를 제공할 수 있습니다.

와일드카드를 사용하여 경로를 보호할 수도 있습니다. 다음 예제에서는 _관리자_ 경로에서 요청된 모든 파일에 _관리자_ 역할의 멤버인 인증된 사용자가 필요합니다.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> 제공된 파일에서 참조하는 파일에 대한 요청은 인증 확인에 대해서만 평가됩니다. 예를 들어 와일드카드 경로 아래의 CSS 파일에 대한 요청은 CSS 파일을 제공하고, `serve` 파일로 정의된 것은 아닙니다. 사용자가 필요한 인증 요구 사항을 충족하기만 하면 CSS 파일이 제공됩니다.

## <a name="fallback-routes"></a>대체 경로

단일 페이지 응용 프로그램은 프런트 엔드 JavaScript 프레임 워크 또는 라이브러리를 사용 하 든, 아니면 Blazor와 같은 Weasembomplatform을 사용 하는지에 관계 없이 웹 앱 탐색을 위한 클라이언트 쪽 라우팅을 사용 하는 경우가 많습니다. 이러한 클라이언트 쪽 라우팅 규칙은 서버에 다시 요청하지 않고 브라우저의 창 위치를 업데이트합니다. 페이지를 새로 고치거나 클라이언트 쪽 라우팅 규칙에 의해 생성된 위치로 직접 이동하는 경우 적절한 HTML 페이지를 제공하기 위해 서버 쪽 대체 경로가 필요합니다.

일반적인 대체 경로는 다음 예제에 나와 있습니다.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

대체 경로는 이전에 정의된 규칙에 의해 catch되지 않은 모든 요청을 catch하므로 라우팅 규칙에서 마지막에 나열되어야 합니다.

## <a name="redirects"></a>리디렉션

[301](https://en.wikipedia.org/wiki/HTTP_301) 및 [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP 상태 코드를 사용하여 한 경로의 요청을 다른 경로로 리디렉션할 수 있습니다.

예를 들어 다음 규칙은 _old-page.html_에서 _new-page.html_로의 301 리디렉션을 만듭니다.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

리디렉션은 고유 파일을 정의하지 않는 경로에도 적용됩니다.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>사용자 지정 오류 페이지

사용자는 오류가 발생할 수 있는 다양한 상황을 경험할 수 있습니다. `platformErrorOverrides` 배열을 사용하면 이러한 오류에 대한 응답으로 사용자 지정 환경을 제공할 수 있습니다. _routes.json_ 파일에 배열을 배치하는 방법에 대해 [예제 경로 파일](#example-route-file)을 참조하세요.

> [!NOTE]
> 요청에서 플랫폼 재정의 수준으로 설정 하면 경로 규칙이 다시 실행 되지 않습니다.

다음 표에는 사용 가능한 플랫폼 오류 재정의가 나열되어 있습니다.

| 오류 유형  | HTTP 상태 코드 | Description |
|---------|---------|---------|
| `NotFound` | 404  | 서버에서 페이지를 찾을 수 없습니다. |
| `Unauthenticated` | 401 | 사용자가 [인증 공급자](authentication-authorization.md)로 로그인하지 않았습니다. |
| `Unauthorized_InsufficientUserInformation` | 401 | 인증 공급자의 사용자 계정이 필수 데이터를 노출하도록 구성되어 있지 않습니다. 이 오류는 앱이 인증 공급자에게 사용자의 이메일 주소를 요청했지만 사용자가 이메일 주소에 대한 액세스를 제한하도록 선택한 경우에 발생할 수 있습니다. |
| `Unauthorized_InvalidInvitationLink` | 401 | 초대가 만료되었거나 사용자가 다른 받는 사람에 대해 생성된 초대 링크를 따라 이동했습니다.  |
| `Unauthorized_MissingRoles` | 401 | 사용자가 필요한 역할의 멤버가 아닙니다. |
| `Unauthorized_TooManyUsers` | 401 | 사이트가 최대 사용자 수에 도달하여 서버에서 추가 추가를 제한하고 있습니다. 이 오류는 생성할 수 있는 [초대](authentication-authorization.md) 수에 제한이 없기 때문에 클라이언트에 노출되며, 일부 사용자가 초대를 수락하지 못할 수 있습니다.|
| `Unauthorized_Unknown` | 401 | 사용자를 인증하는 동안 알 수 없는 문제가 발생했습니다. 이 오류가 발생하는 원인 중 하나는 애플리케이션에 동의하지 않아 사용자가 인식되지 못할 수 있습니다.|

## <a name="custom-mime-types"></a>사용자 지정 mime 형식

`mimeTypes`배열과 같은 수준에 있는 개체를 사용 하 여 `routes` [MIME 형식을](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) 파일 확장명과 연결할 수 있습니다.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

위의 예제에서는 확장명이 인 모든 파일이 `.custom` MIME 형식으로 제공 됩니다 `text/html` .

MIME 형식으로 작업 하는 경우 다음 사항을 고려해 야 합니다.

- 키는 null 이거나 비워 두거나 50 자를 초과할 수 없습니다.
- 값은 null 이거나 비어 있거나 1000 자를 초과할 수 없습니다.

> [!NOTE]
> 정적 Web Apps는 Blazor 응용 프로그램과 WASM 및 DLL 파일에 대해 예상 되는 MIME 형식을 이해 하므로 이러한 응용 프로그램에 대 한 매핑을 추가할 필요가 없습니다.

## <a name="default-headers"></a>기본 헤더

`defaultHeaders`배열과 같은 수준에 나열 된 개체를 사용 하 여 `routes` [응답 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers)를 추가, 수정 또는 제거할 수 있습니다.

헤더에 대 한 값을 제공 하면 헤더를 추가 하거나 수정 합니다. 빈 값을 제공 하면 클라이언트에 제공 되는 헤더를 제거 합니다.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

위의 예에서는 새 헤더를 추가 하 고 `content-security-policy` ,는 `cache-control` 서버 기본값을 수정 하며, `x-dns-prefectch-control` 헤더가 제거 됩니다.

헤더를 사용할 때 다음 사항을 고려해 야 합니다.

- 키는 null 이거나 비워 둘 수 없습니다.
- Null 또는 빈 값은 처리에서 헤더를 제거 합니다.
- 키 또는 값은 8000 자를 초과할 수 없습니다.
- 정의 된 헤더는 모든 요청에서 제공 됩니다.
- _routes.js_ 에 정의 된 헤더는 정적 내용에만 적용 됩니다. 함수 코드에서 API 끝점의 응답 헤더를 사용자 지정할 수 있습니다.

## <a name="example-route-file"></a>예제 경로 파일

다음 예제에서는 _routes.json_ 파일에서 정적 콘텐츠 및 API에 대한 경로 규칙을 빌드하는 방법을 보여 줍니다. 일부 경로는 인증 관련 엔드포인트에 액세스하는 [ _/.auth_ 시스템 폴더](authentication-authorization.md)를 사용합니다.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

다음 예제에서는 요청이 규칙과 일치하는 경우 발생하는 상황을 설명합니다.

| 다음에 대한 요청... | 결과... |
|--|--|--|
| _/profile_ | 인증된 사용자에게는 _/profile/index.html_ 파일이 제공됩니다. _/login_으로 리디렉션된 인증되지 않은 사용자입니다. |
| _/admin/reports_ | _관리자_ 역할의 인증된 사용자에게는 _/admin/reports/index.html_ 파일이 제공됩니다. _관리자_ 역할에 없는 인증 된 사용자는 401 오류<sup>2</sup>를 제공 합니다. _/login_으로 리디렉션된 인증되지 않은 사용자입니다. |
| _/api/admin_ | _관리자_ 역할의 인증된 사용자 요청이 API로 전송됩니다. _관리자_ 역할에 없는 인증된 사용자 및 인증되지 않은 사용자에게는 401 오류가 제공됩니다. |
| _/customers/contoso_ | _관리자_ 또는 _고객 \_ contoso_ 역할에 속하는 인증 된 사용자는 _/customers/contoso/index.html_ 파일<sup>2</sup>를 제공 합니다. _관리자_ 또는 _고객\_contoso_ 역할에 없는 인증된 사용자에게는 401 오류가 제공됩니다. _/login_으로 리디렉션된 인증되지 않은 사용자입니다. |
| _/login_ | 인증되지 않은 사용자는 GitHub를 사용하여 인증해야 합니다. |
| _/.auth/login/twitter_ | Twitter를 사용하여 권한 부여를 사용할 수 없습니다. 서버가 404 오류로 응답합니다. |
| _/logout_ | 사용자는 모든 인증 공급자에서 로그아웃됩니다. |
| _/calendar/2020/01_ | 브라우저에는 _/calendar.html_ 파일이 제공됩니다. |
| _/specials_ | 브라우저가 _/deals_로 리디렉션됩니다. |
| _/unknown-folder_ | _/custom-404.html_ 파일이 제공됩니다. |
| 확장명이 있는 파일 `.custom` | MIME 형식으로 제공 됩니다. `text/html` |

모든 응답에는 `content-security-policy` 값이 인 헤더가 포함 됩니다 `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> API 함수에 대 한 경로 규칙은 역할이 있는 [리디렉션](#redirects) 및 [보안 경로만](#securing-routes-with-roles)지원 합니다.

<sup>2</sup> 배열에서 규칙을 정의 하 여 사용자 지정 오류 페이지를 제공할 수 있습니다 `Unauthorized_MissingRoles` `platformErrorOverrides` .

## <a name="restrictions"></a>제한

- _routes.json_ 파일은 100KB를 초과할 수 없습니다.
- _routes.json_ 파일은 최대 50개의 고유한 역할을 지원합니다.

일반적인 제한 사항 및 제한 사항은 [할당량 문서](quotas.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증 및 권한 부여 설정](authentication-authorization.md)

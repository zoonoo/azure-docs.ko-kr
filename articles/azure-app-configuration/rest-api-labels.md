---
title: Azure App Configuration REST API - 레이블
description: Azure App Configuration REST API를 사용한 레이블 작업을 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932509"
---
# <a name="labels"></a>레이블

api-version: 1.0

**레이블** 리소스는 다음과 같이 정의됩니다.

```json
{
      "name": [string]             // Name of the label
}
```

다음 작업을 지원합니다.

- 목록

모든 작업에 대해 ``name``은 선택적 필터 매개 변수입니다. 생략하는 경우 **모든** 레이블을 의미합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 모든 HTTP 요청은 인증되어야 합니다. [인증](./rest-api-authentication-index.md) 섹션을 참조하세요.
- 모든 HTTP 요청은 명시적으로 `api-version`을 제공해야 합니다. [버전 관리](./rest-api-versioning.md) 세션을 참조하세요.

## <a name="list-labels"></a>레이블 나열

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>페이지 매김

반환되는 항목 수가 응답 제한을 초과하면 결과에 페이지가 매겨집니다. 선택적 `Link` 응답 헤더를 따르고 탐색에 `rel="next"`를 사용합니다. 또는 콘텐츠가 `@nextLink` 속성의 형식으로 다음 링크를 제공합니다. 다음 링크에는 `api-version` 매개 변수가 포함됩니다.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>필터링

`name`에 의한 필터링이 지원됩니다.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|영향|
|--|--|
|`name`은 생략 또는 `name=*`|**모든** 레이블과 일치|
|`name=abc`|**abc** 라는 이름의 레이블과 일치|
|`name=abc*`|**abc** 로 시작하는 레이블 이름과 일치|
|`name=abc,xyz`|5 CSV로 제한된 **abc** 또는 **xyz** 레이블 이름과 일치|

### <a name="reserved-characters"></a>예약 문자

`*`, `\`, `,`

예약된 문자가 값의 일부인 경우, `\{Reserved Character}`을 사용하여 이스케이프 처리해야 합니다. 예약되지 않은 문자도 이스케이프할 수 있습니다.

### <a name="filter-validation"></a>필터 유효성 검사

필터 유효성 검사 오류가 발생하는 경우, 응답은 오류 세부 정보가 있는 HTTP `400`입니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>예

- 모두

    ```http
    GET /labels?api-version={api-version}
    ```

- 레이블 키 이름이 **abc** 로 시작합니다.

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- 레이블 이름이 **abc** 또는 **xyz** 입니다.

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>요청 관련 필드

선택적 `$select` 쿼리 문자열 매개 변수를 사용하고 요청된 필드의 쉼표로 구분된 목록을 제공합니다. `$select` 매개 변수를 생략하면 응답에 기본 세트가 포함됩니다.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>시간 기반 액세스

과거와 같은 결과의 표시를 얻습니다. 섹션 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)를 참조하세요.

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

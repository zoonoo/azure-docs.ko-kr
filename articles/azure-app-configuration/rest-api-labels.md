---
title: Azure 앱 구성 REST API-레이블
description: Azure 앱 구성을 사용 하 여 레이블로 작업 하기 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e6fcc8399c1dbc36a5b2e915c726f1c2496ee2f9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424129"
---
# <a name="labels"></a>레이블

api-version: 1.0

**레이블** 리소스는 다음과 같이 정의 됩니다.

```json
{
      "name": [string]             // Name of the label
}
```

에서는 다음 작업을 지원 합니다.

- 목록

모든 작업에 ``name`` 는 선택적 필터 매개 변수가 있습니다. 생략 하는 경우 **모든** 레이블을 의미 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 모든 HTTP 요청을 인증 해야 합니다. [인증](./rest-api-authentication-index.md) 섹션을 참조 하세요.
- 모든 HTTP 요청은 explicit를 제공 해야 합니다 `api-version` . [버전 관리](./rest-api-versioning.md) 섹션을 참조 하세요.

## <a name="list-labels"></a>레이블 나열

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**보낸**

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

반환 된 항목 수가 응답 제한을 초과 하는 경우 결과의 페이지가 매겨집니다. 선택적 `Link` 응답 헤더를 따르고 `rel="next"` 탐색에 사용 합니다. 또는 콘텐츠가 속성의 형식으로 다음 링크를 제공 합니다 `@nextLink` . 다음 링크에는 `api-version` 매개 변수가 포함 됩니다.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**응답이**

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

필터링 `name` 이 지원 됩니다.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|효과|
|--|--|
|`name`은 생략 또는 `name=*`|**모든** 레이블과 일치|
|`name=abc`|**Abc** 라는 레이블을 찾습니다.|
|`name=abc*`|**Abc** 로 시작 하는 레이블 이름과 일치|
|`name=abc,xyz`|레이블 이름 **abc** 또는 **xyz** (CSV 5 개로 제한 됨)와 일치|

### <a name="reserved-characters"></a>예약 문자

`*`, `\`, `,`

예약 된 문자가 값의 일부인 경우를 사용 하 여 이스케이프 처리 해야 합니다 `\{Reserved Character}` . 예약 되지 않은 문자는 이스케이프할 수도 있습니다.

### <a name="filter-validation"></a>필터 유효성 검사

필터 유효성 검사 오류가 발생 하는 경우 응답은 HTTP 이며 `400` 오류 정보는 다음과 같습니다.

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

### <a name="examples"></a>예제

- 모두

    ```http
    GET /labels?api-version={api-version}
    ```

- 레이블 이름이 **abc** 로 시작 합니다.

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- 레이블 이름은 **abc** 또는 **xyz** 중 하나입니다.

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>특정 필드 요청

선택적 `$select` 쿼리 문자열 매개 변수를 사용 하 고 요청 된 필드의 쉼표로 구분 된 목록을 제공 합니다. `$select`매개 변수가 생략 된 경우 응답은 기본 집합을 포함 합니다.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based 액세스

이전에 발생 한 결과의 표현을 가져옵니다. [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) 섹션을 참조 하세요.

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답이**

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

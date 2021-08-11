---
title: Azure App Configuration REST API - 키 값
description: Azure App Configuration REST API를 사용하여 키-값으로 작업하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932560"
---
# <a name="key-values"></a>키-값

키-값은 `key` + `label`의 고유한 조합으로 식별되는 리소스입니다. `label`는 선택 사항입니다. 레이블 없이 키-값을 명시적으로 참조하려면 "\0"(``%00``으로 인코딩된 URL)을 사용합니다. 각 작업에 대한 세부 정보를 참조하세요.

이 문서는 API 버전 1.0에 적용됩니다.

## <a name="operations"></a>작업

- 가져오기
- 여러 항목 나열
- 설정
- DELETE

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>구문

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>키-값 가져오기

필수: ``{key}``, ``{api-version}``  
옵션: ``label``(생략한 경우 레이블이 없는 키 값 의미)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

키가 없으면 다음 응답이 반환됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>가져오기(조건부)

클라이언트 캐싱을 향상시키려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용합니다. `etag` 인수는 키 표현의 일부입니다. 자세한 내용은 [섹션 14.24 및 14.26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조하세요.

다음 요청은 현재 표현이 지정된 `etag`와 일치하지 않는 경우에만 키 값을 검색합니다.

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**응답:**

```http
HTTP/1.1 304 NotModified
```

또는

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>키-값 나열

옵션: ``key``(지정하지 않은 경우 임의 키 의미) 옵션: ``label``(지정하지 않은 경우 임의 레이블 의미)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

추가 옵션에 대해서는 이 문서의 뒷부분에 나오는 "필터링" 섹션을 참조하세요.

## <a name="pagination"></a>페이지 매김

반환되는 항목 수가 응답 제한을 초과하면 결과에 페이지가 매겨집니다. 선택적 `Link` 응답 헤더를 따르고 탐색을 위해 `rel="next"`를 사용합니다.
또는 콘텐츠가 `@nextLink` 속성의 형식으로 다음 링크를 제공합니다. 연결된 URI는 `api-version` 인수를 포함합니다.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

`key`및 `label` 필터링의 조합이 지원됩니다.
선택적 `key` 및 `label` 쿼리 문자열 매개 변수를 사용합니다.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|영향|
|--|--|
|`key`은 생략 또는 `key=*`|**모든** 키와 일치|
|`key=abc`|**abc** 라는 이름의 키와 일치|
|`key=abc*`|**abc** 로 시작하는 키 이름과 일치|
|`key=abc,xyz`|키 이름 **abc** 또는 **xyz**(5 CSV로 제한)와 일치|

|레이블 필터|영향|
|--|--|
|`label`은 생략 또는 `label=*`|**모든** 레이블과 일치|
|`label=%00`|레이블 없는 KV와 일치|
|`label=prod`|**prod** 레이블과 일치|
|`label=prod*`|**prod** 로 시작하는 레이블과 일치|
|`label=prod,test`|레이블 **prod** 또는 **test**(5 CSV로 제한)와 일치|

***예약 문자***

`*`, `\`, `,`

예약된 문자가 값의 일부인 경우, `\{Reserved Character}`를 사용하여 이스케이프 처리해야 합니다. 예약되지 않은 문자도 이스케이프할 수 있습니다.

***필터 유효성 검사***

필터 유효성 검사 오류의 경우, 응답은 오류 세부 정보가 있는 HTTP `400`입니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

**예제**

- 모두

    ```http
    GET /kv?api-version={api-version}
    ```

- 키 이름은 **abc** 로 시작하고 모든 레이블을 포함합니다.

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- 키 이름은 **abc** 로 시작하고 레이블은 **v1** 또는 **v2** 와 같습니다.

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>요청 관련 필드

선택적 `$select` 쿼리 문자열 매개 변수를 사용하고 요청된 필드의 쉼표로 구분된 목록을 제공합니다. `$select` 매개 변수를 생략하면 응답에 기본 세트가 포함됩니다.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>시간 기반 액세스

과거와 같은 결과의 표시를 얻습니다. 자세한 내용은 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) 섹션을 참조하세요. 페이지 매김은 이 문서의 앞부분에서 정의한 대로 계속 지원됩니다.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>키 설정

- 필수: ``{key}``
- 옵션: ``label``(지정하지 않는 경우 또는 레이블 = %00, 레이블 없는 키-값 의미)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

항목이 잠겨 있으면 다음 응답을 받게 됩니다.

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>키 설정(조건부)

경합 상태를 방지하려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용합니다. `etag` 인수는 키 표현의 일부입니다.
`If-Match` 또는 `If-None-Match`가 생략된 경우에는 작업이 무조건 수행됩니다.

다음 응답은 현재 표현이 지정된 `etag`와 일치하는 경우에만 값을 업데이트합니다.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 응답은 현재 표현이 지정된 `etag`와 일치하지 않는 경우에만 값을 업데이트합니다.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 요청은 표현이 이미 있는 경우에만 값을 추가합니다.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

다음 요청은 표현이 아직 없는 경우에만 값을 추가합니다.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**응답**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

또는

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>DELETE

- 필수: `{key}`, `{api-version}`
- 옵션: `{label}`(지정하지 않는 경우 또는 레이블 = %00, 레이블 없는 키-값 의미)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**응답:** 삭제된 키-값을 반환하거나, 키-값이 없는 경우 none을 반환합니다.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

또는

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>키 삭제(조건부)

이 문서의 앞부분에 있는 "키 설정(조건부)" 섹션과 유사합니다.

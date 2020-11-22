---
title: Azure 앱 구성 REST API-키-값
description: Azure 앱 구성을 사용 하 여 키-값 작업을 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f89b3f2fa4805eeb2fd9f9d511c8f228b98139ac
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241032"
---
# <a name="key-values"></a>키-값

키-값은의 고유한 조합으로 식별 되는 리소스입니다 `key`  +  `label` . `label`는 선택 사항입니다. 레이블 없이 키-값을 명시적으로 참조 하려면 "\ 0" (로 인코딩된 URL)을 사용 ``%00`` 합니다. 각 작업에 대 한 세부 정보를 참조 하세요.

이 문서는 API 버전 1.0에 적용 됩니다.

## <a name="operations"></a>작업

- 가져오기
- 여러 개 나열
- 설정
- 삭제

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

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

필수: ``{key}`` , ``{api-version}``  
선택 사항: 생략 하는 ``label`` 경우 레이블이 없는 키 값을 의미 합니다.

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**보낸**

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

키가 없으면 다음 응답이 반환 됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Get (조건부로)

클라이언트 캐싱을 향상 시키려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용 합니다. `etag`인수는 키 표현의 일부입니다. 자세한 내용은 [섹션 14.24 및 14.26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조 하세요.

다음 요청은 현재 표현이 지정 된와 일치 하지 않는 경우에만 키 값을 검색 합니다 `etag` .

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**보낸**

```http
HTTP/1.1 304 NotModified
```

또는

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>키-값 나열

선택 사항: ``key`` (지정 하지 않은 경우 모든 키를 의미 합니다.) 선택 사항: ``label`` (지정 하지 않은 경우 레이블이 있음을 의미 합니다.)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**응답이**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

추가 옵션에 대해서는이 문서의 뒷부분에 나오는 "필터링" 섹션을 참조 하세요.

## <a name="pagination"></a>페이지 매김

반환 된 항목 수가 응답 제한을 초과 하는 경우 결과의 페이지가 매겨집니다. 선택적 `Link` 응답 헤더를 따르고 `rel="next"` 탐색에를 사용 합니다.
또는 콘텐츠가 속성의 형식으로 다음 링크를 제공 합니다 `@nextLink` . 연결 된 URI는 인수를 포함 합니다 `api-version` .

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**응답이**

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

`key`와 필터링을 함께 `label` 사용할 수 있습니다.
선택적 `key` 및 `label` 쿼리 문자열 매개 변수를 사용 합니다.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|영향|
|--|--|
|`key`은 생략 또는 `key=*`|**모든** 키를 찾습니다.|
|`key=abc`|**Abc** 라는 키를 찾습니다.|
|`key=abc*`|**Abc** 로 시작 하는 키 이름과 일치|
|`key=abc,xyz`|키 이름 **abc** 또는 **xyz** (CSV 5 개로 제한 됨)를 찾습니다.|

|레이블 필터|영향|
|--|--|
|`label`은 생략 또는 `label=*`|**모든** 레이블과 일치|
|`label=%00`|레이블 없이 KV 일치|
|`label=prod`|**Prod** 레이블과 일치 합니다.|
|`label=prod*`|**Prod** 로 시작 하는 레이블과 일치|
|`label=prod,test`|레이블 **prod** 또는 **테스트** (5 개의 CSV로 제한 됨)와 일치|

**_예약 된 문자_* _

`_`, `\`, `,`

예약 된 문자가 값의 일부인 경우를 사용 하 여 이스케이프 처리 해야 합니다 `\{Reserved Character}` . 예약 되지 않은 문자는 이스케이프할 수도 있습니다.

***필터 유효성 검사** _

필터 유효성 검사 오류가 발생 하는 경우 응답은 HTTP 이며 `400` 오류 정보는 다음과 같습니다.

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

_ *예**

- 모두

    ```http
    GET /kv?api-version={api-version}
    ```

- 키 이름이 **abc** 로 시작 하 고 모든 레이블을 포함 합니다.

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- 키 이름이 **abc** 로 시작 하 고 레이블이 **v1** 또는 **v2** 와 같음

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>특정 필드 요청

선택적 `$select` 쿼리 문자열 매개 변수를 사용 하 고 요청 된 필드의 쉼표로 구분 된 목록을 제공 합니다. `$select`매개 변수가 생략 된 경우 응답은 기본 집합을 포함 합니다.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>시간 기반 액세스

이전에 발생 한 결과의 표현을 가져옵니다. 자세한 내용은 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)섹션을 참조 하세요. 페이지 매김은이 문서의 앞부분에서 정의한 대로 계속 지원 됩니다.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답이**

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
- 선택 사항: ``label`` (지정 하지 않는 경우 또는 레이블 = %00, 레이블 없는 키-값을 의미 함)

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

**보낸**

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

## <a name="set-key-conditionally"></a>Set key (조건부로)

경합 상태를 방지 하려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용 합니다. `etag`인수는 키 표현의 일부입니다.
또는이 생략 된 경우에는 `If-Match` `If-None-Match` 작업이 무조건 수행 됩니다.

다음 응답은 현재 표현이 지정 된와 일치 하는 경우에만 값을 업데이트 합니다 `etag` .

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 응답은 현재 표현이 지정 된와 일치 하지 않는 경우에만 값을 업데이트 합니다 `etag` .

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 요청은 표현이 이미 있는 경우에만 값을 추가 합니다.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

다음 요청은 표현이 아직 없는 경우에만 값을 추가 합니다.

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

## <a name="delete"></a>삭제

- 필수: `{key}` , `{api-version}`
- 선택 사항: `{label}` (지정 하지 않는 경우 또는 레이블 = %00, 레이블 없는 키-값을 의미 함)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**응답:** 삭제 된 키 값을 반환 하거나, 키 값이 없는 경우 none을 반환 합니다.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

또는

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>키 삭제 (조건부로)

이는이 문서의 앞부분에 있는 "키 설정 (조건부로)" 섹션과 유사 합니다.

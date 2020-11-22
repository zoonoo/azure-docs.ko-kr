---
title: Azure 앱 구성 REST API-키-값 수정 버전
description: Azure 앱 구성을 사용 하 여 키-값 수정 버전으로 작업 하기 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 668345da8bb89412f7b1dd36975c5bed6f229580
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246387"
---
# <a name="key-value-revisions"></a>키-값 수정 버전

*키-값 수정 버전* 은 키-값 리소스의 기록 표현을 정의 합니다. 개정판은 무료 계층 매장에 대해 7 일 후에 만료 되거나 표준 계층 상점의 경우 30 일 후에 만료 됩니다. 수정 버전은 `List` 작업을 지원 합니다.

모든 작업에 대해 ``key`` 은 선택적 매개 변수입니다. 생략 하는 경우 모든 키를 의미 합니다.

모든 작업에 대해 ``label`` 은 선택적 매개 변수입니다. 생략 하는 경우 모든 레이블을 의미 합니다.

이 문서는 API 버전 1.0에 적용 됩니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>수정 버전 나열

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**보낸**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>페이지 매김

반환 된 항목 수가 응답 제한을 초과 하는 경우 결과의 페이지가 매겨집니다. 선택적 ``Link`` 응답 헤더를 따르고 ``rel="next"`` 탐색에 사용 합니다. 또는 콘텐츠가 속성의 형식으로 다음 링크를 제공 합니다 ``@nextLink`` .

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**응답이**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

## <a name="list-subset-of-revisions"></a>수정 버전 나열

`Range`요청 헤더를 사용 합니다. 응답에 `Content-Range` 헤더가 포함됩니다. 서버가 요청 된 범위를 만족할 수 없는 경우 HTTP `416` ()로 응답 `RangeNotSatisfiable` 합니다.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>필터링

`key`와 필터링을 함께 `label` 사용할 수 있습니다.
선택적 `key` 및 `label` 쿼리 문자열 매개 변수를 사용 합니다.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|영향|
|--|--|
|`key`은 생략 또는 `key=*`|**모든** 키를 찾습니다.|
|`key=abc`|**Abc** 라는 키를 찾습니다.|
|`key=abc*`|**Abc** 로 시작 하는 키 이름과 일치|
|`key=*abc`|**Abc** 로 끝나는 키 이름과 일치|
|`key=*abc*`|**Abc** 를 포함 하는 키 이름과 일치|
|`key=abc,xyz`|키 이름 **abc** 또는 **xyz** (CSV 5 개로 제한 됨)를 찾습니다.|

|레이블 필터|영향|
|--|--|
|`label`은 생략 또는 `label=`|레이블이 없는 항목과 일치|
|`label=*`|**모든** 레이블과 일치|
|`label=prod`|**Prod** 레이블과 일치 합니다.|
|`label=prod*`|**Prod** 로 시작 하는 레이블과 일치|
|`label=*prod`|**Prod** 로 끝나는 레이블과 일치|
|`label=*prod*`|**Prod** 가 포함 된 레이블과 일치|
|`label=prod,test`|레이블 **prod** 또는 **테스트** (5 개의 CSV로 제한 됨)와 일치|

### <a name="reserved-characters"></a>예약 문자

예약 된 문자는 다음과 같습니다.

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
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>예제

- 전체:

    ```http
    GET /revisions
    ```

- 키 이름이 **abc** 로 시작 하는 항목:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- 키 이름이 **abc** 또는 **xyz** 이 고 레이블에 **prod** 가 포함 된 항목입니다.

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>특정 필드 요청

선택적 `$select` 쿼리 문자열 매개 변수를 사용 하 고 요청 된 필드의 쉼표로 구분 된 목록을 제공 합니다. `$select`매개 변수가 생략 된 경우 응답은 기본 집합을 포함 합니다.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>시간 기반 액세스

이전에 발생 한 결과의 표현을 가져옵니다. 자세한 내용은 [리소스 상태에 대 한 Time-Based 액세스에 대 한 HTTP 프레임 워크--Memento](https://tools.ietf.org/html/rfc7089#section-2.1), section 2.1.1를 참조 하세요.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답이**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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

---
title: Azure App Configuration REST API - 키 값 수정 버전
description: Azure App Configuration REST API를 사용하여 키-값 수정 버전으로 작업하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932475"
---
# <a name="key-value-revisions"></a>키-값 수정 버전

*키-값 수정 버전* 은 키-값 리소스의 기록 표현을 정의합니다. 수정 버전은 무료 계층 저장소의 경우 7일 후에 만료되고, 표준 계층 저장소의 경우 30일 후에 만료됩니다. 수정 버전은 `List` 작업을 지원합니다.

모든 작업에 대해 ``key``는 선택적 매개 변수입니다. 생략하는 경우 모든 키를 의미합니다.

모든 작업에 대해 ``label``는 선택적 매개 변수입니다. 생략하는 경우 모든 레이블을 의미합니다.

이 문서는 API 버전 1.0 이상에 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>수정 버전 나열

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**응답:**

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

반환되는 항목 수가 응답 제한을 초과하면 결과에 페이지가 매겨집니다. 선택적 ``Link`` 응답 헤더를 따르고 탐색에 ``rel="next"``를 사용합니다. 또는 콘텐츠가 ``@nextLink`` 속성의 형식으로 다음 링크를 제공합니다.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**응답:**

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

## <a name="list-subset-of-revisions"></a>수정 버전 하위 집합 나열

`Range` 요청 헤더를 사용합니다. 응답에 `Content-Range` 헤더가 포함됩니다. 서버가 요청된 범위를 만족할 수 없는 경우 HTTP `416`(`RangeNotSatisfiable`)으로 응답합니다.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**응답**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>필터링

`key`및 `label` 필터링의 조합이 지원됩니다.
선택적 `key` 및 `label` 쿼리 문자열 매개 변수를 사용합니다.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>지원되는 필터

|키 필터|영향|
|--|--|
|`key`은 생략 또는 `key=*`|**모든** 키와 일치|
|`key=abc`|**abc** 라는 이름의 키와 일치|
|`key=abc*`|**abc** 로 시작하는 키 이름과 일치|
|`key=*abc`|**abc** 로 끝나는 키 이름과 일치|
|`key=*abc*`|**abc** 를 포함하는 키 이름과 일치|
|`key=abc,xyz`|키 이름 **abc** 또는 **xyz**(5 CSV로 제한)와 일치|

|레이블 필터|영향|
|--|--|
|`label`은 생략 또는 `label=`|레이블이 없는 항목과 일치|
|`label=*`|**모든** 레이블과 일치|
|`label=prod`|**prod** 레이블과 일치|
|`label=prod*`|**prod** 로 시작하는 레이블과 일치|
|`label=*prod`|**prod** 로 끝나는 레이블과 일치|
|`label=*prod*`|**prod** 를 포함하는 레이블과 일치|
|`label=prod,test`|레이블 **prod** 또는 **test**(5 CSV로 제한)와 일치|

### <a name="reserved-characters"></a>예약 문자

예약된 문자는 다음과 같습니다.

`*`, `\`, `,`

예약된 문자가 값의 일부인 경우, `\{Reserved Character}`를 사용하여 이스케이프 처리해야 합니다. 예약되지 않은 문자도 이스케이프할 수 있습니다.

### <a name="filter-validation"></a>필터 유효성 검사

필터 유효성 검사 오류가 발생하는 경우, 응답은 오류 세부 정보가 있는 HTTP `400`입니다.

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

### <a name="examples"></a>예

- 전체:

    ```http
    GET /revisions
    ```

- 키 이름이 **abc** 로 시작하는 항목:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- 키 이름이 **abc** 또는 **xyz** 이고 레이블에 **prod** 가 포함된 항목:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>요청 관련 필드

선택적 `$select` 쿼리 문자열 매개 변수를 사용하고 요청된 필드의 쉼표로 구분된 목록을 제공합니다. `$select` 매개 변수를 생략하면 응답에 기본 세트가 포함됩니다.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>시간 기반 액세스

과거와 같은 결과의 표시를 얻습니다. 자세한 내용은 [리소스 상태에 대한 시간 기반 액세스를 위한 HTTP 프레임워크 -- Memento](https://tools.ietf.org/html/rfc7089#section-2.1), 섹션 2.1.1을 참조하세요.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**응답:**

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

---
title: Azure App Configuration REST API - 잠금
description: Azure App Configuration REST API를 사용하여 키-값 잠금으로 작업하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932526"
---
# <a name="locks"></a>잠금

이 API(버전 1.0)는 키-값 리소스에 대한 잠금 및 잠금 해제 의미 체계를 제공합니다. 다음 작업을 지원합니다.

- 잠금 설정
- 잠금 제거

`label`이 있는 경우 와일드카드가 아닌 명시적 레이블 값이어야 합니다. 모든 작업에는 선택적 매개 변수입니다. 생략하는 경우 레이블이 없음을 의미합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>키-값 잠금

- 필수: ``{key}``, ``{api-version}``  
- 선택 사항: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

키-값이 없으면 다음 응답이 반환됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>키-값 잠금 해제

- 필수: ``{key}``, ``{api-version}``  
- 선택 사항: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**응답:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

키-값이 없으면 다음 응답이 반환됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>조건부 잠금 및 잠금 해제

경합 상태를 방지하려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용합니다. `etag` 인수는 키 표현의 일부입니다. `If-Match` 또는 `If-None-Match`가 생략된 경우에는 작업이 무조건 수행됩니다.

다음 요청은 현재 키-값 표현이 지정된 `etag`와 일치하는 경우에만 작업을 적용합니다.

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 요청은 현재 키-값 표현이 있지만 지정된 `etag`와 일치하지 않는 경우에만 작업을 적용합니다.

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

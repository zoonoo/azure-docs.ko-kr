---
title: Azure 앱 구성 REST API-잠금
description: Azure 앱 구성을 사용 하 여 키-값 잠금으로 작업 하기 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7e63b48f2119c48cd43717acee7b13b1701e0032
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241270"
---
# <a name="locks"></a>잠금

이 API (버전 1.0)는 키-값 리소스에 대 한 잠금 및 잠금 해제 의미 체계를 제공 합니다. 다음 작업을 지원 합니다.

- 잠금 두기
- 잠금 제거

있는 경우는 `label` 명시적 레이블 값 (와일드 카드 아님) 이어야 합니다. 모든 작업에는 선택적 매개 변수입니다. 생략 하는 경우 레이블이 없음을 의미 합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>잠금 키-값

- 필수: ``{key}`` , ``{api-version}``  
- 선택 사항: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**보낸**

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

키-값이 없으면 다음 응답이 반환 됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>키-값 잠금 해제

- 필수: ``{key}`` , ``{api-version}``  
- 선택 사항: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**보낸**

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

키-값이 없으면 다음 응답이 반환 됩니다.

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>조건부 잠금 및 잠금 해제

경합 상태를 방지 하려면 `If-Match` 또는 `If-None-Match` 요청 헤더를 사용 합니다. `etag`인수는 키 표현의 일부입니다. 또는이 생략 된 경우에는 `If-Match` `If-None-Match` 작업이 무조건 수행 됩니다.

다음 요청은 현재 키-값 표현이 지정 된와 일치 하는 경우에만 작업을 적용 합니다 `etag` .

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

다음 요청은 현재 키-값 표현이 존재 하지만 지정 된와 일치 하지 않는 경우에만 작업을 적용 합니다 `etag` .

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

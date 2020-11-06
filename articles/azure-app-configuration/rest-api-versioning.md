---
title: Azure 앱 구성 REST API-버전 관리
description: Azure 앱 구성을 사용 하 여 버전 관리를 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424266"
---
# <a name="versioning"></a>버전 관리

api-version: 1.0

각 클라이언트 요청은 명시적인 API 버전을 쿼리 문자열 매개 변수로 제공 해야 합니다. `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` 은 SemVer (major) 형식으로 표현 됩니다. 범위 또는 버전 협상이 지원 되지 않습니다.

## <a name="error-response"></a>오류 응답

다음은 요청 된 API 버전을 일치 시킬 수 없는 경우 서버에서 반환 되는 가능한 오류 응답의 요약입니다.

### <a name="api-version-unspecified"></a>지정 되지 않은 API 버전

클라이언트에서 API 버전을 제공 하지 않고 요청을 만들 때 발생 합니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>지원 되지 않는 API 버전

클라이언트에서 요청한 API 버전이 서버에 의해 지원 되는 API 버전과 일치 하지 않는 경우에 발생 합니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>잘못 된 API 버전

클라이언트에서 API 버전으로 요청을 만들 때 값이 잘못 되었거나 서버에서 구문 분석할 수 없을 때 발생 합니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>모호한 API 버전

클라이언트에서 서버에 모호한 API 버전을 요청할 때 발생 합니다. 예를 들어 여러 다른 값이 있습니다.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```

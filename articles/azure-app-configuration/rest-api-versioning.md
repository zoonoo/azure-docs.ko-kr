---
title: Azure 앱 구성 REST API - 버전 관리
description: Azure 앱 구성 REST API를 사용하여 버전을 관리하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932424"
---
# <a name="versioning"></a>버전 관리

각 클라이언트 요청은 명시적인 API 버전을 쿼리 문자열 매개 변수로 제공해야 합니다. 예: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version`은 SemVer(major.minor) 형식으로 표시됩니다. 범위 또는 버전 협상은 지원되지 않습니다.

이 문서는 API 버전 1.0 이상에 적용됩니다.

다음은 요청한 API 버전을 일치시킬 수 없을 때 서버에서 반환할 수 있는 오류 응답에 대한 요약입니다.

## <a name="api-version-unspecified"></a>지정되지 않은 API 버전

이 오류는 클라이언트에서 API 버전을 제공하지 않고 요청을 만들 때 발생합니다.

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

## <a name="unsupported-api-version"></a>지원되지 않는 API 버전

이 오류는 클라이언트에서 요청한 API 버전이 서버에서 지원되는 API 버전과 일치하지 않는 경우에 발생합니다.

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

## <a name="invalid-api-version"></a>잘못된 API 버전

이 오류는 클라이언트에서 API 버전으로 요청을 만들 때 값이 잘못되었거나 서버에서 구문 분석할 수 없을 때 발생합니다.

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

## <a name="ambiguous-api-version"></a>모호한 API 버전

이 오류는 클라이언트에서 서버에 모호한 API 버전(예: 여러 다른 값)을 요청할 때 발생합니다.

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

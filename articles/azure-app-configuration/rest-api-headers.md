---
title: Azure 앱 구성 REST API-헤더
description: Azure 앱 구성에 사용 되는 헤더에 대 한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5e1f92c68004d0197391ab72df775913c0940fec
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424320"
---
# <a name="headers"></a>헤더

이 문서에서는 Azure 앱 구성 REST API와 함께 사용 되는 헤더에 대 한 참조 페이지에 대 한 링크를 제공 합니다.

## <a name="request-headers"></a>요청 헤더

다음 표에서는 Azure 앱 구성에 사용 되는 일반적인 요청 헤더에 대해 설명 합니다.

| 헤더 | 설명 | 예 |
|--|--|--|
| **권한 부여** | 서비스에 대 한 요청을 [인증](./rest-api-authentication-index.md) 하는 데 사용 됩니다. [섹션 14.8](https://tools.ietf.org/html/rfc2616#section-14.8) 을 참조 하세요. | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **수락** | 클라이언트에서 HTTP 응답을 허용할 미디어 유형을 서버에 알립니다. [섹션 14.1](https://tools.ietf.org/html/rfc2616#section-14.1) 을 참조 하세요. | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **수락-날짜/시간** | 이전 상태에 대 한 표현으로 해당 콘텐츠를 반환 하도록 서버에 요청 합니다. 이 헤더의 값은 해당 상태의 요청 된 datetime입니다. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) 을 참조 하세요. | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | HTTP 요청 본문 내 콘텐츠의 미디어 형식을 포함 합니다. [섹션 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) 을 참조 하세요. | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **날짜** | HTTP 요청이 발급 된 날짜/시간입니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용 됩니다. [섹션 14.18](https://tools.ietf.org/html/rfc2616#section-14.18) 을 참조 하세요. | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | 요청이 발급 된 테 넌 트를 지정 합니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용 됩니다. [섹션 14.23](https://tools.ietf.org/html/rfc2616#section-14.23) 을 참조 하세요. | `Host: contoso.azconfig.io` |
| **If-Match** | HTTP 요청 조건을 만드는 데 사용 됩니다. 이 요청은 대상 리소스의 ETag가이 헤더의 값과 일치 하는 경우에만 성공 합니다. ' * ' 값은 모든 ETag와 일치 합니다. [섹션 14.24](https://tools.ietf.org/html/rfc2616#section-14.24) 을 참조 하세요. | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | HTTP 요청 조건을 만드는 데 사용 됩니다. 이 요청은 대상 리소스의 ETag가이 헤더의 값과 일치 하지 않는 경우에만 성공 합니다. ' * ' 값은 모든 ETag와 일치 합니다. [섹션 14.26](https://tools.ietf.org/html/rfc2616#section-14.26) 을 참조 하세요. | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **동기화 토큰** | 요청 시퀀스 중에 실시간 일관성을 설정 하는 데 사용 됩니다. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | 요청에 대 한 왕복을 추적 하는 데 사용 되는 클라이언트에서 제공 하는 고유 ID입니다. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-m s----sha256** | HTTP 요청 본문의 sha256 다이제스트입니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용 됩니다. | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | 이 헤더는 `Date` 날짜 헤더에 액세스할 수 없는 경우 헤더 대신 설정 하 고 사용할 수 있습니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용 됩니다. | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | 헤더와 함께 사용 `x-ms-client-request-id` 됩니다. 이 헤더의 값이 ' t r u e ' 인 경우 요청 헤더의 값을 반환 하도록 서버에 지시 합니다 `x-ms-client-request-id` . | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>응답 헤더

서버는 응답에 다음 HTTP 헤더를 포함할 수 있습니다.

| 헤더 | 설명 | 예 |
|--|--|--|
| **Content-Type** | HTTP 응답 본문에 있는 콘텐츠의 미디어 형식을 포함 합니다. [섹션 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) 을 참조 하세요. | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | 지정 된 리소스의 상태를 나타내는 불투명 토큰입니다. 조건부 연산에 사용할 수 있습니다. [섹션 14.19](https://tools.ietf.org/html/rfc2616#section-14.19) 을 참조 하세요. | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **마지막-수정 됨** | 요청 된 리소스가 마지막으로 수정 된 시간을 설명 합니다. [HTTP 날짜로](https://tools.ietf.org/html/rfc2616#section-3.3.1)형식이 지정 됩니다. [섹션 14.29](https://tools.ietf.org/html/rfc2616#section-14.29) 을 참조 하세요. | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **링크** | 응답과 관련 된 리소스에 대 한 링크를 제공 합니다. 이 헤더는 _다음_ 링크를 사용 하 여 페이징 하는 데 사용 됩니다. [RFC 5988](https://tools.ietf.org/html/rfc5988) 을 참조 하세요. | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | 응답에 포함 된 콘텐츠가 이전 상태 임을 나타냅니다. 이 헤더의 값은 해당 상태의 날짜/시간입니다. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) 을 참조 하세요. | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **다시 시도--ms** | 클라이언트에서 실패 한 요청을 다시 시도 하기 전까지 대기 하는 제안 된 기간 (밀리초)을 제공 합니다. | `retry-after-ms: 10` |
| **x-ms-request-id** | 서비스 내에서 요청을 추적 하는 데 사용 되는 서버에서 생성 되는 고유 ID입니다. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | 클라이언트에서 인증을 시도 하는 데 사용 되며 인증 시도에 실패 한 이유에 대 한 이유를 제공 합니다. [섹션 14.47](https://tools.ietf.org/html/rfc2616#section-14.47) 을 참조 하세요. | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |

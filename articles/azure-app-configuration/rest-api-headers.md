---
title: Azure App Configuration REST API - 헤더
description: Azure App Configuration REST API에 사용되는 헤더에 대한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932577"
---
# <a name="headers"></a>헤더

이 문서에서는 Azure App Configuration REST API에 사용되는 헤더에 대한 참조 페이지 링크를 제공합니다.

## <a name="request-headers"></a>요청 헤더

다음 표에서는 Azure App Configuration에 사용되는 일반적인 요청 헤더를 설명합니다.

| 헤더 | 설명 | 예제 |
|--|--|--|
| **권한 부여** | 서비스에 대한 요청을 [인증](./rest-api-authentication-index.md)하는 데 사용됩니다. [섹션 14.8](https://tools.ietf.org/html/rfc2616#section-14.8)을 참조하세요. | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **수락** | 클라이언트에서 HTTP 응답을 허용할 미디어 유형을 서버에 알립니다. [섹션 14.1](https://tools.ietf.org/html/rfc2616#section-14.1)을 참조하세요. | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-Datetime** | 이전 상태의 표시로 내용을 반환하도록 서버에 요청합니다. 이 헤더의 값은 해당 상태의 요청된 날짜/시간입니다. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1)를 참조하세요. | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | HTTP 요청 본문 내 콘텐츠의 미디어 형식을 포함합니다. [섹션 14.17](https://tools.ietf.org/html/rfc2616#section-14.17)을 참조하세요. | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **날짜** | HTTP 요청이 발급된 날짜/시간입니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용됩니다. [섹션 14.18](https://tools.ietf.org/html/rfc2616#section-14.18)을 참조하세요. | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | 요청이 발급된 테넌트를 지정합니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용됩니다. [섹션 14.23](https://tools.ietf.org/html/rfc2616#section-14.23)을 참조하세요. | `Host: contoso.azconfig.io` |
| **If-Match** | HTTP 요청 조건을 만드는 데 사용됩니다. 이 요청은 대상 리소스의 ETag가 이 헤더의 값과 일치하는 경우에만 성공합니다. '*' 값은 모든 ETag와 일치합니다. [섹션 14.24](https://tools.ietf.org/html/rfc2616#section-14.24)를 참조하세요. | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | HTTP 요청 조건을 만드는 데 사용됩니다. 이 요청은 대상 리소스의 ETag가 이 헤더의 값과 일치하지 않는 경우에만 성공합니다. '*' 값은 모든 ETag와 일치합니다. [섹션 14.26](https://tools.ietf.org/html/rfc2616#section-14.26)을 참조하세요. | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | 요청 시퀀스 중에 실시간 일관성을 사용하도록 설정하는 데 사용됩니다. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | 요청의 라운드트립을 위해 클라이언트가 제공한 고유 ID입니다. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-sha256** | HTTP 요청 본문의 sha256 메시지 다이제스트입니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용됩니다. | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | 이 헤더는 날짜 헤더에 액세스할 수 없는 경우 `Date` 헤더 대신 설정하고 사용할 수 있습니다. 이 헤더는 [HMAC 인증](./rest-api-authentication-hmac.md)에 사용됩니다. | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | `x-ms-client-request-id` 헤더와 함께 사용됩니다. 이 헤더의 값이 'true'인 경우 `x-ms-client-request-id` 요청 헤더의 값을 반환하도록 서버에 지시합니다. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>응답 헤더

서버는 응답에 다음 HTTP 헤더를 포함할 수 있습니다.

| 헤더 | 설명 | 예제 |
|--|--|--|
| **Content-Type** | HTTP 응답 본문 내 콘텐츠의 미디어 형식을 포함합니다. [섹션 14.17](https://tools.ietf.org/html/rfc2616#section-14.17)을 참조하세요. | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | 지정된 리소스의 상태를 나타내는 불투명 토큰입니다. 조건부 작업에 사용할 수 있습니다. [섹션 14.19](https://tools.ietf.org/html/rfc2616#section-14.19)를 참조하세요. | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Last-Modified** | 요청된 리소스가 마지막으로 수정된 시간을 설명합니다. [HTTP-날짜](https://tools.ietf.org/html/rfc2616#section-3.3.1) 형식으로 지정됩니다. [섹션 14.29](https://tools.ietf.org/html/rfc2616#section-14.29)를 참조하세요. | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **링크** | 응답과 관련된 리소스의 링크를 제공합니다. 이 헤더는 _다음_ 링크를 사용하여 페이징하는 데 사용됩니다. [RFC 5988](https://tools.ietf.org/html/rfc5988)을 참조하세요. | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | 응답에 포함된 콘텐츠가 이전 상태임을 나타냅니다. 이 헤더의 값은 해당 상태의 날짜/시간입니다. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1)를 참조하세요. | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-after-ms** | 실패한 요청을 다시 시도하기 전에 클라이언트가 기다릴 수 있는 기간(밀리초)을 제안합니다. | `retry-after-ms: 10` |
| **x-ms-request-id** | 서비스 내에서 요청을 추적하는 데 사용되는 서버에서 생성된 고유 ID입니다. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | 클라이언트가 인증을 시도하고 해당 시도가 실패한 이유를 제공하는 데 사용됩니다. [섹션 14.47](https://tools.ietf.org/html/rfc2616#section-14.47)을 참조하세요. | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |

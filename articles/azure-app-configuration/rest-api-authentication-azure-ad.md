---
title: Azure Active Directory REST API - 인증
description: REST API를 통한 Azure App Configuration에 인증을 위한 Azure Active Directory 사용
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930520"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

`Bearer`Azure AD(Azure Active Directory)에서 획득한 토큰과 함께 인증 체계를 사용하여 HTTP 요청을 인증할 수 있습니다. TLS(전송 레이어 보안)를 통해 이러한 요청을 전송해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 토큰을 요청하는 데 사용되는 보안 주체를 해당하는 [Azure App Configuration 역할](./rest-api-authorization-azure-ad.md) 중 하나에 할당해야 합니다.

인증에 필요한 모든 HTTP 헤더를 각 요청에 제공합니다. 최소 요구 사항은 다음과 같습니다.

|  요청 헤더 | Description  |
| --------------- | ------------ |
| `Authorization` | `Bearer` 체계에 필요한 인증 정보. |

**예:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD 토큰 획득

Azure AD 토큰을 획득하기 전에 인증할 사용자, 토큰을 요청하는 대상 그룹, 사용할 Azure AD 엔드포인트(기관)를 식별해야 합니다.

### <a name="audience"></a>사용자

적절한 대상 그룹을 사용하여 Azure AD 토큰을 요청합니다. Azure App Configuration의 경우, 다음 대상 그룹 중 하나를 사용합니다. 대상 그룹을 토큰이 요청되는 *리소스* 라고도 합니다.

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> 요청된 대상 그룹이 `{configurationStoreName}.azconfig.io` 이면, 이는 요청을 보내는 데 사용되는 `Host` 요청 헤더(대/소문자 구분)와 정확히 일치해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Azure AD 기관은 Azure AD 토큰을 획득하는 데 사용하는 엔드포인트입니다. `https://login.microsoftonline.com/{tenantId}` 형식으로 되어 있습니다. `{tenantId}` 세그먼트는 인증하려는 사용자 또는 응용 프로그램이 속한 Azure AD 테넌트 ID를 나타냅니다.

### <a name="authentication-libraries"></a>인증 라이브러리

Azure는 Azure AD 토큰 획득 프로세스를 간소화하기 위해 Azure Active Directory 인증 라이브러리라는 라이브러리 집합을 제공합니다. Azure는 여러 언어로 이러한 라이브러리를 빌드합니다. 자세한 내용은 [설명서](../active-directory/azuread-dev/active-directory-authentication-libraries.md)를 참조하세요.

## <a name="errors"></a>오류

다음과 같은 오류가 발생할 수 있습니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**원인:** `Bearer` 체계를 사용하여 권한 부여 요청 헤더를 제공하지 않았습니다.

**해결 방법:** 유효한 `Authorization` HTTP 요청 헤더를 제공합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**원인:** Azure AD 토큰이 유효하지 않습니다.

**해결 방법:** Azure AD 기관에서 Azure AD 토큰을 획득하고 적절한 대상 그룹을 사용했는지 확인합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**원인:** Azure AD 토큰이 유효하지 않습니다.

**해결 방법:** Azure AD 기관에서 Azure AD 토큰을 획득합니다. Azure AD 테넌트가 구성 저장소가 속한 구독과 연결된 테넌트인지 확인합니다. 보안 주체가 둘 이상의 Azure AD 테넌트에 속하는 경우 이 오류가 나타날 수 있습니다.
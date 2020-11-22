---
title: Azure Active Directory REST API-인증
description: Azure Active Directory를 사용 하 여 REST API를 통해 Azure 앱 구성에 인증 합니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 78344bd3896ca7d00c9f761c586b6f5142dc1e58
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253408"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

`Bearer`Azure Active Directory (AZURE AD)에서 획득 한 토큰으로 인증 체계를 사용 하 여 HTTP 요청을 인증할 수 있습니다. TLS (전송 계층 보안)를 통해 이러한 요청을 전송 해야 합니다.

## <a name="prerequisites"></a>필수 조건

해당 하는 [Azure 앱 구성 역할](./rest-api-authorization-azure-ad.md)중 하나에 Azure AD 토큰을 요청 하는 데 사용 되는 보안 주체를 할당 해야 합니다.

인증에 필요한 모든 HTTP 헤더를 사용 하 여 각 요청을 제공 합니다. 최소 요구 사항은 다음과 같습니다.

|  요청 헤더 | Description  |
| --------------- | ------------ |
| `Authorization` | 체계에서 요구 하는 인증 정보 `Bearer` 입니다. |

**예:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD 토큰 획득

Azure AD 토큰을 획득 하기 전에 인증 하려는 사용자, 토큰을 요청 하는 대상 사용자 및 사용할 Azure AD 끝점 (기관)을 식별 해야 합니다.

### <a name="audience"></a>사용자

적절 한 대상 그룹을 사용 하 여 Azure AD 토큰을 요청 합니다. Azure 앱 구성의 경우 다음 대상 중 하나를 사용 합니다. 대상 그룹은 토큰이 요청 되는 *리소스* 라고도 합니다.

- {configurationStoreName}. azconfig
- *. azconfig.io

> [!IMPORTANT]
> 요청 대상이 인 경우 `{configurationStoreName}.azconfig.io` `Host` 요청을 보내는 데 사용 되는 요청 헤더 (대/소문자 구분)와 정확히 일치 해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Azure AD 인증 기관은 Azure AD 토큰을 획득 하는 데 사용 하는 끝점입니다. 의 형식 `https://login.microsoftonline.com/{tenantId}` 입니다. `{tenantId}`세그먼트는 인증을 시도 하는 사용자 또는 응용 프로그램이 속해 있는 AZURE AD 테 넌 트 ID를 나타냅니다.

### <a name="authentication-libraries"></a>인증 라이브러리

Azure는 Azure AD 토큰을 가져오는 프로세스를 간소화 하기 위해 Azure Active Directory 인증 라이브러리 라는 라이브러리 집합을 제공 합니다. Azure는 이러한 라이브러리를 여러 언어로 작성 합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)를 참조하세요.

## <a name="errors"></a>오류

다음과 같은 오류가 발생할 수 있습니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**이유:** 스키마를 사용 하 여 권한 부여 요청 헤더를 제공 하지 않았습니다 `Bearer` .

**해결 방법:** 올바른 `Authorization` HTTP 요청 헤더를 제공 하십시오.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**이유:** Azure AD 토큰이 유효 하지 않습니다.

**해결 방법:** Azure AD 기관에서 Azure AD 토큰을 획득 하 고 적절 한 대상 그룹을 사용 했는지 확인 합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**이유:** Azure AD 토큰이 유효 하지 않습니다.

**해결 방법:** Azure AD 기관에서 Azure AD 토큰을 획득 합니다. Azure AD 테 넌 트가 구성 저장소가 속한 구독과 연결 된 테 넌 트 인지 확인 합니다. 보안 주체가 둘 이상의 Azure AD 테 넌 트에 속하는 경우이 오류가 나타날 수 있습니다.

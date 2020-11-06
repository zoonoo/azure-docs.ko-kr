---
title: Azure Active Directory REST API-인증
description: Azure Active Directory를 사용 하 여 REST API를 사용 하 Azure 앱 구성에 인증 합니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424194"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

Azure Active Directory (Azure AD)에서 획득 한 토큰을 사용 하 여 **전달자** 인증 체계를 사용 하 여 HTTP 요청을 인증할 수 있습니다. 이러한 요청은 TLS를 통해 전송 되어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 토큰을 요청 하는 데 사용 되는 보안 주체는 적용 가능한 [앱 구성 역할](./rest-api-authorization-azure-ad.md) 중 하나에 할당 되어야 합니다.

인증에 필요한 모든 HTTP 헤더를 사용 하 여 각 요청을 제공 합니다. 필요한 최소는 다음과 같습니다.

|  요청 헤더 | Description  |
| --------------- | ------------ |
| **권한 부여** | **전달자** 체계에 필요한 인증 정보입니다. 형식 및 세부 정보는 아래에 설명 되어 있습니다. |

**예:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>토큰 획득 Azure Active Directory

Azure AD 토큰을 획득 하기 전에 인증 하려는 사용자, 토큰을 요청 하는 대상 그룹 및 사용 해야 하는 Azure AD 끝점 (기관)을 식별 해야 합니다.

### <a name="audience"></a>사용자

적절 한 대상 그룹을 사용 하 여 Azure AD 토큰을 요청 해야 합니다. Azure 앱 구성의 경우 토큰을 요청할 때 다음 대상 그룹 중 하나를 지정 해야 합니다. 대상 그룹은 토큰이 요청 되는 "리소스" 라고도 합니다.

- {configurationStoreName}. azconfig
- *. azconfig.io

> [!IMPORTANT]
> 요청한 대상이 {configurationStoreName}. azconfig 인 경우 요청을 보내는 데 사용 되는 "호스트" 요청 헤더 (대/소문자 구분)와 정확 하 게 일치 해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Azure AD 인증 기관은 Azure AD 토큰을 획득 하는 데 사용 되는 끝점입니다. 형식입니다 `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`세그먼트는 인증 하려는 사용자/응용 프로그램이 속한 Azure Active Directory 테 넌 트 ID를 나타냅니다.

### <a name="authentication-libraries"></a>인증 라이브러리

Azure는 Azure AD 토큰을 가져오는 프로세스를 간소화 하기 위해 ADAL (Azure Active Directory 인증 라이브러리) 이라는 라이브러리 집합을 제공 합니다. 이러한 라이브러리는 여러 언어용으로 빌드됩니다. 설명서는 [여기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)에서 찾을 수 있습니다.

## <a name="errors"></a>**Errors**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**이유:** 전달자 체계가 포함 된 권한 부여 요청 헤더를 제공 하지 않았습니다.
**해결 방법:** 올바른 HTTP 요청 헤더를 제공 하십시오. ```Authorization```

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**이유:** Azure AD 토큰이 잘못 되었습니다.
**해결 방법:** Azure AD 기관에서 Azure AD 토큰을 획득 하 고 적절 한 대상이 사용 되는지 확인 합니다.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**이유:** Azure AD 토큰이 잘못 되었습니다.
**해결 방법:** Azure ad 기관에서 Azure ad 토큰을 획득 하 고 Azure ad 테 넌 트가 구성 저장소가 속한 구독과 연결 된 테 넌 트 인지 확인 합니다. 보안 주체가 둘 이상의 Azure AD 테 넌 트에 속하는 경우이 오류가 나타날 수 있습니다.

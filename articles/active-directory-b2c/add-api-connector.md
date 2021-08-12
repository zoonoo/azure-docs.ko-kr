---
title: 사용자 흐름에 API 커넥터 추가(미리 보기)
description: 사용자 흐름에서 사용할 API 커넥터를 구성합니다.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 05/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1913b6cf14aaf31d610adcf446dbe91326e02ff1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108742916"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>등록 사용자 흐름에 API 커넥터 추가

개발자 또는 IT 관리자는 API 커넥터를 사용하여 REST API와 등록 사용자 흐름을 통합함으로써 등록 환경을 사용자 지정하고 외부 시스템과 통합할 수 있습니다. 이 연습의 끝부분에서는 [REST API 서비스](api-connectors-overview.md)와 상호 작용하는 Azure AD B2C 사용자 흐름을 만들 수 있습니다. 

::: zone pivot="b2c-user-flow"

이 시나리오에서 REST API는 이메일 주소의 도메인이 fabrikam.com인지 아니면 fabricam.com인지를 확인합니다. 사용자가 제공한 표시 이름이 5자를 초과합니다. 그런 다음, 고정적인 값이 있는 직위를 반환합니다. 

> [!IMPORTANT]
> 등록을 위한 API 커넥터는 Azure AD B2C의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

::: zone-end

::: zone pivot="b2c-custom-policy"

이 시나리오에서는 사용자가 Azure AD B2C 등록 페이지에 전용 번호를 입력할 수 있는 기능을 추가합니다. REST API는 이메일과 전용 번호의 조합이 프로모션 코드에 매핑되는지 여부를 확인합니다. REST API가 이 사용자에 대한 프로모션 코드를 찾은 경우 Azure AD B2C로 반환됩니다. 마지막으로, 프로모션 코드는 애플리케이션이 사용할 토큰 클레임에 삽입됩니다.

또한 상호 작용은 오케스트레이션 단계로도 설계할 수 있습니다. 이는 REST API가 화면에서 데이터의 유효성을 검사하고 항상 클레임을 반환하는 경우에 적합합니다. 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](custom-policy-rest-api-claims-exchange.md)을 참조하세요.

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>API 커넥터 만들기

[API 커넥터](api-connectors-overview.md)를 사용하려면 먼저 API 커넥터를 만든 다음, 사용자 흐름에서 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
4. **API 커넥터(미리 보기)** 를 선택한 다음, **새 API 커넥터** 를 선택합니다.

   ![새 API 커넥터 추가](./media/add-api-connector/api-connector-new.png)

5. 호출의 표시 이름을 제공합니다. 예를 들어 **사용자 정보가 유효한지 검사** 합니다.
6. API 호출에 대한 **엔드포인트 URL** 을 제공합니다.
7. **인증 유형** 을 선택하고 API 호출에 대한 인증 정보를 구성합니다. API 보안 설정에 대한 옵션은 아래 섹션을 참조하세요.

    ![API 커넥터 구성](./media/add-api-connector/api-connector-config.png)

8. **저장** 을 선택합니다.

## <a name="securing-the-api-endpoint"></a>API 엔드포인트 보안
HTTP 기본 인증 또는 HTTPS 클라이언트 인증서 인증(미리 보기)을 사용하여 API 엔드포인트를 보호할 수 있습니다. 두 경우 모두 API 엔드포인트를 호출할 때 Azure AD B2C에서 사용할 자격 증명을 제공합니다. 그런 다음, API 엔드포인트는 자격 증명을 확인하고 권한 부여 결정을 내립니다.

### <a name="http-basic-authentication"></a>HTTP 기본 인증
HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의되어 있습니다. Azure AD B2C는 `Authorization` 헤더에서 클라이언트 자격 증명(`username` 및 `password`)을 사용하여 HTTP 요청을 보냅니다. 자격 증명은 base64로 인코딩된 문자열 `username:password`로 형식이 지정됩니다. 그런 다음, API는 이러한 값을 확인하여 API 호출을 거부할지 여부를 결정합니다.

### <a name="https-client-certificate-authentication-preview"></a>HTTPS 클라이언트 인증서 인증(미리 보기)

> [!IMPORTANT]
> 이 기능은 미리 보기 상태이며 서비스 수준 계약 없이 제공됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

클라이언트 인증서 인증은 클라이언트에서 해당 ID를 증명하기 위해 클라이언트 인증서를 서버에 제공하는 상호 인증서 기반 인증 방법입니다. 이 경우 Azure AD B2C는 업로드하는 인증서를 API 커넥터 구성의 일부로 사용합니다. 이는 TLS/SSL 핸드셰이크의 일부로 발생합니다. 그러면 API 서비스는 적절한 인증서가 있는 서비스로만 액세스를 제한할 수 있습니다. 클라이언트 인증서는 PKCS12(PFX) X.509 디지털 인증서입니다. 프로덕션 환경에서는 인증 기관에서 서명해야 합니다. 

인증서를 만들려면 자체 서명된 인증서 및 서명된 인증서의 인증서 발급자 공급자와의 통합 옵션이 포함된 [Azure Key Vault](../key-vault/certificates/create-certificate.md)를 사용할 수 있습니다. 권장 설정은 다음과 같습니다.
- **주체**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **콘텐츠 형식**: `PKCS #12`
- **수명 주기 작업 유형**: `Email all contacts at a given percentage lifetime` 또는 `Email all contacts a given number of days before expiry`
- **키 유형**: `RSA`
- **키 크기**: `2048`
- **내보낼 수 있는 프라이빗 키**: `Yes`(pfx 파일을 내보내는 데 필요)

그런 다음, [인증서를 내보낼](../key-vault/certificates/how-to-export-certificate.md)수 있습니다. 또는 PowerShell의 [New-SelfSignedCertificate cmdlet](../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional)을 사용하여 자체 서명된 인증서를 생성할 수 있습니다.

인증서가 있으면 API 커넥터 구성의 일부로 업로드할 수 있습니다. 암호는 암호로 보호되는 인증서 파일에만 필요합니다.

API는 API 엔드포인트를 보호하기 위해 전송된 클라이언트 인증서를 기준으로 권한 부여를 구현해야 합니다. Azure App Service 및 Azure Functions의 경우 [TLS 상호 인증 구성](../app-service/app-service-web-configure-tls-mutual-auth.md)을 참조하여 *API 코드에서 인증서를 사용하도록 설정하고 유효성을 검사* 하는 방법을 알아보세요.  Azure API Management를 사용하여 정책 식을 통해 원하는 값을 기준으로 [클라이언트 인증서 속성을 확인](
../api-management/api-management-howto-mutual-certificates-for-clients.md)할 수도 있습니다.

인증서가 만료되는 경우에 대한 미리 알림 경고를 설정하는 것이 좋습니다. 새 인증서를 생성하고 위의 단계를 반복해야 합니다. 새 인증서를 배포하는 동안 API 서비스는 일시적으로 이전 인증서와 새 인증서를 계속 사용할 수 있습니다. 기존 API 커넥터에 새 인증서를 업로드하려면 **API 커넥터** 에서 API 커넥터를 선택하고 **새 인증서 업로드** 를 클릭합니다. 만료되지 않고 시작 날짜가 지난 가장 최근에 업로드된 인증서가 Azure Active Directory에서 자동으로 사용됩니다.

### <a name="api-key"></a>API 키

일부 서비스는 "API 키" 메커니즘을 사용하여 개발 중에 HTTP 엔드포인트에 대한 액세스 권한을 난독 처리합니다. [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)의 경우 `code`을 **엔드포인트 URL** 에 쿼리 매개 변수로 포함하여 이 작업을 수행할 수 있습니다. (예: `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>) 

프로덕션 환경에서 단독으로 사용해야 하는 메커니즘이 아닙니다. 따라서 기본 또는 인증서 인증에 대한 구성이 항상 필요합니다. 개발 목적으로 인증 방법을 구현하지 않으려는 경우(권장되지 않음) 기본 인증을 선택하고, API에서 권한 부여를 구현하는 동안 무시할 수 있는 `username` 및 `password`에 대해 임시 값을 사용할 수 있습니다.

## <a name="the-request-sent-to-your-api"></a>API로 전송된 요청
API 커넥터는 **HTTP POST** 요청으로 구체화하여 사용자 특성('클레임')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](/graph/api/resources/user#properties) 사용자 속성과 유사하게 직렬화됩니다. 

**요청 예**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**Azure AD B2C**  >  **사용자 특성** 환경에 나열된 사용자 속성 및 사용자 지정 특성만 요청으로 보낼 수 있습니다.

사용자 지정 특성은 디렉터리의 **extension_\<extensions-app-id>_CustomAttribute** 형식으로 존재합니다. API는 동일한 직렬화된 형식으로 클레임을 수신해야 합니다. 사용자 지정 특성에 대한 자세한 내용은 [Azure AD B2C에서 사용자 지정 특성 정의](user-flow-custom-attributes.md)를 참조하세요.

또한 **UI 로캘('ui_locales')** 클레임은 모든 요청에 기본적으로 전송됩니다. API에서 다국어 응답을 반환하는 데 사용할 수 있는 사용자의 로캘을 디바이스에 구성된 대로 제공합니다.

> [!IMPORTANT]
> API 엔드포인트가 호출 될 때 클레임에 값이 없으면 클레임은 API로 전송되지 않습니다. API는 클레임이 요청에 없는 경우를 명시적으로 확인하고 처리하도록 설계해야 합니다.

> [!TIP] 
> [**ID('identities')**](/graph/api/resources/objectidentity) 및 **이메일 주소('email')** 클레임은 API에서 사용자가 테넌트에 계정을 포함하기 전에 사용자를 식별하는 데 사용할 수 있습니다. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용하도록 설정

등록 사용자 흐름에 API 커넥터를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
4. **사용자 흐름** 을 선택하고 API 커넥터를 추가하려는 사용자 흐름을 선택합니다.
5. **API 커넥터** 를 선택하고 사용자 흐름의 다음 단계에서 호출하려는 API 엔드포인트를 선택합니다.

   - **ID 공급자를 사용하여 로그인한 후**
   - **사용자를 만들기 전**

   ![사용자 흐름에 API 추가](./media/add-api-connector/api-connectors-user-flow-select.png)

6. **저장** 을 선택합니다.

## <a name="after-signing-in-with-an-identity-provider"></a>ID 공급자를 사용하여 로그인한 후

API 커넥터는 로그인 프로세스의 해당 절차에서 ID 공급자(예: Google, Facebook, Azure AD)를 사용한 사용자 인증 후 즉시 호출됩니다. 이 단계는 사용자 특성을 수집하기 위해 사용자에게 표시되는 형식인 ***특성 모음 페이지*** 앞에 나옵니다. 이 단계는 사용자가 로컬 계정으로 등록하는 경우에는 호출되지 않습니다.

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

API로 전송되는 정확한 클레임은 ID 공급자가 제공하는 정보에 따라 달라집니다. 'email'은 항상 전송됩니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 웹 API의 예상 응답 유형

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 다음 단계: 특성 컬렉션 페이지로 계속 진행되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환하는 경우 해당 클레임은 다음을 수행합니다.

- 특성 컬렉션 페이지의 입력 필드를 미리 채웁니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조하세요.

#### <a name="blocking-response"></a>차단 응답

차단 응답은 사용자 흐름을 종료합니다. 이 응답은 사용자에게 블록 페이지를 표시하여 사용자 흐름의 연속을 중지하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공하는 `userMessage`가 표시됩니다.

[차단 응답](#example-of-a-blocking-response)의 예를 참조하세요.

## <a name="before-creating-the-user"></a>사용자를 만들기 전

등록 프로세스 내 이 단계에서, API 커넥터는 포함된 경우 특성 모음 페이지 다음에 호출됩니다. 이 단계는 항상 사용자 계정 생성 전에 호출됩니다.

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

API로 전송되는 클레임은 사용자로부터 수집한 정보나 ID 공급자가 제공하는 정보에 따라 달라집니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 웹 API의 예상 응답 유형

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답
- 유효성 검사 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 다음 단계: 디렉터리에 사용자 만들기로 계속 진행되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환하는 경우 해당 클레임은 다음을 수행합니다.

- 특성 컬렉션 페이지에서 클레임에 이미 할당된 모든 값을 재정의합니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조하세요.

#### <a name="blocking-response"></a>차단 응답
차단 응답은 사용자 흐름을 종료합니다. 이 응답은 사용자에게 블록 페이지를 표시하여 사용자 흐름의 연속을 중지하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공하는 `userMessage`가 표시됩니다.

[차단 응답](#example-of-a-blocking-response)의 예를 참조하세요.

### <a name="validation-error-response"></a>유효성 검사-오류 응답
 API가 유효성 검사 오류 응답으로 대응하는 경우 사용자 흐름은 특성 컬렉션 페이지에 유지되고 `userMessage`가 사용자에게 표시됩니다. 그런 다음, 사용자는 양식을 편집하고 다시 제출할 수 있습니다. 이 유형의 응답을 입력 유효성 검사에 사용할 수 있습니다.

[유효성 검사 오류 응답](#example-of-a-validation-error-response)의 예를 참조하세요.

## <a name="example-responses"></a>예제 응답

### <a name="example-of-a-continuation-response"></a>연속 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 매개 변수                                          | Type              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| action                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 예       | 반환된 값은 사용자로부터 수집된 값을 덮어쓸 수 있습니다. **애플리케이션 클레임** 으로 선택된 경우 토큰에서 반환될 수도 있습니다.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 예       | 클레임은 `_<extensions-app-id>_`를 포함할 필요가 없습니다. 반환된 값은 사용자로부터 수집된 값을 덮어쓸 수 있습니다. **애플리케이션 클레임** 으로 선택된 경우 토큰에서 반환될 수도 있습니다.  |

### <a name="example-of-a-blocking-response"></a>차단 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| 매개 변수   | Type   | 필수 | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 버전     | String | 예      | API 버전입니다.                                                    |
| action      | String | 예      | 값은 `ShowBlockPage`여야 합니다.                                              |
| userMessage | String | 예      | 사용자에게 표시할 메시지입니다.                                            |

**차단 응답이 있는 최종 사용자 환경**

![예제 블록 페이지](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>유효성 검사 오류 응답의 예

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| 매개 변수   | Type    | 필수 | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 버전     | String  | 예      | API의 버전입니다.                                                    |
| action      | String  | 예      | 값은 `ValidationError`이어야 합니다.                                           |
| 상태      | 정수/문자열 | 예      | ValidationError 응답의 경우 값 `400` 또는 `"400"`이어야 합니다.  |
| userMessage | String  | 예      | 사용자에게 표시할 메시지입니다.                                            |

> [!NOTE]
> HTTP 상태 코드는 응답 본문의 "status" 값 외에 "400"이어야 합니다.

**유효성 검사 오류 응답이 포함된 최종 사용자 환경**

![유효성 검사 페이지 예](./media/add-api-connector/validation-error-postal-code.png)


::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prepare-a-rest-api-endpoint"></a>REST API 엔드포인트 준비

이 연습에서는 이메일 주소가 전용 ID로 백 엔드 시스템에 등록되었는지 여부를 확인하는 REST API가 있어야 합니다. 등록된 경우 REST API는 고객이 애플리케이션 내에서 상품을 구매할 수 있는 등록 프로모션 코드를 반환합니다. 그렇지 않으면 REST API는 HTTP 409 오류 메시지: "전용 ID '{loyalty ID}'가 '{email}' 이메일 주소와 연결되어 있지 않습니다"를 반환합니다.

다음 JSON 코드는 Azure AD B2C가 REST API 엔드포인트로 전송하는 데이터를 보여줍니다. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

REST API가 데이터의 유효성을 검사한 후에는 다음 JSON 데이터를 사용하여 HTTP 200(Ok)를 반환해야 합니다.

```json
{
    "promoCode": "24534"
}
```

유효성 검사에 실패한 경우 REST API는 `userMessage` JSON 요소와 함께 HTTP 409(충돌)를 반환해야 합니다. IEF는 REST API가 반환하는 `userMessage` 클레임을 예상합니다. 유효성 검사에 실패하면 이 클레임은 사용자에게 문자열로 표시됩니다.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

REST API 엔드포인트의 설정은 이 문서에서 다루지 않습니다. [Azure Functions](../azure-functions/functions-reference.md) 샘플은 링크를 통해 확인하세요. 전체 Azure 함수 코드는 [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)에서 액세스할 수 있습니다.

## <a name="define-claims"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. [클레임 스키마](claimsschema.md) 섹션 내에서 클레임을 선언할 수 있습니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가합니다.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 기술 프로필 추가 

[RESTful 기술 프로필](restful-technical-profile.md)은 자체 RESTful 서비스와의 상호 작용을 지원합니다. Azure AD B2C는 `InputClaims` 컬렉션에서 RESTful 서비스로 데이터를 보내고 `OutputClaims` 컬렉션에서 데이터를 다시 수신합니다. **ClaimsProviders** 요소를 찾고 다음과 같이 새 클레임 공급자를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

이 예제에서 `userLanguage`는 JSON 페이로드 내에서 `lang`으로 REST 서비스에 전송됩니다. `userLanguage` 클레임의 값은 현재 사용자 언어 ID를 포함합니다. 자세한 내용은 [클레임 해결 프로그램](claim-resolver-overview.md)을 참조하세요.

### <a name="configure-the-restful-api-technical-profile"></a>RESTful API 기술 프로필 구성 

REST API를 배포한 후에는 다음을 포함하여 고유한 REST API를 반영하도록 `REST-ValidateProfile` 기술 프로필 메타데이터를 설정하세요.

- **ServiceUrl**. REST API 엔드포인트 URL을 설정합니다.
- **SendClaimsIn**. 입력 클레임이 RESTful 클레임 공급자로 전송되는 방법을 지정합니다.
- **AuthenticationType**. RESTful 클레임 공급자가 수행하는 인증 유형을 설정합니다. 
- **AllowInsecureAuthInProduction**. 프로덕션 환경에서 이 메타데이터가 `true`로 설정되었는지 확인합니다.
    
자세한 구성은 [RESTful 기술 프로필 메타데이터](restful-technical-profile.md#metadata)를 참조하세요.

위의 `AuthenticationType` 및 `AllowInsecureAuthInProduction` 설명은 프로덕션 환경으로 이동할 때 수행해야 하는 변경 내용을 지정합니다. 프로덕션을 위해 RESTful Api를 보호하는 방법을 알아보려면 [Secure RESTful API](secure-rest-api.md)를 참조하세요.

## <a name="validate-the-user-input"></a>사용자 입력 유효성 검사

등록하는 동안 사용자의 전용 번호를 얻으려면 사용자가 화면에 이 데이터를 입력하도록 허용해야 합니다. 기존 등록 기술 프로필 섹션의 `OutputClaims` 요소에 추가하여 등록 페이지에 **loyaltyId** 출력 클레임을 추가합니다. 전체 출력 클레임 목록을 지정하여 화면에 클레임이 표시되는 순서를 제어합니다.  

등록 기술 프로필에 `REST-ValidateProfile`을 호출하는 유효성 검사 기술 프로필 참조를 추가합니다. 새 유효성 검사 기술 프로필은 기본 정책에 정의된 `<ValidationTechnicalProfiles>` 컬렉션의 맨 위에 추가됩니다. 이 동작은 유효성 검사가 성공적으로 수행된 후에만 디렉터리에 계정을 만들도록 Azure AD B2C가 이동한다는 것을 의미합니다.   

1. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 클레임 공급자를 추가합니다.

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>토큰에 클레임 포함하기 

프로모션 코드 클레임을 신뢰 당사자 애플리케이션에 다시 반환하려면 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 파일에 출력 클레임을 추가합니다. 출력 클레임은 성공적인 사용자 경험 이후 토큰에 클레임을 추가할 수 있도록 하고 애플리케이션으로 전송됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정하여 `promoCode`를 출력 클레임으로 추가합니다.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택하고 변경한 정책 파일인 *TrustFrameworkExtensions.xml* 및 *SignUpOrSignin.xml* 을 업로드합니다. 
1. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 전자 메일 주소를 사용하여 등록할 수 있습니다.
1. **지금 등록** 링크를 클릭합니다.
1. **내 전용 ID** 에 1234를 입력하고 **계속** 을 클릭합니다. 이 시점에서 유효성 검사 오류 메시지가 표시됩니다.
1. 다른 값으로 변경하고 **계속** 을 클릭합니다.
1. 애플리케이션으로 다시 전송되는 토큰에는 `promoCode` 클레임이 포함됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>모범 사례 및 문제 해결 방법

### <a name="using-serverless-cloud-functions"></a>서버리스 클라우드 함수 사용

Azure Functions의 HTTP 트리거와 같은 서버리스 함수는 API 커넥터에서 사용할 API 엔드포인트를 만드는 방법을 제공합니다. [예를 들어](code-samples.md#api-connectors) 서버리스 클라우드 함수를 사용하여 유효성 검사 논리를 수행하고 등록을 특정 메일 도메인으로 제한할 수 있습니다. 서버리스 클라우드 함수는 보다 복잡한 시나리오에 대해 다른 웹 API, 사용자 저장소 및 기타 클라우드 서비스를 호출할 수도 있습니다.

### <a name="best-practices"></a>모범 사례
다음 사항을 확인합니다.
* API는 위에 설명된 대로 API 요청 및 응답 계약을 따릅니다. 
* API 커넥터의 **엔드포인트 URL** 은 올바른 API 엔드포인트를 가리킵니다.
* API는 수신된 클레임의 null 값을 명시적으로 확인합니다.
* API는 유연하게 사용자 환경을 보장하기 위해 최대한 신속하게 응답합니다.
    * 서버리스 함수 또는 스케일링 가능한 웹 서비스를 사용하는 경우 프로덕션 환경에서 API를 "활성" 또는 "웜" 상태로 유지하는 호스팅 계획을 사용합니다. Azure Functions의 경우 [프리미엄 플랜](../azure-functions/functions-scale.md)을 사용하는 것이 좋습니다.
 
### <a name="use-logging"></a>로깅 사용

일반적으로 [Application Insights](../azure-functions/functions-monitoring.md)와 같은 웹 API 서비스에서 사용하도록 설정된 로깅 도구를 사용하여 API에서 예기치 않은 오류 코드, 예외 및 성능 저하 문제를 모니터링하는 것이 유용합니다.
* HTTP 200 또는 400이 아닌 HTTP 상태 코드를 모니터링합니다.
* 401 또는 403 HTTP 상태 코드는 일반적으로 인증에 문제가 있음을 나타냅니다. API 커넥터에서 API의 인증 계층 및 해당 구성을 한 번 더 확인합니다.
* 필요한 경우 개발에서 좀 더 적극적인 로깅 수준(예: "추적" 또는 "디버그")을 사용합니다.
* API에서 긴 응답 시간이 나타나는지 모니터링합니다.

## <a name="next-steps"></a>다음 단계

::: zone pivot="b2c-user-flow"

- [샘플](code-samples.md#api-connectors)을 시작하세요.
- [API 커넥터 보호](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](custom-policy-rest-api-claims-exchange.md)
- [API 커넥터 보호](secure-rest-api.md)
- [참조: RESTful 기술 프로필](restful-technical-profile.md)

::: zone-end

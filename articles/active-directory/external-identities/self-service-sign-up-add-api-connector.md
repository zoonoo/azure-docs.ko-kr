---
title: 셀프 서비스 등록 흐름에 API 커넥터 추가-Azure AD
description: 사용자 흐름에서 사용할 웹 API를 구성 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9bbdb831df9c51c6d80e6c441ac7bdd2778428
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044552"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>사용자 흐름에 API 커넥터 추가

[Api 커넥터](api-connectors-overview.md)를 사용 하려면 먼저 api 커넥터를 만든 다음 사용자 흐름에서 사용 하도록 설정 합니다.

> [!IMPORTANT]
>**2021년 1월 4일부터** Google은 [WebView 로그인 지원을 중단](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)합니다. Gmail에서 Google 페더레이션 또는 셀프 서비스 등록을 사용하는 경우 [기간 업무 네이티브 애플리케이션의 호환성을 테스트](google-federation.md#deprecation-of-webview-sign-in-support)해야 합니다.

## <a name="create-an-api-connector"></a>API 커넥터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **모든 api 커넥터** 를 선택한 다음 **새 api 커넥터** 를 선택 합니다.

   ![새 API 커넥터 추가](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 호출에 대 한 표시 이름을 제공 합니다. 예를 들어 **승인 상태를 확인** 합니다.
6. API 호출에 대 한 **끝점 URL** 을 제공 합니다.
7. **인증 유형을** 선택 하 고 API 호출에 대 한 인증 정보를 구성 합니다. API 보안 설정에 대 한 옵션은 아래 섹션을 참조 하세요.

    ![API 커넥터 구성](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. **저장** 을 선택합니다.

## <a name="securing-the-api-endpoint"></a>API 끝점 보안
HTTP 기본 인증 또는 HTTPS 클라이언트 인증서 인증 (미리 보기)을 사용 하 여 API 끝점을 보호할 수 있습니다. 두 경우 모두 API 끝점을 호출할 때 Azure Active Directory에서 사용할 자격 증명을 제공 합니다. 그런 다음 API 끝점은 자격 증명을 확인 하 고 권한 부여 결정을 수행 합니다.

### <a name="http-basic-authentication"></a>HTTP 기본 인증
HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의되어 있습니다. Azure Active Directory는 헤더에 클라이언트 자격 증명 (및)을 사용 하 여 HTTP 요청을 보냅니다 `username` `password` `Authorization` . 자격 증명은 b a s e 64로 인코딩된 문자열로 형식이 지정 됩니다 `username:password` . 그런 다음 API는 이러한 값을 확인 하 여 API 호출을 거부할지 여부를 결정 합니다.

### <a name="https-client-certificate-authentication-preview"></a>HTTPS 클라이언트 인증서 인증 (미리 보기)

> [!IMPORTANT]
> 이 기능은 미리 보기 상태 이며 서비스 수준 계약 없이 제공 됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

클라이언트 인증서 인증은 클라이언트에서 id를 증명 하기 위해 서버에 클라이언트 인증서를 제공 하는 상호 인증서 기반 인증 방법입니다. 이 경우 Azure Active Directory은 API 커넥터 구성의 일부로 업로드 하는 인증서를 사용 합니다. 이는 SSL 핸드셰이크의 일부로 발생합니다. 그러면 API 서비스는 적절 한 인증서가 있는 서비스로만 액세스를 제한할 수 있습니다. 클라이언트 인증서는 PKCS12 (PFX) X. x.509 digital certificate입니다. 프로덕션 환경에서는 인증 기관에 의해 서명 되어야 합니다. 

인증서를 만들려면 자체 서명 된 인증서에 대 한 옵션과 서명 된 인증서의 인증서 발급자 공급자와의 통합이 포함 된 [Azure Key Vault](../../key-vault/certificates/create-certificate.md)를 사용할 수 있습니다. 권장 설정은 다음과 같습니다.
- **제목**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **콘텐츠 형식**: `PKCS #12`
- **형식에 대 한 수명 acton** `Email all contacts at a given percentage lifetime` 또는 `Email all contacts a given number of days before expiry`
- **키 유형**: `RSA`
- **키 크기**: `2048`
- **내보낼 수 있는 개인 키**: `Yes` (pfx 파일을 내보낼 수 있으려면)

그런 다음 [인증서를 내보낼](../../key-vault/certificates/how-to-export-certificate.md)수 있습니다. 또는 PowerShell의 [new-selfsignedcertificate cmdlet](../../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) 을 사용 하 여 자체 서명 된 인증서를 생성할 수 있습니다.

인증서가 있으면 API 커넥터 구성의 일부로 업로드할 수 있습니다. 암호는 암호로 보호 되는 인증서 파일에만 필요 합니다.

Api는 API 끝점을 보호 하기 위해 전송 된 클라이언트 인증서에 따라 권한 부여를 구현 해야 합니다. Azure App Service 및 Azure Functions의 경우 *API 코드에서 인증서* 를 사용 하도록 설정 하 고 유효성을 검사 하는 방법을 알아보려면 [TLS 상호 인증 구성](../../app-service/app-service-web-configure-tls-mutual-auth.md) 을 참조 하세요.  Azure API Management를 사용 하 여 API를 보호 하 고 정책 식을 사용 하 여 원하는 값에 대 한 [클라이언트 인증서 속성을 확인할](
../../api-management/api-management-howto-mutual-certificates-for-clients.md) 수도 있습니다.
 
인증서가 만료 되는 경우에 대 한 미리 알림 경고를 설정 하는 것이 좋습니다. 새 인증서를 생성 하 고 위의 단계를 반복 해야 합니다. 새 인증서를 배포 하는 동안 API 서비스는 일시적으로 이전 인증서와 새 인증서를 계속 사용할 수 있습니다. 기존 API 커넥터에 새 인증서를 업로드 하려면 **모든 api 커넥터** 에서 api 커넥터를 선택 하 고 **새 인증서 업로드** 를 클릭 합니다. 만료 되지 않고 시작 날짜를 지난 가장 최근에 업로드 된 인증서는 Azure Active Directory에서 자동으로 사용 됩니다.

### <a name="api-key"></a>API 키
일부 서비스는 "API 키" 메커니즘을 사용 하 여 개발 중에 HTTP 끝점에 대 한 액세스를 난독 처리 합니다. [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)의 경우를 `code` **끝점 URL** 에 쿼리 매개 변수로 포함 하 여이를 수행할 수 있습니다. 예: `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

프로덕션 환경에서 단독으로 사용 해야 하는 메커니즘이 아닙니다. 따라서 기본 또는 인증서 인증에 대 한 구성이 항상 필요 합니다. 개발 목적으로 인증 방법을 구현 하지 않으려는 경우 (권장 되지 않음) 기본 인증을 선택 하 고에 임시 값을 사용 하 고 api `username` `password` 에서 권한 부여를 구현 하는 동안 api를 무시할 수 있습니다.

## <a name="the-request-sent-to-your-api"></a>API로 전송 된 요청
API 커넥터는 **HTTP POST** 요청으로 구체화 되어 사용자 특성 (' 클레임 ')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](/graph/api/resources/user#properties) 사용자 속성과 유사 하 게 직렬화 됩니다. 

**요청 예**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

**Azure Active Directory**  >  **외부 id**  >  **사용자 지정 사용자 특성** 환경에 나열 된 사용자 속성 및 사용자 지정 특성만 요청에서 보낼 수 있습니다.

사용자 지정 특성은 디렉터리의 **extension_ \<extensions-app-id> _AttributeName**  형식으로 존재 합니다. API는 동일한 직렬화 된 형식으로 클레임을 수신 해야 합니다. 사용자 지정 특성에 대 한 자세한 내용은 [셀프 서비스 등록 흐름에 대 한 사용자 지정 특성 정의](user-flow-add-custom-attributes.md)를 참조 하세요.

또한 **UI 로캘 (' ui_locales ')** 클레임은 모든 요청에 기본적으로 전송 됩니다. API에서 다국어 응답을 반환 하는 데 사용할 수 있는 사용자의 로캘을 장치에 구성 된 대로 제공 합니다.

> [!IMPORTANT]
> API 끝점이 호출 될 때 클레임에 값이 없으면 클레임은 API로 보내지지 않습니다. API는 클레임이 요청에 없는 경우를 명시적으로 확인 하 고 처리 하도록 설계 해야 합니다.

> [!TIP] 
> [**id (' id ')**](/graph/api/resources/objectidentity) 및 **전자 메일 주소 (' 메일 ')** 클레임은 API에서 사용자가 테 넌 트에 계정을 포함 하기 전에 사용자를 식별 하는 데 사용할 수 있습니다. ' Id ' 클레임은 사용자가 Google 또는 Facebook과 같은 id 공급자를 사용 하 여 인증할 때 전송 됩니다. ' email '은 항상 전송 됩니다.

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용 하도록 설정

다음 단계를 수행 하 여 API 커넥터를 셀프 서비스 등록 사용자 흐름에 추가 합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **사용자 흐름** 을 선택 하 고 API 커넥터를 추가 하려는 사용자 흐름을 선택 합니다.
5. **Api 커넥터** 를 선택 하 고 사용자 흐름에서 다음 단계에 호출 하려는 api 끝점을 선택 합니다.

   - **Id 공급자를 사용 하 여 로그인 한 후**
   - **사용자를 만들기 전에**

   ![사용자 흐름에 Api 추가](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. **저장** 을 선택합니다.

## <a name="after-signing-in-with-an-identity-provider"></a>Id 공급자를 사용 하 여 로그인 한 후

사용자가 id 공급자 (예: Google, Facebook, & Azure AD)를 사용 하 여 인증 한 후 즉시 등록 프로세스의이 단계에서 API 커넥터가 호출 됩니다. 이 단계는 사용자 특성을 수집 하기 위해 사용자에 게 표시 되는 형식인 ***특성 컬렉션 페이지*** 앞에 나옵니다. 사용자가 로컬 계정으로 등록 하는 경우에는이 단계가 호출 되지 않습니다.

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

API로 전송 되는 정확한 클레임은 id 공급자가 제공 하는 정보에 따라 달라 집니다. ' email '은 항상 전송 됩니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 web API의 예상 응답 형식

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 특성 컬렉션 페이지의 다음 단계로 계속 진행 되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환 하는 경우 클레임은 다음을 수행 합니다.

- 특성 컬렉션 페이지의 입력 필드를 미리 채웁니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조 하세요.

#### <a name="blocking-response"></a>차단 응답

차단 응답은 사용자 흐름을 종료 합니다. 사용자에 게 블록 페이지를 표시 하 여 사용자 흐름의 연속을 중지 하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공 하는가 표시 됩니다 `userMessage` .

[차단 응답](#example-of-a-blocking-response)의 예를 참조 하세요.

## <a name="before-creating-the-user"></a>사용자를 만들기 전에

등록 프로세스의이 단계에서 API 커넥터는 포함 된 경우 특성 컬렉션 페이지 다음에 호출 됩니다. 이 단계는 항상 Azure AD에서 사용자 계정을 만들기 전에 호출 됩니다. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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
API로 전송 되는 정확한 클레임은 사용자 로부터 수집 된 정보나 id 공급자가 제공 하는 정보에 따라 달라 집니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 web API의 예상 응답 형식

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답
- 유효성 검사 응답

#### <a name="continuation-response"></a>연속 응답
연속 응답은 사용자 흐름이 다음 단계로 계속 진행 되어야 함을 나타냅니다. 디렉터리에서 사용자를 만듭니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환 하는 경우 클레임은 다음을 수행 합니다.

- 특성 컬렉션 페이지에서 클레임에 이미 할당 된 모든 값을 재정의 합니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조 하세요.

#### <a name="blocking-response"></a>차단 응답
차단 응답은 사용자 흐름을 종료 합니다. 사용자에 게 블록 페이지를 표시 하 여 사용자 흐름의 연속을 중지 하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공 하는가 표시 됩니다 `userMessage` .

[차단 응답](#example-of-a-blocking-response)의 예를 참조 하세요.

### <a name="validation-error-response"></a>유효성 검사-오류 응답
 API가 유효성 검사 오류 응답으로 응답 하는 경우 사용자 흐름은 특성 컬렉션 페이지에 유지 `userMessage` 되 고 사용자에 게 표시 됩니다. 그런 다음 사용자는 양식을 편집 하 고 다시 제출할 수 있습니다. 이 유형의 응답을 입력 유효성 검사에 사용할 수 있습니다.

[유효성 검사 오류 응답](#example-of-a-validation-error-response)의 예를 참조 하세요.

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

| 매개 변수                                          | 형식              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                                            | String            | 예      | API 버전입니다.                                                                                                                                                                                                                                                                |
| 작업                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | 사용자 흐름에 대 한 API 커넥터 구성 및 **사용자 특성** 에서 **받을 클레임** 으로 선택한 경우에는 해당 값을 디렉터리에 저장할 수 있습니다. **응용 프로그램 클레임** 으로 선택한 경우 토큰에서 값을 반환할 수 있습니다.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | 반환 된 클레임에는를 포함할 필요가 없습니다 `_<extensions-app-id>_` . 반환 된 값은 사용자 로부터 수집 된 값을 덮어쓸 수 있습니다. 응용 프로그램의 일부로 구성 된 경우 토큰에서 반환 될 수도 있습니다.  |

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

| 매개 변수   | 형식   | 필수 | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 버전     | String | 예      | API 버전입니다.                                                    |
| 작업      | String | 예      | 값은 이어야 합니다. `ShowBlockPage`                                              |
| userMessage | String | 예      | 사용자에게 표시할 메시지입니다.                                            |

**차단 응답이 있는 최종 사용자 환경**

![예제 블록 페이지](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>유효성 검사 오류 응답의 예

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| 매개 변수   | 형식    | 필수 | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 버전     | String  | 예      | API의 버전입니다.                                                    |
| 작업      | String  | 예      | 값은 `ValidationError`이어야 합니다.                                           |
| 상태      | Integer | 예      | `400`ValidationError 응답의 값 이어야 합니다.                        |
| userMessage | String  | 예      | 사용자에게 표시할 메시지입니다.                                            |

> [!NOTE]
> HTTP 상태 코드는 응답 본문의 "status" 값 외에도 "400" 이어야 합니다.

**유효성 검사 오류 응답과 함께 최종 사용자 환경**

![유효성 검사 페이지 예](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>모범 사례 및 문제 해결 방법

### <a name="using-serverless-cloud-functions"></a>서버 리스 클라우드 함수 사용
Azure Functions의 HTTP 트리거와 같은 서버 리스 함수는 API 커넥터에서 사용할 API 끝점을 만드는 간단한 방법을 제공 합니다. 서버를 사용 하지 않는 클라우드 함수를 사용 하 여 유효성 검사 논리를 수행 하 고 [등록을 특정](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)메일 도메인으로 제한할 수 있습니다. 서버를 사용 하지 않는 클라우드 함수는 보다 복잡 한 시나리오에 대해 다른 웹 Api, 사용자 저장소 및 기타 클라우드 서비스를 호출 하 고 호출할 수도 있습니다.

### <a name="best-practices"></a>모범 사례
다음 사항을 확인합니다.
* API는 위에 설명 된 대로 API 요청 및 응답 계약을 따라 수행 됩니다. 
* API 커넥터의 **끝점 URL** 은 올바른 api 끝점을 가리킵니다.
* API는 수신 된 클레임의 null 값을 명시적으로 확인 합니다.
* API는 유연 하 게 사용자 환경을 보장 하기 위해 최대한 신속 하 게 응답 합니다.
    * 서버를 사용 하지 않는 함수 또는 확장 가능한 웹 서비스를 사용 하는 경우 API를 "활성" 또는 "웜" 상태로 유지 하는 호스팅 계획을 사용 합니다. 프로덕션 환경. Azure Functions의 경우 [프리미엄 요금제](../../azure-functions/functions-scale.md) 를 사용 하는 것이 좋습니다.

### <a name="use-logging"></a>로깅 사용
일반적으로 [application insights](../../azure-functions/functions-monitoring.md)와 같은 웹 api 서비스에서 사용 하도록 설정 된 로깅 도구를 사용 하 여 예기치 않은 오류 코드, 예외 및 성능 저하에 대 한 API를 모니터링 하는 것이 유용 합니다.
* HTTP 200 또는 400이 아닌 HTTP 상태 코드를 모니터링 합니다.
* 401 또는 403 HTTP 상태 코드는 일반적으로 인증에 문제가 있음을 나타냅니다. Api 커넥터에서 API의 인증 계층 및 해당 구성을 두 번 확인 합니다.
* 필요한 경우 개발에서 더 적극적인 로깅 수준 (예: "추적" 또는 "디버그")을 사용 합니다.
* 긴 응답 시간에 대 한 API를 모니터링 합니다.

## <a name="next-steps"></a>다음 단계
- [셀프 서비스 등록에 사용자 지정 승인 워크플로를 추가](self-service-sign-up-add-approvals.md) 하는 방법을 알아봅니다.
- [빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 시작 해 보세요.
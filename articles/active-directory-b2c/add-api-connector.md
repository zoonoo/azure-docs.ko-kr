---
title: 사용자 흐름에 API 커넥터 추가 (미리 보기)
description: 사용자 흐름에서 사용할 API 커넥터를 구성 합니다.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 857429ab5fd2e2ea9a0cb0173015ceba4bb0bacb
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504114"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>등록 사용자 흐름에 API 커넥터 추가 (미리 보기)

> [!IMPORTANT]
> 등록을 위한 API 커넥터는 Azure AD B2C의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Api 커넥터](api-connectors-overview.md)를 사용 하려면 먼저 api 커넥터를 만든 다음 사용자 흐름에서 사용 하도록 설정 합니다.

## <a name="create-an-api-connector"></a>API 커넥터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스**에서 **Azure AD B2C**를 선택 합니다.
4. **Api 커넥터 (미리 보기)** 를 선택한 다음 **새 api 커넥터**를 선택 합니다.

   ![새 API 커넥터 추가](./media/add-api-connector/api-connector-new.png)

5. 호출에 대 한 표시 이름을 제공 합니다. 예를 들어 **사용자 정보를 확인**합니다.
6. API 호출에 대 한 **끝점 URL** 을 제공 합니다.
7. API에 대 한 인증 정보를 제공 합니다.

   - 현재 기본 인증만 지원 됩니다. 개발 목적으로 기본 인증 없이 API를 사용 하려면 API에서 무시할 수 있는 ' 더미 ' **사용자 이름** 및 **암호** 를 입력 하면 됩니다. API 키를 사용 하 여 Azure 함수와 함께 사용 하기 위해 코드를 **끝점 URL** 에 쿼리 매개 변수로 포함할 수 있습니다 (예: https []() ://contoso.azurewebsites.net/api/endpoint<b>? code = 0123456789</b>).

   ![새 API 커넥터 구성](./media/add-api-connector/api-connector-config.png)
8. **저장**을 선택합니다.

## <a name="the-request-sent-to-your-api"></a>API로 전송 된 요청
API 커넥터는 **HTTP POST** 요청으로 구체화 되어 사용자 특성 (' 클레임 ')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user#properties) 사용자 속성과 유사 하 게 직렬화 됩니다. 

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

**Azure AD B2C**  >  **사용자 특성** 환경에 나열 된 사용자 속성 및 사용자 지정 특성만 요청에서 보낼 수 있습니다.

사용자 지정 특성은 디렉터리의 **extension_ \<extensions-app-id> _CustomAttribute**  형식으로 존재 합니다. API는 동일한 직렬화 된 형식으로 클레임을 수신 해야 합니다. 사용자 지정 특성에 대 한 자세한 내용은 [Azure Active Directory B2C에서 사용자 지정 특성 정의](user-flow-custom-attributes.md)를 참조 하세요.

또한 **UI 로캘 (' ui_locales ')** 클레임은 모든 요청에 기본적으로 전송 됩니다. API에서 다국어 응답을 반환 하는 데 사용할 수 있는 사용자의 로캘을 장치에 구성 된 대로 제공 합니다.

> [!IMPORTANT]
> API 끝점이 호출 될 때 클레임에 값이 없으면 클레임은 API로 보내지지 않습니다. API는 클레임이 요청에 없는 경우를 명시적으로 확인 하 고 처리 하도록 설계 해야 합니다.

> [!TIP] 
> [**id (' id ')**](https://docs.microsoft.com/graph/api/resources/objectidentity) 및 **전자 메일 주소 (' 메일 ')** 클레임은 API에서 사용자가 테 넌 트에 계정을 포함 하기 전에 사용자를 식별 하는 데 사용할 수 있습니다. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용 하도록 설정

등록 사용자 흐름에 API 커넥터를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스**에서 **Azure AD B2C**를 선택 합니다.
4. **사용자 흐름**을 선택 하 고 API 커넥터를 추가 하려는 사용자 흐름을 선택 합니다.
5. **Api 커넥터**를 선택 하 고 사용자 흐름에서 다음 단계에 호출 하려는 api 끝점을 선택 합니다.

   - **Id 공급자를 사용 하 여 로그인 한 후**
   - **사용자를 만들기 전에**

   ![사용자 흐름에 Api 추가](./media/add-api-connector/api-connectors-user-flow-select.png)

6. **저장**을 선택합니다.

## <a name="after-signing-in-with-an-identity-provider"></a>Id 공급자를 사용 하 여 로그인 한 후

사용자가 id 공급자 (예: Google, Facebook, & Azure AD)를 사용 하 여 인증 한 후 즉시 등록 프로세스의이 단계에서 API 커넥터가 호출 됩니다. 이 단계는 사용자 특성을 수집 하기 위해 사용자에 게 표시 되는 형식인 **_특성 컬렉션 페이지_* 앞에 나옵니다. 사용자가 로컬 계정으로 등록 하는 경우에는이 단계가 호출 되지 않습니다.

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

등록 프로세스의이 단계에서 API 커넥터는 포함 된 경우 특성 컬렉션 페이지 다음에 호출 됩니다. 이 단계는 항상 사용자 계정을 만들기 전에 호출 됩니다.

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

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

| 매개 변수                                          | Type              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                                            | String            | 예      | API 버전입니다.                                                                                                                                                                                                                                                                |
| action                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 예       | 반환 된 값은 사용자 로부터 수집 된 값을 덮어쓸 수 있습니다. _ * 응용 프로그램 클레임 * *로 선택한 경우 토큰에서 반환 될 수도 있습니다.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 예       | 클레임은를 포함할 필요가 없습니다 `_<extensions-app-id>_` . 반환 된 값은 사용자 로부터 수집 된 값을 덮어쓸 수 있습니다. **응용 프로그램 클레임**으로 선택 된 경우 토큰에서 반환 될 수도 있습니다.  |

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
| action      | String | 예      | 값은 이어야 합니다. `ShowBlockPage`                                              |
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
| 버전     | String  | 예      | API 버전입니다.                                                    |
| action      | String  | 예      | 값은 `ValidationError`이어야 합니다.                                           |
| 상태      | 정수 | 예      | `400`ValidationError 응답의 값 이어야 합니다.                        |
| userMessage | String  | 예      | 사용자에게 표시할 메시지입니다.                                            |

> [!NOTE]
> HTTP 상태 코드는 응답 본문의 "status" 값 외에도 "400" 이어야 합니다.

**유효성 검사 오류 응답과 함께 최종 사용자 환경**

![유효성 검사 페이지 예](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>모범 사례 및 문제 해결 방법

### <a name="using-serverless-cloud-functions"></a>서버 리스 클라우드 함수 사용
Azure Functions의 HTTP 트리거와 같은 서버 리스 함수는 API 커넥터에서 사용할 API 끝점을 만드는 간단한 방법을 제공 합니다. 서버를 사용 하지 않는 클라우드 함수를 사용 하 여 유효성 검사 논리를 수행 하 고 [등록을 특정](code-samples.md#api-connectors)메일 도메인으로 제한할 수 있습니다. 서버를 사용 하지 않는 클라우드 함수는 보다 복잡 한 시나리오에 대해 다른 웹 Api, 사용자 저장소 및 기타 클라우드 서비스를 호출 하 고 호출할 수도 있습니다.

### <a name="best-practices"></a>모범 사례
다음 사항을 확인합니다.
* API는 위에 설명 된 대로 API 요청 및 응답 계약을 따라 수행 됩니다. 
* API 커넥터의 **끝점 URL** 은 올바른 api 끝점을 가리킵니다.
* API는 수신 된 클레임의 null 값을 명시적으로 확인 합니다.
* API는 유연 하 게 사용자 환경을 보장 하기 위해 최대한 신속 하 게 응답 합니다.
    * 서버를 사용 하지 않는 함수 또는 확장 가능한 웹 서비스를 사용 하는 경우 API를 "활성" 또는 "웜" 상태로 유지 하는 호스팅 계획을 사용 합니다. 프로덕션 환경. Azure Functions의 경우 [프리미엄 요금제](../azure-functions/functions-scale.md) 를 사용 하는 것이 좋습니다.


### <a name="use-logging"></a>로깅 사용
일반적으로 [application insights](../azure-functions/functions-monitoring.md)와 같은 웹 api 서비스에서 사용 하도록 설정 된 로깅 도구를 사용 하 여 예기치 않은 오류 코드, 예외 및 성능 저하에 대 한 API를 모니터링 하는 것이 유용 합니다.
* HTTP 200 또는 400이 아닌 HTTP 상태 코드를 모니터링 합니다.
* 401 또는 403 HTTP 상태 코드는 일반적으로 인증에 문제가 있음을 나타냅니다. Api 커넥터에서 API의 인증 계층 및 해당 구성을 두 번 확인 합니다.
* 필요한 경우 개발에서 더 적극적인 로깅 수준 (예: "추적" 또는 "디버그")을 사용 합니다.
* 긴 응답 시간에 대 한 API를 모니터링 합니다.

## <a name="next-steps"></a>다음 단계
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
- [샘플](code-samples.md#api-connectors)을 시작 해 보세요.

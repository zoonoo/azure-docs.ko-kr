---
title: Azure AD B2C의 API 커넥터 정보
description: Azure AD(Azure Active Directory) API 커넥터를 사용하여 REST API를 통해 사용자 흐름을 사용자 지정하고 확장합니다.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 04/27/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a9eeb7ed664f67e1273a7dfff701a18970cd91fd
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108174527"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>API 커넥터를 사용하여 등록 사용자 흐름 사용자 지정 및 확장

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

> [!IMPORTANT]
> 등록을 위한 API 커넥터는 Azure AD B2C의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요 

개발자 또는 IT 관리자는 API 커넥터를 사용하여 REST API와 가입 사용자 흐름을 통합함으로써 가입 환경을 사용자 지정하고 외부 시스템과 통합할 수 있습니다. 예를 들어 API 커넥터를 사용하여 다음을 수행할 수 있습니다.

- **사용자 입력 데이터 유효성 검사**. 잘못된 형식이나 유효하지 않은 사용자 데이터의 유효성을 검사합니다. 예를 들어 외부 데이터 저장소의 기존 데이터 또는 허용되는 값 목록에 대해 사용자가 제공한 데이터의 유효성을 검사할 수 있습니다. 유효하지 않은 경우 사용자에게 유효한 데이터를 제공하도록 요청하거나 사용자가 등록 흐름을 계속할 수 없도록 차단할 수 있습니다.
- **사용자 지정 승인 워크플로와 통합** 합니다. 계정 생성을 관리 및 제한하기 위해 사용자 지정 승인 시스템에 연결합니다.
- **사용자 특성을 덮어씁니다**. 사용자로부터 수집된 특성에 값 서식을 다시 지정하거나 할당합니다. 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다. 
- **사용자 ID를 확인합니다**. ID 검증 서비스를 사용하여 계정 생성 결정에 보안 수준을 강화합니다.
- **사용자 지정 비즈니스 논리 실행**. 클라우드 시스템에서 다운스트림 이벤트를 트리거하여 푸시 알림을 전송하고, 회사 데이터베이스를 업데이트하고, 권한을 관리하고, 데이터베이스를 감사하고, 기타 사용자 지정 작업을 수행할 수 있습니다.

API 커넥터는 HTTP 엔드포인트 URL 및 API 호출 인증을 정의하여 API 엔드포인트를 호출하는 데 필요한 정보를 Azure AD B2C에 제공합니다. API 커넥터를 구성한 후에는 사용자 흐름에서 특정 단계에 해당 커넥터를 사용할 수 있습니다. 사용자가 가입 흐름에서 해당 단계에 도달하면 API 커넥터가 호출되고 API에 대한 HTTP POST 요청으로 구체화하여 사용자 정보(“클레임”)를 JSON 본문의 키-값 쌍으로 전송합니다. API 응답은 사용자 흐름의 실행에 영향을 줄 수 있습니다. 예를 들어 API 응답은 사용자가 가입하지 못하도록 차단할 수 있으며, 사용자에게 정보를 다시 입력하도록 요청하거나 사용자 특성을 덮어쓰고 추가할 수 있습니다.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용할 수 있는 위치

다음과 같이 사용자 흐름에는 API 커넥터를 사용할 수 있는 두 개의 위치가 있습니다.

- ID 공급자를 사용하여 로그인한 후
- 사용자를 만들기 전

> [!IMPORTANT]
> 두 경우 모두, 사용자 로그인이 아닌 사용자 **등록** 중에 API 커넥터가 호출됩니다.

### <a name="after-signing-in-with-an-identity-provider"></a>ID 공급자를 사용하여 로그인한 후

API 커넥터는 로그인 프로세스의 해당 절차에서 ID 공급자(예: Google, Facebook, Azure AD)를 사용한 사용자 인증 후 즉시 호출됩니다. 이 단계는 사용자 특성을 수집하기 위해 사용자에게 표시되는 형식인 ***특성 모음 페이지*** 앞에 나옵니다. 이 단계는 사용자가 로컬 계정으로 등록하는 경우에는 호출되지 않습니다. 다음은 이 단계에서 사용할 수 있는 API 커넥터 시나리오의 예입니다.

- 사용자가 제공한 메일 또는 페더레이션 ID를 사용하여 기존 시스템에서 클레임을 조회합니다. 기존 시스템에서 해당 클레임을 반환하고 특성 모음 페이지를 미리 채운 다음 토큰에서 반환할 수 있도록 합니다.
- 소셜 ID를 토대로 허용 또는 차단 목록을 구현합니다.

### <a name="before-creating-the-user"></a>사용자를 만들기 전

등록 프로세스 내 이 단계에서, API 커넥터는 포함된 경우 특성 모음 페이지 다음에 호출됩니다. 이 단계는 항상 사용자 계정 생성 전에 호출됩니다. 다음은 등록 중에 이 시점에서 사용할 수 있는 시나리오의 예입니다.

- 사용자 입력 데이터의 유효성을 검사하고 데이터를 다시 제출하도록 사용자에게 요청합니다.
- 사용자가 입력한 데이터를 기반으로 사용자 등록을 차단합니다.
- 사용자 ID를 확인합니다.
- 사용자에 대한 기존 데이터를 외부 시스템을 쿼리하여 애플리케이션 토큰에 반환하거나 Azure AD에 저장합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C(Azure Active Directory B2C)의 기반이 되는 ID 환경 프레임워크를 사용하여 사용자 경험에서 RESTful API와 통합할 수 있습니다. 이 문서에서는 [RESTful 기술 프로필](restful-technical-profile.md)을 사용하여 RESTful 서비스와 상호 작용하는 사용자 경험을 만드는 방법을 보여 줍니다.

Azure AD B2C를 사용하면 RESTful 서비스를 호출하여 사용자 경험에 고유한 비즈니스 논리를 추가할 수 있습니다. ID 환경 프레임워크는 RESTful 서비스에서 데이터를 주고받아 클레임을 교환할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- **사용자 입력 데이터 유효성 검사**. 예를 들어 사용자가 제공한 메일 주소가 고객 데이터베이스에 있는지 확인하고, 없으면 오류를 표시할 수 있습니다.
- **클레임 처리**. 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 REST API는 이름의 첫 번째 문자만을 대문자로 시작하여 Azure AD B2C에 반환할 수 있습니다.
- **회사 LOB(기간 업무) 애플리케이션과 추가로 통합하여 사용자 데이터 보강**. RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 반환 클레임을 사용자의 Azure AD 계정에서 저장하여 다음, 오케스트레이션 단계에서 계산하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리 실행**. 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 사용 권한을 관리하고, 데이터베이스를 감사하고, 다른 워크플로를 수행할 수 있습니다.

![RESTful 서비스 클레임 교환 다이어그램](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> RESTful 서비스에서 Azure AD B2C로의 응답 속도가 느리거나 응답이 없는 경우 시간 제한은 30초이고 재시도 횟수는 2회입니다(총 3회 시도). 시간 제한 및 재시도 횟수 설정은 현재 구성할 수 없습니다.

## <a name="calling-a-restful-service"></a>RESTful 서비스 호출

상호 작용에는 REST API 클레임과 Azure AD B2C 간의 클레임 정보 교환이 포함됩니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필**. RESTful 서비스에 대한 호출은 지정된 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)의 [유효성 검사 기술 프로필](validation-technical-profile.md) 또는 [표시 컨트롤](display-controls.md)의 [확인 표시 컨트롤](display-control-verification.md)에서 발생합니다. 유효성 검사 기술 프로필은 사용자 경험이 진행되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사하고 사용자에게 표시되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 다음 오케스트레이션 단계로 클레임을 다시 보냅니다.

- **클레임 교환**. 직접 클레임 교환은 [사용자 경험](userjourneys.md)의 오케스트레이션 단계에서 직접 REST API 기술 프로필을 호출하여 구성할 수 있습니다. 이 정의는 다음으로 제한됩니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사하고 애플리케이션에 반환되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 다음 오케스트레이션 단계로 클레임을 다시 보냅니다.

사용자 지정 정책으로 정의된 사용자 경험의 모든 단계에서 REST API 호출을 추가할 수 있습니다. 예를 들어 REST API를 다음과 같이 호출할 수 있습니다.

- 로그인하는 동안 Azure AD B2C에서 자격 증명의 유효성을 검사하기 직전에
- 로그인 직후에
- Azure AD B2C에서 새 계정을 디렉터리에 만들기 전에
- Azure AD B2C에서 새 계정을 디렉터리에 만든 후에
- Azure AD B2C에서 액세스 토큰을 발급하기 전에

![유효성 검사 기술 프로필 컬렉션](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>데이터 전송

[RESTful 기술 프로필](restful-technical-profile.md)에서 `InputClaims` 요소에는 RESTful 서비스로 보낼 클레임 목록이 포함되어 있습니다. 클레임의 이름을 RESTful 서비스에 정의된 이름으로 매핑하고 기본값을 설정한 후 [클레임 확인자](claim-resolver-overview.md)를 사용할 수 있습니다.

SendClaimsIn 특성을 사용하여 RESTful 클레임 공급자로 입력 클레임을 전송하는 방법을 구성할 수 있습니다. 사용 가능한 값은

- **본문**: JSON 형식의 HTTP POST 요청 본문으로 전송됩니다.
- **양식**: 앰퍼샌드 '&'로 구분된 키 값 형식으로 HTTP POST 요청 본문에서 전송됩니다.
- **헤더**: HTTP GET 요청 헤더로 전송됩니다.
- **QueryString**: HTTP GET 요청 쿼리 문자열로 전송됩니다.

**본문** 옵션이 구성된 경우 REST API 기술 프로필을 사용하여 복잡한 JSON 페이로드를 엔드포인트로 보낼 수 있습니다. 자세한 내용은 [JSON 페이로드 전송](restful-technical-profile.md#send-a-json-payload)을 참조하세요.

## <a name="receiving-data"></a>데이터 수신

[RESTful 기술 프로필](restful-technical-profile.md)의 `OutputClaims` 요소에는 REST API에서 반환된 클레임 목록이 포함됩니다. 정책에 정의된 클레임 이름을 REST API에서 정의된 이름에 매핑해야 할 수도 있습니다. DefaultValue 특성을 설정하기만 하면, REST API ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

RESTful 클레임 공급자가 구문 분석한 출력 클레임은 항상 다음과 같이 플랫 JSON 본문 응답을 구문 분석할 것으로 예상됩니다.

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

출력 클레임은 다음 xml 코드 조각과 비슷합니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

중첩된 JSON 본문 응답을 구문 분석하려면 ResolveJsonPathsInJsonTokens 메타데이터를 true로 설정합니다. 출력 클레임에서 PartnerClaimType을 출력하려는 JSON 경로 요소로 설정합니다.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


출력 클레임은 다음 xml 코드 조각과 비슷합니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>REST API 지역화

RESTful 기술 프로필에서 현재 세션의 언어/로캘을 전송하고, 필요한 경우 지역화된 오류 메시지를 발생시킬 수 있습니다. [클레임 확인자](claim-resolver-overview.md)를 사용하여 사용자 언어와 같은 컨텍스트 클레임을 보낼 수 있습니다. 다음 예제에서는 이 시나리오를 나타내는 RESTful 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>오류 메시지 처리

REST API가 "CRM 시스템에서 사용자를 찾을 수 없습니다."와 같은 오류 메시지를 반환해야 할 수 있습니다. 오류가 발생하면 REST API는 HTTP 409 오류 메시지(충돌 응답 상태 코드)를 반환해야 합니다. 자세한 내용은 [RESTful 기술 프로필](restful-technical-profile.md#returning-validation-error-message)을 참조하세요.

이 동작은 유효성 검사 기술 프로필에서 REST API 기술 프로필을 호출하는 방식으로만 수행할 수 있습니다. 이렇게 하면 사용자가 페이지의 데이터를 수정하고 페이지 제출 시 유효성 검사를 다시 실행할 수 있습니다.

사용자 경험에서 직접 REST API 기술 프로필을 참조하는 경우 사용자는 관련 오류 메시지와 함께 신뢰 당사자 애플리케이션으로 다시 리디렉션됩니다.

::: zone-end

## <a name="security-considerations"></a>보안 고려 사항

인증된 클라이언트만 통신할 수 있도록 REST API 엔드포인트를 보호합니다. REST API는 HTTPS 엔드포인트를 사용해야 합니다. authentication 형식을 다음 인증 방법 중 하나로 설정합니다.

### <a name="api-key"></a>API 키

API 키는 REST API 엔드포인트 액세스를 위해 사용자를 인증하는 데 사용되는 고유 식별자입니다. 예를 들어 [Azure Functions HTTP 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)는 `code`를 엔드포인트 URL에 쿼리 매개 변수로 포함합니다.

::: zone pivot="b2c-user-flow"

```http
https://contoso.azurewebsites.net/api/endpoint?code=0123456789 
```

API 키 인증은 프로덕션에서 단독으로 사용하면 안 됩니다. 따라서 기본 또는 인증서 인증에 대한 구성이 항상 필요합니다. 개발 목적으로 인증 방법을 구현하지 않으려는 경우(권장되지 않음) 기본 인증을 선택하고, API에서 권한 부여를 구현하는 동안 무시할 수 있는 `username` 및 `password`에 대해 임시 값을 사용할 수 있습니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

API 키는 사용자 지정 HTTP 헤더를 보낼 수 있습니다. 예를 들어 [Azure Functions HTTP 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)는 `x-functions-key` HTTP 헤더를 사용하여 요청자를 식별합니다.  

::: zone-end

### <a name="client-certificate"></a>클라이언트 인증서

클라이언트 인증서 인증은 클라이언트에서 해당 ID를 증명하기 위해 클라이언트 인증서를 서버에 제공하는 상호 인증서 기반 인증 방법입니다. 이 경우 Azure AD B2C는 업로드하는 인증서를 API 커넥터 구성의 일부로 사용합니다.  이 동작은 SSL 핸드셰이크의 일부로 발생합니다. 

그러면 API 서비스는 적절한 인증서가 있는 서비스로만 액세스를 제한할 수 있습니다. 클라이언트 인증서는 PKCS12(PFX) X.509 디지털 인증서입니다. 프로덕션 환경에서는 인증 기관에서 서명해야 합니다.

### <a name="http-basic-authentication"></a>HTTP 기본 인증

HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의되어 있습니다. Azure AD B2C는 `Authorization` 헤더에서 클라이언트 자격 증명(`username` 및 `password`)을 사용하여 HTTP 요청을 보냅니다. 자격 증명은 base64로 인코딩된 문자열 `username:password`로 형식이 지정됩니다. 그런 다음, API는 이러한 값을 확인하여 API 호출을 거부할지 여부를 결정합니다.

::: zone pivot="b2c-custom-policy"

### <a name="bearer-token"></a>전달자 토큰

전달자 토큰 인증은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)에 정의되어 있습니다. 전달자 토큰 인증에서 Azure AD B2C는 인증 헤더에서 토큰을 사용하여 HTTP 요청을 보냅니다.

```http
Authorization: Bearer <token>
```

전달자 토큰은 불투명 문자열입니다. 이 토큰은 JWT 액세스 토큰이거나 REST API에서 Azure AD B2C가 인증 헤더에서 보낼 것으로 예상하는 문자열일 수 있습니다. 
 
::: zone-end

## <a name="rest-api-platform"></a>REST API 플랫폼

REST API는 안전하게 보호되고 JSON 형식으로 클레임을 주고받을 수 있는 경우, 어떤 플랫폼에서 어떤 프로그래밍 언어로도 작성될 수 있습니다.

REST API 서비스에 대한 요청은 Azure AD B2C 서버에서 제공됩니다. REST API 서비스를 공개적으로 액세스할 수 있는 HTTPS 엔드포인트에 게시해야 합니다. REST API 호출은 Azure 데이터 센터 IP 주소에서 도착합니다.

REST API 서비스 및 기본 구성 요소(예: 데이터베이스 및 파일 시스템)를 고가용성으로 디자인합니다.


## <a name="next-steps"></a>다음 단계



::: zone pivot="b2c-user-flow"

- [사용자 흐름에 API 커넥터를 추가](add-api-connector.md)하는 방법을 알아봅니다.
- [샘플](code-samples.md#api-connectors)을 시작하세요.

::: zone-end

::: zone pivot="b2c-custom-policy"

RESTful 기술 프로필 사용 예제에 대해서는 다음 문서를 참조하세요.

- [연습: 가입 사용자 흐름에 API 커넥터 추가](add-api-connector.md)
- [연습: Azure Active Directory B2C에서 REST API 클레임 교환을 사용자 지정 정책에 추가](custom-policy-rest-api-claims-exchange.md)
- [REST API 서비스 보호](secure-rest-api.md)
- [참조: RESTful 기술 프로필](restful-technical-profile.md)

::: zone-end
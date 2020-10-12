---
title: B2C 사용자 지정 정책의 REST API 클레임 교환
titleSuffix: Azure AD B2C
description: RESTful 서비스와 상호 작용하는 Azure AD B2C 사용자 경험을 만드는 방법을 소개합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89594189"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 REST API 클레임 교환 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)의 기반이 되는 ID 환경 프레임워크를 사용하여 사용자 경험에서 RESTful API와 통합할 수 있습니다. 이 문서에서는 [RESTful 기술 프로필](restful-technical-profile.md)을 사용하여 RESTful 서비스와 상호 작용하는 사용자 경험을 만드는 방법을 보여 줍니다.

Azure AD B2C를 사용하면 RESTful 서비스를 호출하여 사용자 경험에 고유한 비즈니스 논리를 추가할 수 있습니다. ID 환경 프레임워크는 RESTful 서비스에서 데이터를 주고받아 클레임을 교환할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- **사용자 입력 데이터 유효성 검사**. 예를 들어 사용자가 제공한 메일 주소가 고객 데이터베이스에 있는지 확인하고, 없으면 오류를 표시할 수 있습니다.
- **클레임 처리**. 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 REST API는 이름의 첫 번째 문자만을 대문자로 시작하여 Azure AD B2C에 반환할 수 있습니다.
- **회사 LOB(기간 업무) 애플리케이션과 추가로 통합하여 사용자 데이터 보강**. RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 반환 클레임을 사용자의 Azure AD 계정에서 저장하여 다음, 오케스트레이션 단계에서 계산하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리 실행**. 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 사용 권한을 관리하고, 데이터베이스를 감사하고, 다른 워크플로를 수행할 수 있습니다.

![RESTful 서비스 클레임 교환 다이어그램](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> RESTful 서비스에서 Azure AD B2C 하는 속도가 느리거나 응답이 없으면 시간이 30 초이 고 재시도 횟수가 2 회 됩니다 (총 3 회 시도). 시간 제한 및 다시 시도 횟수 설정은 현재 구성할 수 없습니다.

## <a name="calling-a-restful-service"></a>RESTful 서비스 호출

상호 작용에는 REST API 클레임과 Azure AD B2C 간의 클레임 정보 교환이 포함됩니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필**. RESTful 서비스에 대한 호출은 지정된 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)의 [유효성 검사 기술 프로필](validation-technical-profile.md) 또는 [표시 컨트롤](display-controls.md)의 [확인 표시 컨트롤](display-control-verification.md)에서 발생합니다. 유효성 검사 기술 프로필은 사용자 경험이 진행되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사하고 사용자에게 표시되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

- **클레임 교환**. 직접 클레임 교환은 [사용자 경험](userjourneys.md)의 오케스트레이션 단계에서 직접 REST API 기술 프로필을 호출하여 구성할 수 있습니다. 이 정의는 다음으로 제한됩니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사하고 애플리케이션에 반환되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

사용자 지정 정책으로 정의된 사용자 경험의 모든 단계에서 REST API 호출을 추가할 수 있습니다. 예를 들어 REST API를 다음과 같이 호출할 수 있습니다.

- 로그인하는 동안 Azure AD B2C에서 자격 증명의 유효성을 검사하기 직전에
- 로그인 직후에
- Azure AD B2C에서 새 계정을 디렉터리에 만들기 전에
- Azure AD B2C에서 새 계정을 디렉터리에 만든 후에
- Azure AD B2C에서 액세스 토큰을 발급하기 전에

![유효성 검사 기술 프로필 컬렉션](media/custom-policy-rest-api-intro/validation-technical-profile.png)

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

출력 클레임은 다음과 비슷합니다.

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


출력 클레임은 다음과 같습니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>보안 고려 사항

인증된 클라이언트만 통신할 수 있도록 REST API 엔드포인트를 보호해야 합니다. REST API는 HTTPS 엔드포인트를 사용해야 합니다. AuthenticationType 메타데이터를 다음 인증 방법 중 하나로 설정합니다.

- **클라이언트 인증서**는 클라이언트 인증서 인증을 사용하여 액세스를 제한합니다. 적절한 인증서가 있는 서비스만 API에 액세스할 수 있습니다. 클라이언트 인증서를 Azure AD B2C 정책 키에 저장합니다. [클라이언트 인증서를 사용하여 RESTful 서비스 보호](secure-rest-api.md#https-client-certificate-authentication)
- **기본**은 HTTP 기본 인증을 사용하여 REST API를 보호합니다. Azure AD B2C를 포함하여 확인된 사용자만 API에 액세스할 수 있습니다. 사용자 이름 및 암호는 Azure AD B2C 정책 키에 저장됩니다. [HTTP 기본 인증을 사용하여 RESTful 서비스 보호](secure-rest-api.md#http-basic-authentication) 방법을 알아봅니다.
- **전달자**는 클라이언트 OAuth2 액세스 토큰을 사용하여 액세스를 제한합니다. 액세스 토큰은 Azure AD B2C 정책 키에 저장됩니다. [전달자 토큰을 사용하여 RESTful 서비스 보호](secure-rest-api.md#oauth2-bearer-authentication) 방법을 자세히 알아봅니다.

## <a name="rest-api-platform"></a>REST API 플랫폼
REST API는 안전하게 보호되고 [RESTful 기술 프로필](restful-technical-profile.md)에 지정된 대로 클레임을 주고받을 수 있는 경우, 어떤 플랫폼에서 어떤 프로그래밍 언어로도 작성될 수 있습니다.

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

이 작업은 유효성 검사 기술 프로필에서 REST API 기술 프로필을 호출하는 방식으로만 수행할 수 있습니다. 이렇게 하면 사용자가 페이지의 데이터를 수정하고 페이지 제출 시 유효성 검사를 다시 실행할 수 있습니다.

HTTP 409 응답은 이 오케스트레이션 단계 내에서 후속 유효성 검사 기술 프로필의 처리를 방지하는 데 필요합니다.

사용자 경험에서 직접 REST API 기술 프로필을 참조하는 경우 사용자는 관련 오류 메시지와 함께 신뢰 당사자 애플리케이션으로 다시 리디렉션됩니다.

## <a name="publishing-your-rest-api"></a>REST API 게시

REST API 서비스에 대한 요청은 Azure AD B2C 서버에서 제공됩니다. REST API 서비스를 공개적으로 액세스할 수 있는 HTTPS 엔드포인트에 게시해야 합니다. REST API 호출은 Azure 데이터 센터 IP 주소에서 도착합니다.

REST API 서비스 및 기본 구성 요소(예: 데이터베이스 및 파일 시스템)를 고가용성으로 디자인합니다.

## <a name="next-steps"></a>다음 단계

RESTful 기술 프로필 사용 예제에 대해서는 다음 문서를 참조하세요.

- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합](custom-policy-rest-api-claims-validation.md)
- [연습: Azure Active Directory B2C에서 REST API 클레임 교환을 사용자 지정 정책에 추가](custom-policy-rest-api-claims-validation.md)
- [REST API 서비스 보호](secure-rest-api.md)
- [참조: RESTful 기술 프로필](restful-technical-profile.md)

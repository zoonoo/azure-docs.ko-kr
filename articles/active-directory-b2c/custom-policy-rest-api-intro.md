---
title: REST API는 B2C 사용자 지정 정책에서 교환을 주장합니다.
titleSuffix: Azure AD B2C
description: RESTful 서비스와 상호 작용하는 Azure AD B2C 사용자 여정을 만드는 소개입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337416"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 REST API 클레임 교환 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)의 기초가 되는 ID 환경 프레임워크는 사용자 여정 내에서 RESTful API와 통합할 수 있습니다. 이 문서에서는 [RESTful 기술 프로필을](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)사용하여 RESTful 서비스와 상호 작용하는 사용자 여정을 만드는 방법을 보여 주며 있습니다.

Azure AD B2C를 사용하여 사용자 고유의 RESTful 서비스를 호출하여 사용자 여정에 고유한 비즈니스 논리를 추가할 수 있습니다. ID 환경 프레임워크는 RESTful 서비스에서 데이터를 보내서 수신하여 클레임을 교환할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- **사용자 입력 데이터의 유효성을 검사합니다.** 예를 들어 사용자가 제공한 전자 메일 주소가 고객의 데이터베이스에 있는지 확인할 수 있으며 그렇지 않은 경우 오류가 발생합니다.
- **클레임 을 처리합니다.** 사용자가 모든 소문자 또는 모든 대문자에 이름을 입력하는 경우 REST API는 대문자로만 이름을 포맷하고 Azure AD B2C로 반환할 수 있습니다.
- **회사 업무 용 업무 용 응용 프로그램과 추가 통합하여 사용자 데이터를 보강합니다.** RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 그런 다음 반환 클레임을 사용자의 Azure AD 계정에 저장하거나, 다음 오케스트레이션 단계에서 평가하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리를 실행합니다.** 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 권한을 관리하고, 데이터베이스를 감사하고, 다른 워크플로를 수행할 수 있습니다.

![RESTful 서비스 클레임 교환 의 다이어그램](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>RESTful 서비스 호출

상호 작용에는 REST API 클레임과 Azure AD B2C 간의 정보 교환이 포함됩니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필**. RESTful 서비스에 대한 호출은 지정된 [자체 어설션된 기술 프로파일의](self-asserted-technical-profile.md) [유효성 검사 기술 프로필](validation-technical-profile.md) 또는 디스플레이 [컨트롤의](display-controls.md) [확인 표시 제어](display-control-verification.md) 내에서 발생합니다. 유효성 검사 기술 프로필은 사용자 경험이 진행되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

  - 클레임을 REST API로 보냅니다.
  - 클레임의 유효성을 검사하고 사용자에게 표시되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

- **클레임 교환**. 직접 클레임 교환은 [사용자 여정의](userjourneys.md)오케스트레이션 단계에서 REST API 기술 프로필을 직접 호출하여 구성할 수 있습니다. 이 정의는 다음으로 제한됩니다.

  - 클레임을 REST API로 보냅니다.
  - 클레임의 유효성을 검사하고 응용 프로그램에 반환되는 사용자 지정 오류 메시지를 throw합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

사용자 지정 정책으로 정의된 사용자 경험의 모든 단계에서 REST API 호출을 추가할 수 있습니다. 예를 들어 REST API를 다음과 같이 호출할 수 있습니다.

- 로그인하는 동안 Azure AD B2C바로 직전에 자격 증명의 유효성을 검사합니다.
- 로그인 직후.
- Azure AD B2C가 디렉터리에서 새 계정을 생성하기 전에
- Azure AD B2C가 디렉터리에서 새 계정을 만듭니다.
- Azure AD B2C가 액세스 토큰을 발행하기 전에

![유효성 검사 기술 프로필 컬렉션](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>데이터 전송

[RESTful 기술 프로필에서](restful-technical-profile.md) `InputClaims` 요소에는 RESTful 서비스로 보낼 클레임 목록이 포함되어 있습니다. 클레임 이름을 RESTful 서비스에 정의된 이름에 매핑하고 기본값을 설정하고 [클레임 확인자를](claim-resolver-overview.md)사용할 수 있습니다.

SendClaimsIn 특성을 사용하여 입력 클레임이 RESTful 클레임 공급자에게 전송되는 방법을 구성할 수 있습니다. 사용 가능한 값은

- **본문**, HTTP POST 요청 본문에서 JSON 형식으로 전송됩니다.
- **양식**, 앰퍼샌드 '&' 분리 된 키 값 형식으로 HTTP POST 요청 본문에 전송됩니다.
- **헤더**, HTTP GET 요청 헤더로 전송됩니다.
- **쿼리 문자열**, HTTP GET 요청 쿼리 문자열로 전송됩니다.

**Body** 옵션을 구성하면 REST API 기술 프로필을 사용하면 복잡한 JSON 페이로드를 엔드포인트로 보낼 수 있습니다. 자세한 내용은 [JSON 페이로드 보내기](restful-technical-profile.md#send-a-json-payload)를 참조하십시오.

## <a name="receiving-data"></a>데이터 수신

`OutputClaims` [RESTful 기술 프로필의](restful-technical-profile.md) 요소에는 REST API에서 반환된 클레임 목록이 포함되어 있습니다. 정책에 정의된 클레임 이름을 REST API에서 정의된 이름에 매핑해야 할 수도 있습니다. DefaultValue 특성을 설정하는 한 REST API ID 공급자가 반환하지 않는 클레임을 포함할 수도 있습니다.

RESTful 클레임 공급자에 의해 구문 분석 된 출력 클레임은 항상 플랫 JSON Body 응답을 구문 분석할 것으로 예상합니다.

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

출력 클레임은 다음과 같아야 합니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

중첩된 JSON 본문 응답을 구문 분석하려면 ResolveJsonPathsInJsonTokens 메타데이터를 true로 설정합니다. 출력 클레임에서 PartnerClaimType을 출력할 JSON 경로 요소로 설정합니다.

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


출력 클레임은 다음과 같아야 합니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>보안 고려 사항

인증된 클라이언트만 통신할 수 있도록 REST API 끝점을 보호해야 합니다. REST API는 HTTPS 끝점을 사용해야 합니다. 인증 유형 메타데이터를 다음 인증 방법 중 하나로 설정합니다.

- **클라이언트 인증서는** 클라이언트 인증서 인증을 사용하여 액세스를 제한합니다. 적절한 인증서가 있는 서비스만 API에 액세스할 수 있습니다. Azure AD B2C 정책 키에 클라이언트 인증서를 저장합니다. [클라이언트 인증서를 사용하여 RESTful 서비스를 보호하는](secure-rest-api.md#https-client-certificate-authentication)방법에 대해 자세히 알아봅니다.
- **기본** 은 HTTP 기본 인증을 통해 REST API를 보호합니다. Azure AD B2C를 포함하여 확인된 사용자만 API에 액세스할 수 있습니다. 사용자 이름과 암호는 Azure AD B2C 정책 키에 저장됩니다. [HTTP 기본 인증을 사용하여 RESTful 서비스를 보호하는](secure-rest-api.md#http-basic-authentication)방법에 대해 알아봅니다.
- **Bearer는** 클라이언트 OAuth2 액세스 토큰을 사용하여 액세스를 제한합니다. 액세스 토큰은 Azure AD B2C 정책 키에 저장됩니다. [Bearer 토큰을 사용하여 RESTful 서비스를 보호하는](secure-rest-api.md#oauth2-bearer-authentication)방법에 대해 자세히 알아보십시오.

## <a name="rest-api-platform"></a>REST API 플랫폼
REST API는 모든 플랫폼을 기반으로 [하며, RESTful 기술 프로필에](restful-technical-profile.md)명시된 대로 클레임을 송수신하고 수신할 수 있는 한 모든 프로그래밍 언어로 작성될 수 있습니다.

## <a name="localize-the-rest-api"></a>나머지 API 지역화
RESTful 기술 프로필에서 현재 세션의 언어/로캘을 보내고 필요한 경우 지역화된 오류 메시지를 발생시킬 수 있습니다. 클레임 [확인자를](claim-resolver-overview.md)사용하여 사용자 언어와 같은 컨텍스트 클레임을 보낼 수 있습니다. 다음 예제에서는 이 시나리오를 보여 주는 RESTful 기술 프로필을 보여 주실 수 있습니다.

```XML
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

REST API는 "CRM 시스템에서 사용자를 찾을 수 없습니다."와 같은 오류 메시지를 반환해야 할 수 있습니다. 오류가 발생하면 REST API는 HTTP 409 오류 메시지(충돌 응답 상태 코드)를 반환해야 합니다. 자세한 내용은 [RESTful 기술 프로필을](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)참조하십시오.

이는 유효성 검사 기술 프로필에서 REST API 기술 프로필을 호출해야만 달성할 수 있습니다. 이렇게 하면 사용자가 페이지의 데이터를 수정하고 페이지 제출 시 유효성 검사를 다시 실행할 수 있습니다.

이 오케스트레이션 단계에서 후속 유효성 검사 기술 프로필이 처리되지 않도록 하려면 HTTP 409 응답이 필요합니다.

사용자 여정에서 직접 REST API 기술 프로필을 참조하면 관련 오류 메시지가 있는 신뢰할 수 있는 응용 프로그램으로 다시 리디렉션됩니다.

## <a name="publishing-your-rest-api"></a>REST API 게시

REST API 서비스에 대한 요청은 Azure AD B2C 서버에서 제공됩니다. REST API 서비스는 공개적으로 액세스할 수 있는 HTTPS 끝점에 게시되어야 합니다. REST API 호출은 Azure 데이터 센터 IP 주소에서 도착합니다.

REST API 서비스와 데이터베이스 및 파일 시스템과 같은 기본 구성 요소(예: 고가용성)를 디자인합니다.

## <a name="next-steps"></a>다음 단계

RESTful 기술 프로필 사용의 예는 다음 문서를 참조하십시오.

- [연습: 사용자 입력의 유효성 검사로 Azure AD B2C 사용자 여정에서 REST API 클레임 교환 통합](custom-policy-rest-api-claims-validation.md)
- [연습: Azure Active Directory B2C의 사용자 지정 정책에 REST API 클레임 교환 추가](custom-policy-rest-api-claims-validation.md)
- [REST API 서비스 보안](secure-rest-api.md)
- [참조: RESTful 기술 프로필](restful-technical-profile.md)
---
title: B2C 사용자 지정 정책에서 클레임 교환 REST API
titleSuffix: Azure AD B2C
description: RESTful services와 상호 작용 하는 Azure AD B2C 사용자 경험을 만드는 방법에 대해 소개 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337416"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 REST API 클레임 교환 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)의 기초가 되는 Id 경험 프레임 워크는 사용자 경험 내에서 RESTful Api와 통합할 수 있습니다. 이 문서에서는 [RESTful 기술 프로필](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)을 사용 하 여 RESTful 서비스와 상호 작용 하는 사용자 경험을 만드는 방법을 보여 줍니다.

Azure AD B2C를 사용 하 여 고유한 RESTful 서비스를 호출 하 여 사용자 경험에 고유한 비즈니스 논리를 추가할 수 있습니다. Id 경험 프레임 워크는 RESTful 서비스에서 exchange 클레임으로 데이터를 보내고 받을 수 있습니다. 예를 들어 다음 작업을 할 수 있습니다.

- **사용자 입력 데이터의 유효성을 검사**합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객의 데이터베이스에 있는지 확인 하 고 그렇지 않은 경우 오류를 표시할 수 있습니다.
- **클레임을 처리**합니다. 사용자가 모든 소문자 또는 모든 대문자에 이름을 입력 하는 경우 첫 번째 문자만 사용 하 여 이름에 서식을 지정 하 고 Azure AD B2C으로 반환할 수 REST API.
- **회사 lob (기간 업무) 응용 프로그램과의 통합을 통해 사용자 데이터를 보강**합니다. RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 그런 다음 반환 클레임을 사용자의 Azure AD 계정에 저장 하거나, 다음 오케스트레이션 단계에서 평가 하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리를 실행**합니다. 푸시 알림을 보내고, 회사 데이터베이스를 업데이트 하 고, 사용자 마이그레이션 프로세스를 실행 하 고, 사용 권한을 관리 하 고, 데이터베이스를 감사 하 고, 다른 워크플로를 수행할 수 있습니다.

![RESTful service 클레임 교환 다이어그램](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>RESTful 서비스 호출

상호 작용에는 REST API 클레임과 Azure AD B2C 간의 정보 교환 클레임 교환이 포함 됩니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필**. RESTful 서비스에 대 한 호출은 지정 된 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)의 [유효성 검사 기술 프로필](validation-technical-profile.md) 내에서 발생 하거나 [표시 컨트롤](display-controls.md)의 [확인 디스플레이 컨트롤](display-control-verification.md) 에서 발생 합니다. 유효성 검사 기술 프로필은 사용자 경험이 진행되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사 하 고 사용자에 게 표시 되는 사용자 지정 오류 메시지를 throw 합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

- **클레임 교환**. 직접 클레임 교환은 [사용자](userjourneys.md)경험의 오케스트레이션 단계에서 직접 REST API 기술 프로필을 호출 하 여 구성할 수 있습니다. 이 정의는 다음으로 제한됩니다.

  - REST API에 클레임을 보냅니다.
  - 클레임의 유효성을 검사 하 고 응용 프로그램에 반환 되는 사용자 지정 오류 메시지를 throw 합니다.
  - REST API에서 후속 오케스트레이션 단계로 클레임을 다시 보냅니다.

사용자 지정 정책으로 정의된 사용자 경험의 모든 단계에서 REST API 호출을 추가할 수 있습니다. 예를 들어 REST API를 다음과 같이 호출할 수 있습니다.

- 로그인 하는 동안 Azure AD B2C 자격 증명의 유효성을 검사 합니다.
- 로그인 직후
- Azure AD B2C 디렉터리에 새 계정을 만듭니다.
- Azure AD B2C 디렉터리에 새 계정을 만듭니다.
- Azure AD B2C 액세스 토큰을 발급 합니다.

![유효성 검사 기술 프로필 컬렉션](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>데이터 전송

[RESTful 기술 프로필](restful-technical-profile.md)에서 요소는 `InputClaims` RESTful 서비스에 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 RESTful 서비스에 정의 된 이름으로 매핑하고 기본값을 설정 하 고 [클레임 확인자](claim-resolver-overview.md)를 사용할 수 있습니다.

SendClaimsIn 특성을 사용 하 여 RESTful 클레임 공급자로 입력 클레임을 전송 하는 방법을 구성할 수 있습니다. 가능한 값은 다음과 같습니다.

- JSON 형식의 HTTP POST 요청 본문으로 전송 되는 **본문**입니다.
- 앰퍼샌드 ' & '로 구분 된 키 값 형식으로 HTTP POST 요청 본문으로 전송 되는 **폼**입니다.
- HTTP GET 요청 헤더에 전송 된 **헤더**입니다.
- HTTP GET 요청 쿼리 문자열에 전송 된 **QueryString**입니다.

**Body** 옵션을 구성 하면 REST API 기술 프로필을 사용 하 여 복잡 한 JSON 페이로드를 끝점으로 보낼 수 있습니다. 자세한 내용은 [JSON 페이로드 보내기](restful-technical-profile.md#send-a-json-payload)를 참조 하세요.

## <a name="receiving-data"></a>데이터 수신

`OutputClaims` [RESTful 기술 프로필](restful-technical-profile.md) 의 요소는 REST API에서 반환 하는 클레임 목록을 포함 합니다. 정책에 정의된 클레임 이름을 REST API에서 정의된 이름에 매핑해야 할 수도 있습니다. DefaultValue 특성을 설정 하는 한 REST API id 공급자에서 반환 되지 않은 클레임을 포함할 수도 있습니다.

RESTful 클레임 공급자가 구문 분석 한 출력 클레임은 항상 다음과 같이 플랫 JSON 본문 응답을 구문 분석할 것으로 간주 됩니다.

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

출력 클레임은 다음과 같습니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

중첩 된 JSON 본문 응답을 구문 분석 하려면 ResolveJsonPathsInJsonTokens 메타 데이터를 true로 설정 합니다. 출력 클레임에서, 출력 하려는 JSON 경로 요소로 대상 Claimtype을 설정 합니다.

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
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>보안 고려 사항

인증 된 클라이언트만 통신할 수 있도록 REST API 끝점을 보호 해야 합니다. REST API는 HTTPS 끝점을 사용 해야 합니다. AuthenticationType 메타 데이터를 다음 인증 방법 중 하나로 설정 합니다.

- 클라이언트 **인증서** 는 클라이언트 인증서 인증을 사용 하 여 액세스를 제한 합니다. 적절 한 인증서가 있는 서비스만 API에 액세스할 수 있습니다. 클라이언트 인증서를 Azure AD B2C 정책 키에 저장 합니다. [클라이언트 인증서를 사용 하 여 RESTful 서비스를 보호](secure-rest-api.md#https-client-certificate-authentication)하는 방법에 대해 자세히 알아보세요.
- **Basic** 은 HTTP 기본 인증을 사용 하 여 REST API를 보호 합니다. Azure AD B2C를 포함하여 확인된 사용자만 API에 액세스할 수 있습니다. 사용자 이름 및 암호는 Azure AD B2C 정책 키에 저장 됩니다. [HTTP 기본 인증을 사용 하 여 RESTful 서비스를 보호](secure-rest-api.md#http-basic-authentication)하는 방법을 알아봅니다.
- **전달자** 는 클라이언트 OAuth2 액세스 토큰을 사용 하 여 액세스를 제한 합니다. 액세스 토큰은 Azure AD B2C 정책 키에 저장 됩니다. [전달자 토큰을 사용 하 여 RESTful 서비스를 보호](secure-rest-api.md#oauth2-bearer-authentication)하는 방법에 대해 자세히 알아보세요.

## <a name="rest-api-platform"></a>REST API 플랫폼
REST API은 안전 하 고 [RESTful 기술 프로필](restful-technical-profile.md)에 지정 된 대로 클레임을 보내고 받을 수 있는 한 모든 플랫폼을 기반으로 하 고 모든 프로그래밍 언어로 작성 될 수 있습니다.

## <a name="localize-the-rest-api"></a>REST API 지역화
RESTful 기술 프로필에서 현재 세션의 언어/로캘을 전송 하 고, 필요한 경우 지역화 된 오류 메시지를 발생 시킬 수 있습니다. [클레임 해결 프로그램](claim-resolver-overview.md)을 사용 하 여 사용자 언어와 같은 컨텍스트 클레임을 보낼 수 있습니다. 다음 예제에서는이 시나리오를 보여 주는 RESTful 기술 프로필을 보여 줍니다.

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

REST API "CRM 시스템에서 사용자를 찾을 수 없습니다."와 같은 오류 메시지를 반환 해야 할 수 있습니다. 오류가 발생 하면 REST API HTTP 409 오류 메시지 (충돌 응답 상태 코드)를 반환 해야 합니다. 자세한 내용은 [RESTful 기술 프로필](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)을 참조 하세요.

이는 유효성 검사 기술 프로필에서 REST API 기술 프로필을 호출 하는 경우에만 수행할 수 있습니다. 이렇게 하면 사용자가 페이지의 데이터를 수정 하 고 페이지 제출 시 유효성 검사를 다시 실행할 수 있습니다.

HTTP 409 응답은이 오케스트레이션 단계 내에서 후속 유효성 검사 기술 프로필의 처리를 방지 하는 데 필요 합니다.

사용자 경험에서 직접 REST API 기술 프로필을 참조 하는 경우 해당 사용자는 해당 오류 메시지와 함께 신뢰 당사자 응용 프로그램으로 다시 리디렉션됩니다.

## <a name="publishing-your-rest-api"></a>REST API 게시

REST API 서비스에 대 한 요청은 Azure AD B2C 서버에서 제공 됩니다. REST API 서비스를 공개적으로 액세스할 수 있는 HTTPS 끝점에 게시 해야 합니다. REST API 호출은 Azure 데이터 센터 IP 주소에서 도착 합니다.

REST API 서비스 및 기본 구성 요소 (예: 데이터베이스 및 파일 시스템)를 항상 사용 가능 하도록 디자인 합니다.

## <a name="next-steps"></a>다음 단계

RESTful 기술 프로필을 사용 하는 예제는 다음 문서를 참조 하세요.

- [연습: Azure AD B2C 사용자 경험에서 사용자 입력의 유효성을 검사 하는 REST API 클레임 교환 통합](custom-policy-rest-api-claims-validation.md)
- [연습:에서 사용자 지정 정책에 클레임 교환을 추가 REST API Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [REST API 서비스 보안](secure-rest-api.md)
- [참조: 기술 프로필 RESTful](restful-technical-profile.md)
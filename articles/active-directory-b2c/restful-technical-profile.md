---
title: 사용자 지정 정책에서 RESTful 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책에 RESTful 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa14854807727506f5d697d7871c97e219c096a3
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950887"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 RESTful 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 사용자 고유의 RESTful 서비스에 대 한 지원을 제공 합니다. Azure AD B2C는 입력 클레임 컬렉션의 RESTful 서비스에 데이터를 보내고 출력 클레임 컬렉션에서 데이터를 다시 받습니다. RESTful service 통합을 사용 하면 다음을 수행할 수 있습니다.

- **사용자 입력 데이터 유효성 검사** -잘못 된 형식의 데이터를 Azure AD B2C에 유지 하지 않습니다. 사용자의 값이 유효 하지 않으면 RESTful 서비스는 사용자에 게 항목을 제공 하도록 지시 하는 오류 메시지를 반환 합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객의 데이터베이스에 있는지 확인할 수 있습니다.
- **입력 클레임 덮어쓰기** -입력 클레임의 값을 다시 입력할 수 있습니다. 예를 들어 사용자가 모든 소문자 또는 모든 대문자에 이름을 입력 하는 경우에는 첫 번째 문자만 대문자를 사용 하 여 이름을 지정할 수 있습니다.
- **사용자 데이터 보강** -회사의 lob (기간 업무) 응용 프로그램과 추가로 통합할 수 있습니다. 예를 들어 RESTful 서비스는 사용자의 전자 메일 주소를 수신 하 고 고객의 데이터베이스를 쿼리 한 다음 Azure AD B2C에 대 한 사용자의 충성도 번호를 반환할 수 있습니다. 반환 클레임을 저장 하거나, 다음 오케스트레이션 단계에서 계산 하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리 실행** -푸시 알림을 보내고, 회사 데이터베이스를 업데이트 하 고, 사용자 마이그레이션 프로세스를 실행 하 고, 사용 권한을 관리 하 고, 데이터베이스를 감사 하 고, 기타 작업을 수행할 수 있습니다.

정책이 입력 클레임을 REST API 보낼 수 있습니다. REST API는 나중에 정책에서 사용할 수 있는 출력 클레임을 반환 하거나 오류 메시지를 throw 할 수도 있습니다. 다음과 같은 방법으로 RESTful 서비스와의 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필** -유효성 검사 기술 프로필은 RESTful 서비스를 호출 합니다. 유효성 검사 기술 프로필은 사용자 경험을 계속 하기 전에 사용자가 제공한 데이터의 유효성을 검사 합니다. 유효성 검사 기술 프로필을 사용 하면 자체 어설션된 페이지에 오류 메시지가 표시 되 고 출력 클레임에서 반환 됩니다.
- **클레임 교환** -오케스트레이션 단계를 통해 RESTful 서비스에 대 한 호출을 수행 합니다. 이 시나리오에서는 오류 메시지를 렌더링할 사용자 인터페이스가 없습니다. REST API에서 오류를 반환 하는 경우 사용자는 오류 메시지와 함께 신뢰 당사자 응용 프로그램으로 다시 리디렉션됩니다.

## <a name="protocol"></a>Protocol (Protokoll)

**프로토콜** 요소의 **Name** 특성을 `Proprietary`으로 설정 해야 합니다. **Handler** 특성은 Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.

다음 예제에서는 RESTful 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 REST API 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 REST API에 정의 된 이름에 매핑할 수도 있습니다. 다음 예에서는 정책과 REST API 간의 매핑을 보여 줍니다. **GivenName** 클레임은 **firstName**으로 REST API 전송 되 고, **성의** 는 **lastName**으로 전송 됩니다. **전자 메일** 클레임이 있는 그대로 설정 되어 있습니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 요소는 REST API 보내기 전에 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 REST API에서 반환 하는 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 REST API에 정의 된 이름에 매핑해야 할 수 있습니다. `DefaultValue` 특성을 설정 하는 한 REST API id 공급자에서 반환 되지 않은 클레임을 포함할 수도 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **OutputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

다음 예제에서는 REST API에서 반환 하는 클레임을 보여 줍니다.

- **LoyaltyNumber** 클레임 이름에 매핑되는 **MembershipId** 클레임입니다.

또한 기술 프로필은 id 공급자가 반환 하지 않는 클레임을 반환 합니다.

- 기본값이 `true`로 설정 된 **loyaltyNumberIsNew** 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ServiceUrl | Igen | REST API 끝점의 URL입니다. |
| AuthenticationType | Igen | RESTful 클레임 공급자에서 수행 하는 인증 유형입니다. 가능한 값은 `None`, `Basic`또는 `ClientCertificate`입니다. `None` 값은 REST API 익명이 아님을 나타냅니다. `Basic` 값은 REST API HTTP 기본 인증을 사용 하 여 보안이 유지 됨을 나타냅니다. Azure AD B2C를 포함 하 여 확인 된 사용자만 API에 액세스할 수 있습니다. `ClientCertificate` (권장) 값은 REST API 클라이언트 인증서 인증을 사용 하 여 액세스를 제한 함을 나타냅니다. Azure AD B2C와 같이 적절 한 인증서가 있는 서비스만 서비스에 액세스할 수 있습니다. |
| SendClaimsIn | Nem | 입력 클레임이 RESTful 클레임 공급자로 전송 되는 방법을 지정 합니다. 가능한 값은 `Body` (기본값), `Form`, `Header`또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문으로 전송 되는 입력 클레임입니다. `Form` 값은 앰퍼샌드 ' & '로 구분 된 키 값 형식으로 요청 본문에 전송 되는 입력 클레임입니다. `Header` 값은 요청 헤더에 전송 되는 입력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에서 전송 되는 입력 클레임입니다. |
| ClaimsFormat | Nem | 출력 클레임의 형식을 지정 합니다. 가능한 값은 `Body` (기본값), `Form`, `Header`또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문으로 전송 되는 출력 클레임입니다. `Form` 값은 앰퍼샌드 ' & '로 구분 된 키 값 형식으로 요청 본문에 전송 되는 출력 클레임입니다. `Header` 값은 요청 헤더에 전송 되는 출력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에서 전송 되는 출력 클레임입니다. |
| DebugMode | Nem | 디버그 모드에서 기술 프로필을 실행 합니다. 디버그 모드에서 REST API는 추가 정보를 반환할 수 있습니다. 오류 메시지 반환 섹션을 참조 하세요. |

## <a name="cryptographic-keys"></a>암호화 키

인증 형식이 `None`로 설정 된 경우 **CryptographicKeys** 요소가 사용 되지 않습니다.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

인증 유형이 `Basic`로 설정 된 경우 **CryptographicKeys** 요소에는 다음 특성이 포함 됩니다.

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Igen | 인증에 사용 되는 사용자 이름입니다. |
| BasicAuthenticationPassword | Igen | 인증에 사용 되는 암호입니다. |

다음 예제에서는 기본 인증을 사용 하는 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

인증 유형이 `ClientCertificate`로 설정 된 경우 **CryptographicKeys** 요소에는 다음 특성이 포함 됩니다.

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClientCertificate | Igen | 인증에 사용할 X509 인증서 (RSA 키 집합)입니다. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>오류 메시지 반환

REST API에서 ' CRM 시스템에서 사용자를 찾을 수 없습니다. '와 같은 오류 메시지를 반환 해야 할 수 있습니다. 오류가 발생 하면 REST API는 HTTP 409 오류 메시지 (충돌 응답 상태 코드)를 다음 특성으로 반환 해야 합니다.

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| version | Igen | 1.0.0 |
| status | Igen | 409 |
| code | Nem | `DebugMode` 사용 하도록 설정 된 경우 표시 되는 RESTful 끝점 공급자의 오류 코드입니다. |
| 요청 | Nem | `DebugMode` 사용 하도록 설정 된 경우 표시 되는 RESTful 끝점 공급자의 요청 식별자입니다. |
| userMessage | Igen | 사용자에 게 표시 되는 오류 메시지입니다. |
| developerMessage | Nem | 문제에 대 한 자세한 설명 및 문제를 해결 하는 방법 `DebugMode`를 사용 하는 경우 표시 됩니다. |
| moreInfo | Nem | `DebugMode` 사용 하도록 설정 된 경우 표시 되는 추가 정보를 가리키는 URI입니다. |

다음 예에서는 JSON으로 형식이 지정 된 오류 메시지를 반환 하는 REST API을 보여 줍니다.

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

다음 예제에서는 오류 메시지 C# 를 반환 하는 클래스를 보여 줍니다.

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="examples"></a>Példák:
- [사용자 입력의 유효성 검사로 Azure AD B2C 사용자 경험에서 REST API 클레임 교환 통합](active-directory-b2c-custom-rest-api-netfw.md)
- [HTTP 기본 인증을 사용 하 여 RESTful 서비스 보호](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [클라이언트 인증서를 사용 하 여 RESTful 서비스 보호](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [연습: Azure AD B2C 사용자 경험의 REST API 클레임 교환을 사용자 입력에 대 한 유효성 검사로 통합](active-directory-b2c-rest-api-validation-custom.md)

 















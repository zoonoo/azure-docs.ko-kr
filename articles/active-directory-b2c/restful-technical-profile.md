---
title: Azure Active Directory B2C의 사용자 지정 정책에서 RESTful 기술 프로필 정의 | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 RESTful 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21a2ea861df96a057db0ec13eacd0906ed51fff1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512736"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 RESTful 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD(Active Directory) B2C에서는 고유한 RESTful 서비스를 지원합니다. Azure AD B2C는 입력 클레임 컬렉션의 RESTful 서비스로 데이터를 보내고 출력 클레임 컬렉션에 데이터를 다시 수신합니다. RESTful 서비스 통합을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- **사용자 입력 데이터의 유효성 검사** - 잘못된 형식의 데이터가 Azure AD B2C에 저장되지 않도록 방지합니다. 사용자의 값이 유효하지 않으면 RESTful 서비스는 사용자에게 항목을 제공하도록 지시하는 오류 메시지를 반환합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객 데이터베이스에서 종료되었는지 확인할 수 있습니다.
- **입력 클레임 덮어쓰기** - 입력 클레임의 값 서식을 다시 지정할 수 있습니다. 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다.
- **사용자 데이터 보강** - 회사 LOB(기간 업무) 애플리케이션과 추가로 통합할 수 있습니다. 예를 들어 Your RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 반환 클레임을 저장하거나, 다음 오케스트레이션 단계에서 계산하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리 실행** - 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 사용 권한을 관리하고, 데이터베이스를 감사하고, 다른 작업을 수행할 수 있습니다.

사용자 정책에서 입력 클레임을 REST API로 보낼 수 있습니다. REST API는 나중에 정책에서 사용할 수 있는 출력 클레임을 반환하거나, 오류 메시지를 throw할 수 있습니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필** - 유효성 검사 기술 프로필은 RESTful 서비스를 호출합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 오류 메시지가 자체 어설션된 페이지에 표시되고 출력 클레임에 반환됩니다.
- **클레임 교환** - 오케스트레이션 단계를 통해 RESTful 서비스가 호출됩니다. 이 시나리오에서는 오류 메시지를 렌더링하는 사용자 인터페이스가 없습니다. REST API가 오류를 반환하는 경우 오류 메시지와 함께 사용자가 신뢰 당사자 애플리케이션으로 다시 리디렉션됩니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제는 RESTful 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 REST API로 보낼 클레임 목록을 포함합니다. 클레임 이름을 REST API에서 정의된 이름에 매핑할 수도 있습니다. 다음 예제는 정책과 REST API 간의 매핑을 보여 줍니다. **givenName**은 REST API에 **firstName**으로 전송되는 반면, **surname**은 **lastName**으로 전송됩니다. **email** 클레임은 현재 상태대로 설정됩니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 요소는 REST API로 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 REST API에서 반환된 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 REST API에서 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, REST API ID 공급자에서 반환되지 않은 클레임을 포함할 수도 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

다음 예제는 REST API에서 반환된 클레임을 보여 줍니다.

- **loyaltyNumber** 클레임 이름에 매핑된 **MembershipId** 클레임입니다.

기술 프로필은 ID 공급자에서 반환되지 않은 클레임도 반환합니다. 

- 기본값이 `true`로 설정된 **loyaltyNumberIsNew** 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ServiceUrl | 예 | REST API 엔드포인트의 URL입니다. | 
| AuthenticationType | 예 | RESTful 클레임 공급자가 수행하는 인증 형식입니다. 가능한 값은 `None`, `Basic` 또는 `ClientCertificate`입니다. `None` 값은 REST API가 익명이 아님을 나타냅니다. `Basic` 값은 REST API가 HTTP 기본 인증으로 보호됨을 나타냅니다. Azure AD B2C를 포함하여 확인된 사용자만 API에 액세스할 수 있습니다. `ClientCertificate`(권장) 값은 REST API가 클라이언트 인증서 인증을 사용한 액세스를 제한함을 나타냅니다. Azure AD B2C와 같은 적절한 인증서를 가진 서비스만 사용자 서비스에 액세스할 수 있습니다. | 
| SendClaimsIn | 아닙니다. | 입력 클레임이 RESTful 클레임 공급자에게 전송되는 방법을 지정합니다. 가능한 값은 `Body`(기본값), `Form`, `Header` 또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문에 전송되는 입력 클레임입니다. `Form` 값은 앰퍼샌드 '&'로 구분된 키 값 형식의 요청 본문에 전송되는 입력 클레임입니다. `Header` 값은 요청 헤더에 전송되는 입력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에 전송되는 입력 클레임입니다. | 
| ClaimsFormat | 아닙니다. | 출력 클레임의 형식을 지정합니다. 가능한 값은 `Body`(기본값), `Form`, `Header` 또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문에 전송되는 출력 클레임입니다. `Form` 값은 앰퍼샌드 '&'로 구분된 키 값 형식의 요청 본문에 전송되는 출력 클레임입니다. `Header` 값은 요청 헤더에 전송되는 출력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에 전송되는 출력 클레임입니다. | 
| DebugMode | 아닙니다. | 디버그 모드에서 기술 프로필을 실행합니다. 디버그 모드에서 REST API는 자세한 정보를 반환할 수 있습니다. 오류 메시지 반환 섹션을 참조하세요. | 

## <a name="cryptographic-keys"></a>암호화 키

인증 형식이 `None`으로 설정된 경우 **CryptographicKeys** 요소가 사용되지 않습니다.

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

인증 형식이 `Basic`으로 설정된 경우 **CryptographicKeys** 요소에 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | 예 | 인증에 사용되는 사용자 이름입니다. | 
| BasicAuthenticationPassword | 예 | 인증에 사용되는 암호입니다. |

다음 예제는 기본 인증을 사용하는 기술 프로필을 보여 줍니다.

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

인증 형식이 `ClientCertificate`으로 설정된 경우 **CryptographicKeys** 요소에 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClientCertificate | 예 | 인증에 사용할 X509 인증서(RSA 키 집합)입니다. | 

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

REST API가 'CRM 시스템에서 사용자를 찾을 수 없습니다.'와 같은 오류 메시지를 반환해야 할 수 있습니다. 오류가 발생하면 REST API는 다음 특성을 사용하여 HTTP 409 오류 메시지(충돌 응답 상태 코드)를 반환해야 합니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 버전 | 예 | 1.0.0 | 
| status | 예 | 409 | 
| code | 아닙니다. | `DebugMode`를 사용으로 설정한 경우에 표시되는 RESTful 엔드포인트 공급자의 오류 코드입니다. | 
| requestId | 아닙니다. | `DebugMode`를 사용으로 설정한 경우에 표시되는 RESTful 엔드포인트 공급자의 요청 식별자입니다. | 
| userMessage | 예 | 사용자에게 표시되는 오류 메시지입니다. | 
| developerMessage | 아닙니다. | `DebugMode`를 사용으로 설정한 경우에 표시되는, 문제점 및 해결 방법에 대한 자세한 설명입니다. | 
| moreInfo | 아닙니다. | `DebugMode`를 사용으로 설정한 경우에 표시되는, 추가 정보를 가리키는 URI입니다. | 

다음 예제는 JSON 형식의 오류 메시지를 반환하는 REST API를 보여 줍니다.

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

다음 예제는 오류 메시지를 반환하는 C# 클래스를 보여 줍니다.

```C#
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

## <a name="examples"></a>예제:
- [Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합](active-directory-b2c-custom-rest-api-netfw.md) 
- [HTTP 기본 인증을 사용하여 RESTful 서비스 보호](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [클라이언트 인증서를 사용하여 RESTful 서비스 보호](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합](active-directory-b2c-rest-api-validation-custom.md)

 















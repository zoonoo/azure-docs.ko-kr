---
title: REST API 클레임 교환-Azure Active Directory B2C
description: Active Directory B2C에서 사용자 지정 정책에 REST API 클레임 교환을 추가 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330714"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>연습:에서 사용자 지정 정책에 클레임 교환을 추가 REST API Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)를 사용 하면 id 개발자가 사용자 경험에서 RESTful API와 상호 작용을 통합할 수 있습니다. 이 연습을 마치면 [RESTful services](custom-policy-rest-api-intro.md)와 상호 작용 하는 Azure AD B2C 사용자 경험을 만들 수 있습니다.

이 시나리오에서는 회사 lob (기간 업무) 워크플로와 통합 하 여 사용자의 토큰 데이터를 보강 합니다. 로컬 또는 페더레이션된 계정으로 등록 또는 로그인 하는 동안 Azure AD B2C는 REST API를 호출 하 여 원격 데이터 원본에서 사용자의 확장 된 프로필 데이터를 가져옵니다. 이 샘플에서는 Azure AD B2C 사용자의 고유 식별자 인 objectId를 보냅니다. 그런 다음 REST API 사용자의 계정 잔액 (난수)을 반환 합니다. 이 샘플을 시작 지점으로 사용 하 여 자체 CRM 시스템, 마케팅 데이터베이스 또는 기간 업무 (lob) 워크플로와 통합할 수 있습니다.

또한 유효성 검사 기술 프로필로 상호 작용을 디자인할 수 있습니다. 이는 REST API 화면에서 데이터의 유효성을 검사 하 고 클레임을 반환 하는 경우에 적합 합니다. 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 클레임 교환 REST API 통합을 참조 하 여 사용자 입력의 유효성을 검사](custom-policy-rest-api-claims-validation.md)합니다.

## <a name="prerequisites"></a>전제 조건

- [사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.
- [Azure AD B2C 사용자 지정 정책에서 REST API 클레임 교환을 통합](custom-policy-rest-api-intro.md)하는 방법에 대해 알아봅니다.

## <a name="prepare-a-rest-api-endpoint"></a>REST API 끝점 준비

이 연습에서는 사용자의 Azure AD B2C objectId가 백 엔드 시스템에 등록 되었는지 여부를 확인 하는 REST API 있어야 합니다. 등록 된 경우 REST API 사용자 계정 잔액을 반환 합니다. 그렇지 않으면 REST API는 디렉터리에 새 계정을 등록 하 고 시작 잔액 `50.00`을 반환 합니다.

다음 JSON 코드는 REST API 끝점으로 전송 되 Azure AD B2C 데이터를 보여 줍니다. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

REST API에서 데이터의 유효성을 검사 한 후에는 다음 JSON 데이터를 사용 하 여 HTTP 200 (Ok)를 반환 해야 합니다.

```json
{
    "balance": "760.50"
}
```

REST API 끝점의 설정은이 문서의 범위를 벗어납니다. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) 샘플을 만들었습니다. [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)에서 전체 Azure 함수 코드에 액세스할 수 있습니다.

## <a name="define-claims"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공 합니다. 클레임 [스키마](claimsschema.md) 섹션 내에서 클레임을 선언할 수 있습니다. 

1. 정책의 확장 파일을 엽니다. 예를 <em> `SocialAndLocalAccounts/` </em>들면입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가 합니다.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>RESTful API 기술 프로필 구성 

[Restful 기술 프로필](restful-technical-profile.md) 은 고유한 Restful 서비스와의 상호 작용을 지원 합니다. Azure AD B2C는 `InputClaims` 컬렉션의 RESTful 서비스로 데이터를 보내고 `OutputClaims` 컬렉션에서 데이터를 다시 받습니다. <em>**`TrustFrameworkExtensions.xml`**</em> 파일에서 **ClaimsProviders** 요소를 찾고 다음과 같이 새 클레임 공급자를 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

이 예제에서는가 JSON `userLanguage` 페이로드 `lang` 내에서 REST 서비스로 전송 됩니다. `userLanguage` 클레임의 값은 현재 사용자 언어 ID를 포함 합니다. 자세한 내용은 [클레임 해결 프로그램](claim-resolver-overview.md)을 참조 하세요.

위의 `AuthenticationType` 설명은 프로덕션 환경 `AllowInsecureAuthInProduction` 으로 이동할 때 수행 해야 하는 변경 내용을 지정 합니다. 프로덕션을 위해 RESTful Api를 보호 하는 방법을 알아보려면 [Secure RESTFUL api](secure-rest-api.md)를 참조 하세요.

## <a name="add-an-orchestration-step"></a>오케스트레이션 단계 추가

[사용자 경험](userjourneys.md) 신뢰 당사자 응용 프로그램이 사용자에 대해 원하는 클레임을 가져올 수 있도록 하는 정책을 통해 명시적 경로를 지정 합니다. 사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 오케스트레이션 단계를 추가 하거나 뺄 수 있습니다. 이 경우 사용자가 REST API 호출을 통해 등록 하거나 로그인 한 후 응용 프로그램에 제공 된 정보를 보강 하는 데 사용 되는 새 오케스트레이션 단계를 추가 합니다.

1. 정책의 기본 파일을 엽니다(예: 예를 <em> `SocialAndLocalAccounts/` </em>들면입니다.
1. `<UserJourneys>` 요소를 검색합니다. 전체 요소를 복사 하 고 삭제 합니다.
1. 정책의 확장 파일을 엽니다. 예를 <em> `SocialAndLocalAccounts/` </em>들면입니다.
1. 요소를 `<UserJourneys>` 닫은 후 확장 파일에를 붙여넣습니다. `<ClaimsProviders>`
1. `<UserJourney Id="SignUpOrSignIn">`를 찾고 마지막 오케스트레이션 앞에 다음 오케스트레이션 단계를 추가 합니다.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. `Order` 을로 `8`변경 하 여 마지막 오케스트레이션 단계를 리팩터링 합니다. 최종 두 오케스트레이션 단계는 다음과 같습니다.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. **Profileedit** 및 **passwordreset** 사용자 경험의 마지막 두 단계를 반복 합니다.


## <a name="include-a-claim-in-the-token"></a>토큰에 클레임 포함 

`balance` 클레임을 신뢰 당사자 응용 프로그램에 다시 반환 하려면 출력 클레임을 <em> `SocialAndLocalAccounts/` </em> 파일에 추가 합니다. 출력 클레임을 추가 하면 사용자가 성공적으로 경험 한 후 토큰에 클레임이 발급 되며 응용 프로그램으로 전송 됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정 하 여 `balance` 를 출력 클레임으로 추가 합니다.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

**Profileedit .xml**및 **passwordreset .xml** 사용자 경험에 대해이 단계를 반복 합니다.

변경한 파일 ( *trustframeworkbase.xml*및 *Trustframeworkextensions.xml*, *signuporsignin*, *profileedit*.xml 및 *passwordreset*)을 저장 합니다. 

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테 넌 트가 포함 된 디렉터리를 선택 하 여 azure ad 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **ID 경험 프레임워크**를 선택합니다.
1. **사용자 지정 정책 업로드**를 선택 하 고 변경한 정책 파일 ( *trustframeworkbase.xml*및 Trustframeworkextensions.xml, *signu*, *TrustFrameworkExtensions.xml* *passwordreset*.xml 및 *passwordreset*)을 업로드 합니다. 
1. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 전자 메일 주소 또는 Facebook 계정을 사용 하 여 등록할 수 있습니다.
1. 애플리케이션으로 다시 전송되는 토큰에는 `balance` 클레임이 포함됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>다음 단계


## <a name="next-steps"></a>다음 단계

Api를 보호 하는 방법을 알아보려면 다음 문서를 참조 하세요.

- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](custom-policy-rest-api-claims-exchange.md)
- [RESTful API 보호](secure-rest-api.md)
- [참조: 기술 프로필 RESTful](restful-technical-profile.md)

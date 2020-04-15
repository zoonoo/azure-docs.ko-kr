---
title: 클레임 추가 및 사용자 지정 정책에서 사용자 입력 사용자 지정
titleSuffix: Azure AD B2C
description: 사용자 입력을 사용자 지정하고 Azure Active Directory B2C의 등록 또는 로그인 경험에 클레임을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56a3478f1c0dbc05eba07a5109f5bb6ba89b79d0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079882"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 클레임 추가 및 사용자 지정 정책을 사용하여 사용자 입력 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 등록 과정 중에 새 특성을 수집 합니다. 사용자의 도시를 가져오고, 드롭다운으로 구성 하 고, 제공 해야 하는지 여부를 정의 합니다.

등록 또는 로그인 사용자 경험을 사용 하 여 사용자의 초기 데이터를 수집할 수 있습니다. 나중에 프로필 편집 사용자 경험을 통해 추가 클레임을 수집할 수 있습니다. 언제 든 지 사용자가 대화형으로 정보를 수집 Azure AD B2C Id 경험 프레임 워크는 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 사용 합니다. 이 샘플에서는 다음을 수행 합니다.

1. "City" 클레임을 정의 합니다.
1. 사용자에 게 도시를 요청 합니다.
1. Azure AD B2C 디렉터리의 사용자 프로필에 도시를 보관 합니다.
1. 각 로그인의 Azure AD B2C 디렉터리에서 도시 클레임을 읽습니다.
1. 로그인 또는 등록 후에 신뢰 당사자 응용 프로그램에 도시를 반환 합니다.  

## <a name="prerequisites"></a>전제 조건

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 소셜 및 로컬 계정을 사용 하 여 등록 및 로그인 하는 데 사용 되는 사용자 지정 정책이 있어야 합니다.

## <a name="define-a-claim"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공 합니다. 클레임 [스키마](claimsschema.md) 는 클레임을 선언 하는 위치입니다. 다음 요소는 클레임을 정의하는 데 사용됩니다.

- **DisplayName** - 사용자에게 표시되는 레이블을 정의하는 문자열입니다.
- [DataType](claimsschema.md#datatype) -클레임의 유형입니다.
- **UserHelpText** - 사용자가 필요한 내용을 파악할 수 있도록 도와줍니다.
- [Userinputtype](claimsschema.md#userinputtype) -입력란, 라디오 선택, 드롭다운 목록 또는 다중 선택과 같은 입력 컨트롤의 유형입니다.

정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> 합니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 도시 클레임을 추가 합니다.  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>사용자 인터페이스에 클레임 추가

다음 기술 프로필은 사용자가 입력을 제공 해야 하는 경우 호출 되는 [자체 어설션된](self-asserted-technical-profile.md)입니다.

- **LocalAccountSignUpWithLogonEmail** -로컬 계정 등록 흐름
- **SelfAsserted-** 페더레이션 계정 처음 사용자 로그인
- **SelfAsserted-ProfileUpdate** -프로필 흐름 편집

등록 하는 동안 도시 클레임을 수집 하려면 `LocalAccountSignUpWithLogonEmail` 기술 프로필에 대 한 출력 클레임으로 추가 되어야 합니다. 확장 파일에서이 기술 프로필을 재정의 합니다. 전체 출력 클레임 목록을 지정 하 여 화면에 클레임이 표시 되는 순서를 제어 합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 ClaimsProviders를 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

페더레이션 계정으로 초기 로그인 한 후 도시 클레임을 수집 하려면 `SelfAsserted-Social` 기술 프로필에 대 한 출력 클레임으로 추가 해야 합니다. 로컬 및 페더레이션된 계정 사용자가 나중에 프로필 데이터를 편집할 수 있도록 하려면 `SelfAsserted-ProfileUpdate` 기술 프로필에 출력 클레임을 추가 합니다. 확장 파일에서 이러한 기술 프로필을 재정의 합니다. 출력 클레임의 전체 목록을 지정 하 여 화면에 클레임이 표시 되는 순서를 제어 합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 ClaimsProviders를 추가 합니다.

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>클레임 읽기 및 쓰기

다음 기술 프로필은 Active Directory Azure Active Directory 데이터를 읽고 쓰는 [기술 프로필](active-directory-technical-profile.md)입니다.  
`PersistedClaims`를 사용 하 여 사용자 프로필에 데이터를 쓰고 `OutputClaims` 각 Active Directory 기술 프로필 내에서 사용자 프로필의 데이터를 읽을 수 있습니다.

확장 파일에서 이러한 기술 프로필을 재정의 합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 새 ClaimsProviders를 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>토큰에 클레임 포함 

도시 클레임을 신뢰 당사자 응용 프로그램에 다시 반환 하려면 출력 클레임을 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 파일에 추가 합니다. 사용자 경험에 성공 하면 출력 클레임이 토큰에 추가 되 고 응용 프로그램으로 전송 됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정 하 여 출력 클레임으로 도시를 추가 합니다.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테 넌 트가 포함 된 디렉터리를 선택 하 여 azure ad 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. **사용자 지정 정책 업로드**를 선택한 후 변경한 두 정책 파일을 업로드합니다.
2. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
3. 전자 메일 주소를 사용하여 등록할 수 있습니다.

등록 화면은 다음 스크린샷 처럼 표시 됩니다.

![수정된 등록 옵션의 스크린샷](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

애플리케이션으로 다시 전송되는 토큰에는 `city` 클레임이 포함됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [ClaimsSchema](claimsschema.md) 요소에 대해 자세히 알아보세요.
- [사용자 지정 프로필 편집 정책에서 사용자 지정 특성을 사용](custom-policy-custom-attributes.md)하는 방법에 대해 알아봅니다.

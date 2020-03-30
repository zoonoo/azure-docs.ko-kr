---
title: 사용자 지정 정책에서 클레임 추가 및 사용자 입력 사용자 지정
titleSuffix: Azure AD B2C
description: 사용자 입력을 사용자 지정하고 Azure Active Directory B2C의 등록 또는 로그인 경험에 클레임을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473679"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 클레임 추가 및 사용자 지정 정책을 사용하여 사용자 입력 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C(Azure AD B2C)에서 등록 하는 동안 새 특성을 수집 합니다. 사용자의 도시를 구하고 드롭다운으로 구성하고 제공해야 하는지 여부를 정의합니다.

> [!NOTE]
> 이 샘플은 기본 제공 클레임 '도시'를 사용합니다. 대신 지원되는 [Azure AD B2C 기본 제공 특성](user-profile-attributes.md) 또는 사용자 지정 특성 중 하나를 선택할 수 있습니다. 사용자 지정 특성을 사용하려면 [정책에서 사용자 지정 특성을 사용하도록 설정합니다.](custom-policy-custom-attributes.md) 다른 기본 제공 또는 사용자 지정 특성을 사용하려면 'city'를 기본 제공 특성 *jobTitle* 또는 *extension_loyaltyId*같은 사용자 지정 특성과 같이 선택한 특성으로 바꿉꿉습니다.  

등록 또는 로그인 사용자 여정을 사용하여 사용자로부터 초기 데이터를 수집할 수 있습니다. 나중에 프로필 편집 사용자 경험을 통해 추가 클레임을 수집할 수 있습니다. Azure AD B2C가 사용자로부터 직접 정보를 대화식으로 수집할 때마다 ID 환경 프레임워크는 [자체 어설션된 기술 프로필을](self-asserted-technical-profile.md)사용합니다. 이 샘플에서는 다음을 수행합니다.

1. "도시" 클레임을 정의합니다. 
1. 사용자에게 도시에 대해 물어봅니다.
1. Azure AD B2C 디렉터리에서 사용자 프로필에 도시를 유지합니다.
1. 각 로그인에 Azure AD B2C 디렉터리에서 도시 클레임을 읽습니다.
1. 로그인 또는 가입 후 도시를 신뢰할 수 있는 파티 애플리케이션으로 되돌리십시오.  

## <a name="prerequisites"></a>사전 요구 사항

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 소셜 및 로컬 계정으로 등록 하고 로그인할 수 있는 사용자 지정 정책이 있어야 합니다.

## <a name="define-a-claim"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공합니다. [클레임 스키마는](claimsschema.md) 클레임을 선언하는 위치입니다. 다음 요소는 클레임을 정의하는 데 사용됩니다.

- **DisplayName** - 사용자에게 표시되는 레이블을 정의하는 문자열입니다.
- [데이터 유형](claimsschema.md#datatype) - 클레임의 유형입니다.
- **UserHelpText** - 사용자가 필요한 내용을 파악할 수 있도록 도와줍니다.
- [UserInputType](claimsschema.md#userinputtype) - 텍스트 상자, 라디오 선택, 드롭다운 목록 또는 여러 선택과 같은 입력 컨트롤 유형입니다.

정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [클레임스키마](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **클레임스키마** 요소에 도시 클레임을 추가합니다.  

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

사용자가 입력을 제공해야 할 때 호출되는 다음 기술 프로필은 [자체 어설션됩니다.](self-asserted-technical-profile.md)

- **LocalAccountSignUp로그온이메일** - 로컬 계정 등록 흐름.
- **자체 어설션-소셜** - 페더레이션 된 계정 처음 사용자 로그인.
- **자체 어설션-프로필 업데이트** - 프로파일 흐름을 편집합니다.

가입 하는 동안 도시 클레임을 수집 하려면 `LocalAccountSignUpWithLogonEmail` 기술 프로필에 출력 클레임으로 추가 해야 합니다. 확장자 파일에서 이 기술 프로필을 재정의합니다. 출력 클레임의 전체 목록을 지정하여 클레임이 화면에 표시되는 순서를 제어합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 클레임 공급자를 추가합니다.

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

페더레이션 된 계정으로 초기 로그인 후 도시 클레임을 수집하려면 `SelfAsserted-Social` 기술 프로필에 출력 클레임으로 추가해야 합니다. 로컬 및 페더레이션된 계정 사용자가 나중에 프로필 데이터를 편집할 수 있도록 `SelfAsserted-ProfileUpdate` 출력 클레임을 기술 프로필에 추가합니다. 확장자 파일에서 이러한 기술 프로파일을 재정의합니다. 출력 클레임의 전체 목록을 지정하여 클레임이 화면에 표시되는 순서를 제어합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 클레임 공급자를 추가합니다.

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

## <a name="read-and-write-a-claim"></a>클레임 읽기 및 작성

다음 기술 프로필은 [Azure Active Directory에](active-directory-technical-profile.md)데이터를 읽고 쓰는 Active Directory 기술 프로필입니다.  
사용자 `PersistedClaims` 프로필에 `OutputClaims` 데이터를 작성하고 각 Active Directory 기술 프로필 내의 사용자 프로필에서 데이터를 읽는 데 사용합니다.

확장자 파일에서 이러한 기술 프로파일을 재정의합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 새 클레임 공급자를 추가합니다.

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

도시 클레임을 다시 사용 당사자 응용 프로그램으로 반환하려면 파일에 <em> `SocialAndLocalAccounts/` </em> 출력 클레임을 추가합니다. 출력 클레임은 성공적인 사용자 여정 이후에 토큰에 추가되고 응용 프로그램으로 전송됩니다. 의존 파티 섹션 내의 기술 프로파일 요소를 수정하여 도시를 출력 클레임으로 추가합니다.
 
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

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD 테넌트가 포함된 디렉터리를 선택하여 Azure AD 테넌트를 포함하는 디렉터리를 사용하고 있는지 확인합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. **사용자 지정 정책 업로드**를 선택한 후 변경한 두 정책 파일을 업로드합니다.
2. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
3. 전자 메일 주소를 사용하여 등록할 수 있습니다.

가입 화면은 다음 스크린샷과 유사해야 합니다.

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

- IEF 참조에서 [클레임스키마](claimsschema.md) 요소에 대해 자세히 알아봅니다.
- [사용자 지정 프로필 편집 정책에서 사용자 지정 특성을 사용하는](custom-policy-custom-attributes.md)방법에 대해 알아봅니다.

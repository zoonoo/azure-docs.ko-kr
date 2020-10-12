---
title: Azure Active Directory B2C 로그인 상태 유지
description: Azure Active Directory B2C에서 KMSI(로그인 유지)를 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389193"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 KMSI(로그인 유지) 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 디렉터리에 로컬 계정이 있는 웹 및 네이티브 응용 프로그램 사용자에 대해 로그인 유지 (KMSI) 기능을 사용 하도록 설정할 수 있습니다. 이 기능은 사용자의 사용자 이름과 암호를 다시 입력 하 라는 메시지를 표시 하지 않고 응용 프로그램으로 돌아가는 사용자에 게 액세스 권한을 부여 합니다. 사용자가 로그아웃하면 이 액세스 권한이 철회됩니다.

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

![로그인 상태 유지 확인란을 표시 하는 등록 로그인 페이지의 예](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>사전 요구 사항

- 로컬 계정 로그인을 허용 하도록 구성 된 Azure AD B2C 테 넌 트입니다. 외부 id 공급자 계정에는 KMSI가 지원 되지 않습니다.
- [사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다.

## <a name="configure-the-page-identifier"></a>페이지 식별자 구성

KMSI를 사용 하도록 설정 하려면 콘텐츠 정의 `DataUri` 요소를 [페이지 식별자](contentdefinitions.md#datauri) `unifiedssp` 및 [페이지 버전](page-layout.md) *1.1.0* 이상으로 설정 합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다. 이 확장 파일은 사용자 지정 정책 시작 팩에 포함 된 정책 파일 중 하나로, 필수 구성 요소, [사용자 지정 정책 시작](custom-policy-get-started.md)에서 가져와야 합니다.
1. **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 정책의 **BuildingBlocks** 요소에 **contentdefinitions** 요소를 추가 합니다.

    사용자 지정 정책은 다음 코드 조각과 같이 표시 됩니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>자체 어설션된 기술 프로필에 메타 데이터 추가

KMSI 확인란을 등록 및 로그인 페이지에 추가 하려면 `setting.enableRememberMe` 메타 데이터를 true로 설정 합니다. 확장 파일에서 SelfAsserted-LocalAccountSignin-Email 기술 프로필을 재정의 합니다.

1. ClaimsProviders 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. ClaimsProviders 요소에 다음 클레임 공급자를 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 확장 파일을 저장합니다.

## <a name="configure-a-relying-party-file"></a>신뢰 당사자 파일 구성

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. `<UserJourneyBehaviors>`자식 노드가 아직 없으면 `<RelyingParty>` 노드에 추가 합니다. 바로 뒤에 위치 해야 합니다 `<DefaultUserJourney ReferenceId="User journey Id" />` (예:) `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **Ssosession** -세션이 및에 지정 된 시간 만큼 확장 되는 방법을 `SessionExpiryInSeconds` 나타냅니다  `KeepAliveInDays` . `Rolling`값 (기본값)은 사용자가 인증을 수행할 때마다 세션이 확장 됨을 나타냅니다. `Absolute`값은 지정 된 기간이 지나면 사용자가 강제로 다시 인증 함을 나타냅니다.

    - **SessionExpiryInSeconds**  - *로그인 유지* 가 사용 하도록 설정 되어 있지 않거나 사용자가 *로그인 유지*를 선택 하지 않은 경우 세션 쿠키의 수명입니다. 이 전달 되 면 세션이 만료 `SessionExpiryInSeconds` 되거나 브라우저가 닫힙니다.

    - **KeepAliveInDays** - *로그인 유지* 가 사용 되 고 사용자가 *로그인 유지*를 선택 하는 경우 세션 쿠키의 수명입니다.  값 `KeepAliveInDays` 이 값 보다 우선적으로 적용 `SessionExpiryInSeconds` 되 고 세션 만료 시간을 결정 합니다. 사용자가 브라우저를 닫고 나중에 다시 여는 경우 KeepAliveInDays 기간 내에 있는 동안에도 자동으로 로그인 할 수 있습니다.

    자세한 내용은 [사용자 경험 동작](relyingparty.md#userjourneybehaviors)을 참조 하세요.

SessionExpiryInSeconds의 값을 짧은 기간 (1200 초)으로 설정 하는 것이 좋지만, KeepAliveInDays의 값은 다음 예제와 같이 상대적으로 긴 기간 (30 일)으로 설정할 수 있습니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>정책 테스트

1. 변경 내용을 저장하고 파일을 업로드합니다.
1. 업로드 한 사용자 지정 정책을 테스트 하려면 Azure Portal에서 정책 페이지로 이동한 다음 **지금 실행**을 선택 합니다.
1. **사용자 이름** 및 **암호**를 입력 하 고 로그인 **유지**를 선택한 다음 **로그인**을 클릭 합니다.
1. Azure Portal로 돌아갑니다. 정책 페이지로 이동한 다음 **복사** 를 선택 하 여 로그인 URL을 복사 합니다.
1. 브라우저 주소 표시줄에서 `&prompt=login` 쿼리 문자열 매개 변수를 제거 하 여 사용자가 해당 요청에 대 한 자격 증명을 입력 하도록 합니다.
1. 브라우저에서 **이동**을 클릭 합니다. 이제 Azure AD B2C는 다시 로그인 하 라는 메시지를 표시 하지 않고 액세스 토큰을 발급 합니다. 

## <a name="next-steps"></a>다음 단계

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)에서 샘플 정책을 찾습니다.

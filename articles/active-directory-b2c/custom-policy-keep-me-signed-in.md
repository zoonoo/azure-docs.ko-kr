---
title: Azure Active 디렉터리 B2C에서 로그인 유지
description: Azure Active Directory B2C에서 KMSI(로그인 유지)를 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330833"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 KMSI(로그인 유지) 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C) 디렉터리에 로컬 계정이 있는 웹 및 네이티브 응용 프로그램의 사용자에 대해 KMSI(로그인 유지) 기능을 활성화할 수 있습니다. 이 기능은 사용자 이름과 암호를 다시 입력하라는 메시지를 표시하지 않고 응용 프로그램으로 돌아가는 사용자에게 액세스 권한을 부여합니다. 사용자가 로그아웃하면 이 액세스 권한이 철회됩니다.

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

![로그인 유지 확인란이 표시된 등록 페이지 예](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>사전 요구 사항

- 로컬 계정 로그인을 허용하도록 구성된 Azure AD B2C 테넌트입니다. KMSI는 외부 ID 공급자 계정에 대해 지원되지 않습니다.
- [사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다.

## <a name="configure-the-page-identifier"></a>페이지 식별자 구성

KMSI를 사용하려면 콘텐츠 `DataUri` 정의 요소를 [페이지 식별자](contentdefinitions.md#datauri) `unifiedssp` 및 [페이지 버전](page-layout.md) *1.1.0* 이상으로 설정합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>. 이 확장 파일은 사용자 지정 정책 시작 팩에 포함된 정책 파일 중 하나이며 필수 구성 조건인 [사용자 지정 정책 시작에서](custom-policy-get-started.md)가져와야 합니다.
1. **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 정책의 **빌딩 블록** 요소에 ContentDefinitions 요소를 **추가합니다.**

    사용자 지정 정책은 다음과 같은 코드 코드 조각과 같아야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>자체 어설션기술 프로필에 메타데이터 추가

등록 및 로그인 페이지에 KMSI 확인란을 추가하려면 `setting.enableRememberMe` 메타데이터를 false로 설정합니다. 확장 파일에서 자체 어설션-LocalAccountSign-전자 메일 기술 프로필을 재정의합니다.

1. ClaimsProviders 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 클레임 공급자 요소에 다음 클레임 공급자를 추가합니다.

```XML
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

## <a name="configure-a-relying-party-file"></a>의존 자 파일 구성

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. 아직 존재하지 않는 경우 노드에 `<UserJourneyBehaviors>` 자식 `<RelyingParty>` 노드를 추가합니다. 예를 들어 : `<DefaultUserJourney ReferenceId="User journey Id" />` `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`바로 그 후에 위치해야 합니다.
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - 에 지정된 `SessionExpiryInSeconds` 시간까지 세션이 `KeepAliveInDays`연장되는 방법을 나타냅니다. 값(기본값)은 `Rolling` 사용자가 인증을 수행할 때마다 세션이 확장된다는 것을 나타냅니다. 이 `Absolute` 값은 지정된 기간 이후에 사용자가 강제로 다시 인증해야 한다는 것을 나타냅니다.

    - **SessionExpiryInSeconds** - 로그인 된 *상태로 유지할* 때 세션 쿠키의 수명이 활성화되지 않았거나 사용자가 나를 *로그인 상태로 유지하지*않는 경우 . 세션이 지나거나 `SessionExpiryInSeconds` 브라우저가 닫히면 만료됩니다.

    - **KeepAliveInDays** - 로그인 된 *상태로 유지 하면* 세션 쿠키의 수명이 활성화되고 사용자가 *나를 로그인 상태로 유지합니다.*  값은 `KeepAliveInDays` `SessionExpiryInSeconds` 값보다 우선하며 세션 만료 시간을 지정합니다. 사용자가 브라우저를 닫고 나중에 다시 열면 KeepAliveInDays 기간 내에 있는 한 자동으로 로그인할 수 있습니다.

    자세한 내용은 [사용자 여정 동작을](relyingparty.md#userjourneybehaviors)참조하십시오.

다음 예제와 같이 SessionExpiryInSeconds 값을 짧은 기간(1200초)으로 설정하는 것이 좋으며 KeepAliveInDays 의 값은 비교적 긴 기간(30일)으로 설정할 수 있습니다.

```XML
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
1. 업로드한 사용자 지정 정책을 테스트하려면 Azure Portal에서 정책 페이지로 이동한 다음 **지금 실행을**선택합니다.
1. 사용자 **이름과** **암호를**입력하고 **로그인 유지를**선택한 다음 **로그인을**클릭합니다.
1. Azure 포털로 돌아갑니다. 정책 페이지로 이동한 다음 **복사를** 선택하여 로그인 URL을 복사합니다.
1. 브라우저 주소 표시줄에서 `&prompt=login` 쿼리 문자열 매개 변수를 제거하여 사용자가 해당 요청에 자격 증명을 입력하도록 합니다.
1. 브라우저에서 **이동을**클릭합니다. 이제 Azure AD B2C는 다시 로그인하라는 메시지를 표시하지 않고 액세스 토큰을 발행합니다. 

## <a name="next-steps"></a>다음 단계

여기에서 샘플 정책을 찾을 수 [있습니다.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)

---
title: Azure Active Directory B2C 로그인 상태 유지
description: Azure Active Directory B2C에서 KMSI(로그인 유지)를 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 84ba68c97f69872e39121915a6edf23aa029fa75
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161689"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 KMSI(로그인 유지) 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 디렉터리에 로컬 계정이 있는 웹 및 네이티브 응용 프로그램 사용자에 대해 로그인 유지 (KMSI) 기능을 사용 하도록 설정할 수 있습니다. 이 기능은 사용자의 사용자 이름과 암호를 다시 입력 하 라는 메시지를 표시 하지 않고 응용 프로그램으로 돌아가는 사용자에 게 액세스 권한을 부여 합니다. 사용자가 로그아웃하면 이 액세스 권한이 철회됩니다.

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

![로그인 상태 유지 확인란을 표시 하는 등록 로그인 페이지의 예](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>필수 조건

- 로컬 계정 로그인을 허용 하도록 구성 된 Azure AD B2C 테 넌 트입니다. 외부 id 공급자 계정에는 KMSI가 지원 되지 않습니다.
- [사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다.

## <a name="configure-the-page-identifier"></a>페이지 식별자 구성 

KMSI를 사용 하도록 설정 하려면 콘텐츠 정의 `DataUri` 요소를 [페이지 식별자](contentdefinitions.md#datauri) `unifiedssp` 및 [페이지 버전](page-layout.md) *1.1.0* 이상으로 설정 합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> 합니다. 이 확장 파일은 사용자 지정 정책 시작 팩에 포함 된 정책 파일 중 하나로, 필수 구성 요소, [사용자 지정 정책 시작](custom-policy-get-started.md)에서 가져와야 합니다.
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
    
1. 확장 파일을 저장합니다.



## <a name="configure-a-relying-party-file"></a>신뢰 당사자 파일 구성

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. 아직 존재 하지 않는 경우 `<UserJourneyBehaviors>` 자식 노드를 `<RelyingParty>` 노드에 추가 합니다. `<DefaultUserJourney ReferenceId="User journey Id" />`바로 뒤에 위치 해야 합니다 (예: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`).
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **Ssosession** -`SessionExpiryInSeconds` 및 `KeepAliveInDays`에 지정 된 시간에 따라 세션이 확장 되는 방법을 나타냅니다. `Rolling` 값 (기본값)은 사용자가 인증을 수행할 때마다 세션이 확장 됨을 나타냅니다. `Absolute` 값은 지정 된 기간이 지나면 사용자가 강제로 다시 인증 함을 나타냅니다.
 
    - **SessionExpiryInSeconds** - *로그인 유지* 가 사용 하도록 설정 되어 있지 않거나 사용자가 *로그인 유지*를 선택 하지 않은 경우 세션 쿠키의 수명입니다. `SessionExpiryInSeconds` 전달 되거나 브라우저가 닫힌 후 세션이 만료 됩니다.
 
    - **KeepAliveInDays** - *로그인 유지* 가 사용 되 고 사용자가 *로그인 유지*를 선택 하는 경우 세션 쿠키의 수명입니다.  `KeepAliveInDays` 값은 `SessionExpiryInSeconds` 값 보다 우선적으로 적용 되며 세션 만료 시간을 결정 합니다. 사용자가 브라우저를 닫고 나중에 다시 여는 경우 KeepAliveInDays 기간 내에 있는 동안에도 자동으로 로그인 할 수 있습니다.
    
    자세한 내용은 [사용자 경험 동작](relyingparty.md#userjourneybehaviors)을 참조 하세요.
 
SessionExpiryInSeconds의 값을 짧은 기간 (1200 초)으로 설정 하는 것이 좋지만, KeepAliveInDays의 값은 다음 예제와 같이 상대적으로 긴 기간 (30 일)으로 설정할 수 있습니다.

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

4. 변경 내용을 저장한 다음, 파일을 업로드합니다.
5. Azure Portal에서 업로드한 사용자 지정 정책을 테스트하려면 정책 페이지로 이동한 다음, **지금 실행**을 선택합니다.

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)에서 샘플 정책을 찾을 수 있습니다.

---
title: Azure AD B2C에서 암호 재설정 흐름 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 암호 다시 설정 흐름을 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f451d08dfbde643d91705f54296e9757a51c9d88
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798396"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 재설정 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>암호 다시 설정 흐름

[등록 및 로그인](add-sign-up-and-sign-in-policy.md) 경험을 통해 사용자는 **암호를 잊으셨나요?** 링크를 사용 하 여 자신의 암호를 다시 설정할 수 있습니다. 암호 다시 설정 흐름은 다음 단계를 포함 합니다.

1. 등록 및 로그인 페이지에서 사용자가 **암호를 잊으셨나요?** 링크를 클릭 합니다. Azure AD B2C는 암호 다시 설정 흐름을 시작 합니다. 
2. 사용자는 시간이 지정 된 시간 암호를 사용 하 여 전자 메일 주소를 제공 하 고 확인 합니다.
3. 그러면 사용자가 새 암호를 입력할 수 있습니다.

![암호 다시 설정 흐름](./media/add-password-reset-policy/password-reset-flow.png)

암호 다시 설정 흐름은 로그인에 대 한 암호를 사용 하 여 [전자 메일 주소](identity-provider-local.md#email-sign-in) 또는 [사용자 이름을](identity-provider-local.md#username-sign-in) 사용 하는 Azure AD B2C의 로컬 계정에 적용 됩니다.

> [!TIP]
> 사용자가 암호를 잊어버린 경우 사용자가 암호를 다시 설정 하려는 경우 셀프 서비스 암호 재설정 흐름을 통해 사용자가 암호를 변경할 수 있습니다. 사용자가 암호를 알고 변경 하려는 경우를 지원 하도록 [암호 변경 흐름](add-password-change-policy.md) 을 구성 하는 것이 좋습니다.

사용자를 임의의 암호로 Azure AD B2C로 마이그레이션한 후의 일반적인 방법은 사용자가 전자 메일 주소를 확인 하 고 처음 로그인 할 때 암호를 다시 설정 하도록 하는 것입니다. 관리자가 암호를 변경한 후에도 사용자가 암호를 다시 설정 하도록 하는 것이 일반적입니다. 이 기능을 사용 하려면 [암호 재설정 강제](force-password-reset.md) 사용을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>셀프 서비스 암호 재설정 (권장)

이제 새 암호 재설정 환경이 등록 또는 로그인 정책의 일부가 됩니다. 사용자가 **암호를 잊으셨나요?** 링크를 선택 하면 암호 잊음 환경으로 즉시 전송 됩니다. 응용 프로그램에서 [AADB2C90118 오류 코드](#password-reset-policy-legacy)를 더 이상 처리할 필요가 없으며 암호 재설정에 대 한 별도의 정책이 필요 하지 않습니다.

::: zone pivot="b2c-user-flow"

**로그인 (권장)** 또는 **등록 및 로그인 (권장)** 사용자 흐름에 대해 셀프 서비스 암호 재설정 환경을 구성할 수 있습니다. 이러한 사용자 흐름이 없는 경우 [로그인 및 등록](add-sign-up-and-sign-in-policy.md) 사용자 흐름을 만듭니다. 

등록 또는 로그인 사용자 흐름에 대해 셀프 서비스 암호 재설정을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정 하려는 등록 또는 로그인 사용자 흐름 ( **권장**)을 선택 합니다.
1. 왼쪽 메뉴의 **설정** 에서 **속성** 을 선택 합니다.
1. **암호 복잡도** 에서 **셀프 서비스 암호 재설정** 을 선택 합니다.
1. **저장** 을 선택합니다.
1. 왼쪽 메뉴의 **사용자 지정** 에서 **페이지 레이아웃** 을 선택 합니다.
1. **페이지 레이아웃 버전** 에서 **2.1.2-Current** 이상을 선택 합니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

다음 섹션에서는 사용자 지정 정책에 셀프 서비스 암호 환경을 추가 하는 방법에 대해 설명 합니다. 이 샘플은 [사용자 지정 정책 시작 팩](./custom-policy-get-started.md)에 포함 된 정책 파일을 기반으로 합니다. 

> [!TIP]
> [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset)에 대 한 "암호 재설정을 사용 하 여 등록 또는 로그인" 정책의 전체 샘플을 찾을 수 있습니다.

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>사용자가 암호를 잊으셨나요?를 선택 했음을 나타냄 링크나

사용자가 **암호를 잊으셨나요?** 링크를 선택한 정책을 나타내려면 부울 클레임을 정의 합니다. 이 클레임은 암호 찾기 기술 프로필로 사용자 경험을 안내 하는 데 사용 됩니다. 이 클레임은 토큰에도 발급 될 수 있으므로 응용 프로그램은 사용자가 암호 찾기 흐름을 통해 로그인 한 것을 인식 합니다.

클레임 [스키마](claimsschema.md)에서 클레임을 선언 합니다. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가 합니다. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>페이지 레이아웃 버전 업그레이드

[페이지 레이아웃 버전](contentdefinitions.md#migrating-to-page-layout) `2.1.2` 는 등록 또는 로그인 경험 내에서 셀프 서비스 암호 재설정 흐름을 사용 하도록 설정 하는 데 필요 합니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [Contentdefinitions](contentdefinitions.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 아래 표시 된 것 처럼 **Contentdefinition** 요소 내에서 Id **api. signuporsignin** 을 사용 하 여 **datauri** 요소를 수정 합니다.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

클레임을 시작 하기 위해 `isForgotPassword` 클레임 변환 기술 프로필이 사용 됩니다. 이 기술 프로필은 나중에 참조 될 예정입니다. 호출 될 때 클레임 값을로 설정 합니다 `isForgotPassword` `true` . `ClaimsProviders` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다. 그런 후에 다음 클레임 공급자를 추가 합니다.  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email`기술 프로필은 `setting.forgotPasswordLinkOverride` 사용자 경험에서 실행 되도록 암호 재설정 클레임 교환을 정의 합니다. 

### <a name="add-the-password-reset-sub-journey"></a>암호 재설정 하위 경험 추가

이제 사용자에 게 로그인 하 고, 등록 하 고, 암호 재설정을 수행할 수 있는 기능이 포함 됩니다. 사용자 경험을 보다 효율적으로 구성 하기 위해 [하위](subjourneys.md) 경험을 사용 하 여 암호 재설정 흐름을 처리할 수 있습니다.

사용자 경험에서 하위 경험을 호출 하 고 사용자에 게 암호 재설정 환경을 제공 하는 특정 단계를 수행 합니다. 하위 이동 `Call` 이 완료 되 면 하위 경험을 시작한 오케스트레이션 단계로 제어가 반환 되도록 형식 하위 경험을 사용 합니다.

`SubJourneys` 요소를 찾습니다. 요소가 존재 하지 않는 경우 요소 뒤에 추가 `User Journeys` 합니다. 그런 후에 다음 하위 경험을 추가 합니다.

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>사용자 경험 준비

암호를 잊으셨나요 **?** 링크를 잊어버린 암호 하위 과정에 연결 해야 합니다. 이렇게 하려면 **CombinedSignInAndSignUp** 단계의 **ClaimsProviderSelection** 요소 내에서 잊어버린 암호 하위 여행 Id를 참조 합니다.

**CombinedSignInAndSignUp** 단계를 사용 하 여 사용자 지정 사용자 경험을 사용 하지 않는 경우 다음 절차를 사용 하 여 기존 등록 또는 로그인 사용자 경험을 복제 합니다. 그렇지 않으면 다음 섹션으로 계속 진행 합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml* 을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 2 단계에서 복사한 **Userjourney** 요소의 전체 내용을 붙여넣어  요소의 자식 요소를 만듭니다.
5. 사용자 경험의 Id 이름을 바꿉니다. 예들 들어 `Id="CustomSignUpSignIn"`입니다.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>암호 찾기 링크를 잊어버린 암호 하위 여행에 연결 

사용자 경험에서 암호 하위 경험을 **ClaimsProviderSelection** 로 나타낼 수 있습니다. 이 요소를 추가 하면 암호를 잊으셨나요 **?** 링크를 잊어버린 암호 하위 과정에 연결 합니다.

1. 사용자 경험에서 또는를 포함 하는 오케스트레이션 단계 요소를 `Type="CombinedSignInAndSignUp"` 찾습니다 `Type="ClaimsProviderSelection"` . 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소에는 사용자가 로그인 하는 데 사용할 수 있는 id 공급자의 목록이 포함 되어 있습니다. 다음 줄을 추가합니다.
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 다음 오케스트레이션 단계에서 **Claim이상 변경** 요소를 추가 합니다. 다음 줄을 추가합니다.

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. 현재 단계와 다음 단계 사이에 다음 오케스트레이션 단계를 추가 합니다. 추가 하는 새 오케스트레이션 단계는 `isForgotPassword` 클레임이 존재 하는지 확인 합니다. 클레임이 있는 경우 [암호 재설정 하위](#add-the-password-reset-sub-journey)과정을 호출 합니다. 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. 새 오케스트레이션 단계를 추가한 후에는 1에서 N 사이의 정수를 건너뛰지 않고 순차적으로 단계 번호를 다시 매깁니다.

### <a name="set-the-user-journey-to-be-executed"></a>실행할 사용자 경험 설정

사용자 경험을 수정 하거나 만들었기 때문에 **신뢰** 당사자 섹션에서이 사용자 지정 정책에 대해 Azure AD B2C 실행 되는 여행을 지정 합니다. [RelyingParty](relyingparty.md) 요소 내에서 **defaultuserjourney** 요소를 찾습니다. **ClaimsProviderSelections** 를 추가한 사용자 경험의 ID와 일치 하도록 **defaultuserjourney** 를 업데이트 합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>앱에 대 한 암호 흐름 잊음 표시

응용 프로그램은 사용자가 암호 잊음 사용자 흐름을 통해 로그인 했는지 여부를 검색 해야 할 수 있습니다. **IsForgotPassword** 클레임에는이를 나타내는 부울 값이 포함 됩니다 .이 값은 응용 프로그램에 전송 된 토큰에서 실행할 수 있습니다. 필요한 경우 `isForgotPassword` **신뢰 당사자** 섹션의 출력 클레임에를 추가 합니다. 응용 프로그램에서 클레임을 확인 `isForgotPassword` 하 여 사용자가 암호를 다시 설정할지 여부를 결정할 수 있습니다.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택 하 고 다음 순서 대로 변경한 두 정책 파일을 업로드 합니다.
   1. 예를 들어 확장 정책 `TrustFrameworkExtensions.xml` 입니다.
   2. 예를 들어 신뢰 당사자 정책 `SignUpSignIn.xml` 입니다.

::: zone-end

### <a name="test-the-password-reset-flow"></a>암호 재설정 흐름 테스트

1. 테스트 하려는 등록 또는 로그인 사용자 흐름 (권장)을 선택 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 등록 또는 로그인 페이지에서 **암호를 잊으셨나요?** 를 선택 합니다.
1. 이전에 만든 계정의 전자 메일 주소를 확인 하 고 **계속** 을 선택 합니다.
1. 이제 사용자의 암호를 변경할 수 있습니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
1. 반환 토큰의 `isForgotPassword` 클레임 값을 확인 합니다. 가 있고이 true로 설정 된 경우 사용자가 암호를 재설정 했음을 나타냅니다.

## <a name="password-reset-policy-legacy"></a>암호 재설정 정책 (레거시)

[셀프 서비스 암호 재설정](#self-service-password-reset-recommended) 환경을 사용 하지 않는 경우이 링크를 클릭 하면 암호 재설정 사용자 흐름이 자동으로 트리거되지 않습니다. 대신 `AADB2C90118` 오류 코드가 애플리케이션에 반환됩니다. 응용 프로그램은 인증 라이브러리를 다시 초기화 하 여 Azure AD B2C 암호 재설정 사용자 흐름을 인증 함으로써이 오류 코드를 처리 해야 합니다.

다음 다이어그램에서:

1. 응용 프로그램에서 사용자가 로그인을 클릭 합니다. 앱은 권한 부여 요청을 시작 하 고 사용자가 Azure AD B2C 하 여 로그인을 완료 합니다. 권한 부여 요청은 **B2C_1_signup_signin** 와 같은 등록 또는 로그인 정책 이름을 지정 합니다.
1. 사용자가 암호를 **잊으셨나요?** 링크를 선택 합니다. Azure AD B2C는 AADB2C90118 오류 코드를 응용 프로그램에 반환 합니다.
1. 응용 프로그램은 오류 코드를 처리 하 고 새 권한 부여 요청을 시작 합니다. 권한 부여 요청은 **B2C_1_pwd_reset** 와 같은 암호 재설정 정책 이름을 지정 합니다.

![레거시 암호 재설정 사용자 흐름](./media/add-password-reset-policy/password-reset-flow-legacy.png)

예제를 보려면 사용자 흐름 링크를 보여 주는 [간단한 ASP.NET 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)을 살펴보세요.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

응용 프로그램의 사용자가 자신의 암호를 다시 설정 하도록 하려면 암호 재설정 사용자 흐름을 만듭니다.

1. Azure AD B2C 테넌트 개요 메뉴에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 탭에서 **암호 다시 설정** 사용자 흐름을 선택합니다. 
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다.
1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *passwordreset1* 과 같습니다.
1. **ID 공급자** 에서 **메일 주소를 사용하여 암호 재설정** 을 사용하도록 설정합니다.
1. **응용 프로그램 클레임** 에서 **자세히 표시** 를 선택 하 고 응용 프로그램으로 다시 전송 된 권한 부여 토큰에서 반환 하려는 클레임을 선택 합니다. 예를 들어 **사용자의 개체 ID** 를 선택합니다.
1. **확인** 을 선택합니다.
1. **만들기** 를 선택하여 사용자 흐름을 추가합니다. 접두사 *B2C_1* 이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭 하 고 이전에 만든 계정의 전자 메일 주소를 확인 한 다음 **계속** 을 선택 합니다.
1. 이제 사용자에 대 한 암호를 변경할 수 있습니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

사용자 지정 정책은 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 등록 및 로그인, 암호 재설정 및 프로필 편집 정책을 포함 하 여 몇 가지 미리 작성 된 정책으로 시작 팩을 제공 합니다. 자세한 내용은 [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)을 참조 하세요.

::: zone-end

## <a name="next-steps"></a>다음 단계

[암호 재설정을 강제](force-password-reset.md)설정 합니다.

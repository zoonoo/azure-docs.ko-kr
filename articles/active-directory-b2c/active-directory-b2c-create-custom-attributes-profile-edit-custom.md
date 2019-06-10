---
title: Azure Active Directory B2C에서 사용자 지정 정책에 고유한 특성 추가 | Microsoft Docs
description: 확장 속성, 사용자 지정 특성을 사용하고 사용자 인터페이스에 포함하는 방법에 대한 연습입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab7231c214060d17927e2509bee1687e2c9c87a3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507570"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: 사용자 지정 프로필 편집 정책에서 사용자 지정 특성 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Azure Active Directory) B2C 디렉터리에 사용자 지정 특성을 만듭니다. 이 새로운 특성을 프로필 편집 사용자 경험에서 사용자 지정 클레임으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

[Azure Active Directory B2C: 사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 따릅니다.

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>사용자 지정 특성을 사용하여 사용자 지정 정책을 통해 Azure AD B2C에서 고객 정보 수집
Azure AD B2C 디렉터리에는 기본 제공 특성 집합이 함께 제공됩니다. 예로는 **Given Name**, **Surname**, **City**, **Postal Code** 및 **userPrincipalName**이 있습니다. 종종 다음 예제와 같이 고유한 특성을 만들어야 합니다.
* 고객 대면 애플리케이션은 **LoyaltyNumber**와 같은 특성을 위해 유지해야 합니다.
* ID 공급자는 **uniqueUserGUID**와 같이 저장해야 하는 고유한 사용자 ID를 포함합니다.
* 사용자 지정 사용자 경험은 **migrationStatus**와 같은 사용자 상태를 위해 유지해야 합니다.

Azure AD B2C는 각 사용자 계정에 저장된 특성 집합을 확장합니다. 또한 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 이러한 특성을 읽고 쓸 수 있습니다.

확장 속성은 디렉터리에서 사용자 개체의 스키마를 확장합니다. ‘확장 속성’, ‘사용자 지정 특성’ 및 ‘사용자 지정 클레임’은 이 문서의 컨텍스트에서 동일한 항목을 참조합니다.    이름은 컨텍스트(예: 애플리케이션, 개체 또는 정책)에 따라 달라집니다.

확장 속성은 사용자에 대한 데이터를 포함할 수 있더라도 애플리케이션 개체에만 등록할 수 있습니다. 이 속성은 애플리케이션에 연결됩니다. 확장 속성을 등록하려면 애플리케이션 개체에 쓰기 권한이 있어야 합니다. 단일 개체에 100개의 확장 속성(모든 형식 및 모든 애플리케이션)을 작성할 수 있습니다. 확장 속성이 대상 디렉터리 유형에 추가되고 Azure AD B2C 디렉터리 테넌트에서 즉시 액세스할 수 있게 됩니다.
애플리케이션이 삭제되면 모든 사용자와 관련하여 포함된 모든 데이터와 함께 해당 확장 속성도 제거됩니다. 확장 속성이 애플리케이션에 의해 삭제되면 대상 디렉터리 개체에서 제거되고 값이 삭제됩니다.

확장 속성은 테넌트의 등록된 애플리케이션 컨텍스트에서만 존재합니다. 애플리케이션의 개체 ID는 ID를 사용하는 **TechnicalProfile**에 포함되어야 합니다.

>[!NOTE]
>Azure AD B2C 디렉터리는 일반적으로 `b2c-extensions-app`이라는 웹앱을 포함합니다. 이 애플리케이션은 주로 Azure Portal을 통해 만든 사용자 지정 클레임에 대한 B2C 기본 제공 정책에 사용됩니다. 고급 사용자만 이 애플리케이션을 사용하여 B2C 사용자 지정 정책에 대한 확장을 등록하는 것이 좋습니다.  
지침은 이 문서의 **다음 단계** 섹션에 포함되어 있습니다.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>확장 속성을 저장할 새 애플리케이션 만들기

1. 브라우저 세션을 열고 [Azure Portal](https://portal.azure.com)로 이동합니다. 구성하려는 B2C 디렉터리의 관리자 자격 증명으로 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 선택합니다. **추가 서비스**를 선택하여 서비스를 찾아야 합니다.
3. **앱 등록**을 선택합니다. **새 애플리케이션 등록**을 선택합니다.
4. 다음 항목을 제공합니다.
    * 웹 애플리케이션의 이름: **WebApp-GraphAPI-DirectoryExtensions**.
    * 애플리케이션 유형: **웹앱/API**.
    * 로그온 URL: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. **만들기**를 선택합니다.
6. 새로 만든 웹 애플리케이션을 선택합니다.
7. **설정** > **필수 사용 권한**을 선택합니다.
8. **Microsoft Azure Active Directory** API를 선택합니다.
9. 애플리케이션 권한에 확인 표시 입력: **디렉터리 데이터 읽기 및 쓰기**. 그런 다음 **저장**을 선택합니다.
10. **사용 권한 부여**를 선택하고 **예**를 확인합니다.
11. 다음 식별자를 클립보드에 복사하고 저장합니다.
    * **애플리케이션 ID**. 예: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **개체 ID** - 예: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>사용자 지정 정책을 수정하여 **ApplicationObjectId** 추가

[Azure Active Directory B2C: 사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 수행할 때 **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml** 및 **PasswordReset.xml**이라는 [샘플 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)을 다운로드하고 수정했습니다. 이 단계에서는 해당 파일을 추가로 수정합니다.

* **TrustFrameworkBase.xml** 파일을 열고 다음 예제에서 표시된 대로 `Metadata` 섹션을 추가합니다. `ApplicationObjectId` 값에 이전에 기록한 개체 ID 및 `ClientId` 값에 기록한 애플리케이션 ID를 삽입합니다. 

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> **TechnicalProfile**이 처음 새로 작성된 확장 속성에 작성되는 경우, 일회성 오류가 발생할 수 있습니다. 확장 속성은 처음 사용할 때 만들어집니다.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>사용자 경험에서 새 확장 속성 또는 사용자 지정 특성 사용

1. **ProfileEdit.xml** 파일을 엽니다.
2. 사용자 지정 클레임 `loyaltyId`를 추가합니다. `<RelyingParty>` 요소에서 사용자 지정 클레임을 포함하여 애플리케이션에 대한 토큰에 포함됩니다.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. **TrustFrameworkExtensions.xml** 파일을 열고 `<ClaimsSchema>` 요소 및 해당 자식 요소를 `BuildingBlocks` 요소에 추가합니다.

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. 동일한 `ClaimType` 정의를 **TrustFrameworkBase.xml**에 추가합니다. 기본 및 확장 파일에 둘 다 `ClaimType` 정의를 추가할 필요는 없습니다. 그러나 다음 단계에서는 기본 파일의 **TechnicalProfile**에 `extension_loyaltyId`를 추가합니다. 기본 파일이 없으면 정책 유효성 검사기가 기본 파일의 업로드를 거부합니다. **TrustFrameworkBase.xml** 파일에서 **ProfileEdit**라는 사용자 경험의 실행을 추적하는 데 유용할 수 있습니다. 편집기에서 동일한 이름의 사용자 경험을 검색합니다. 오케스트레이션 5단계에서 **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**를 호출하는 것을 확인합니다. 이 **TechnicalProfile**을 검색 및 검사하여 흐름에 익숙해지도록 합니다.

5. **TrustFrameworkBase.xml** 파일을 열고 `loyaltyId`를 **TechnicalProfile SelfAsserted-ProfileUpdate**에서 입력 및 출력 클레임으로 추가합니다.

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. **TrustFrameworkBase.xml** 파일에서 `loyaltyId` 클레임을 **TechnicalProfile AAD-UserWriteProfileUsingObjectId**에 추가합니다. 이렇게 추가하면 클레임 값이 디렉터리의 현재 사용자에 대한 확장 속성에서 유지됩니다.

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. **TrustFrameworkBase.xml** 파일에서 `loyaltyId` 클레임을 **TechnicalProfile AAD-UserReadUsingObjectId**에 추가하여 사용자가 로그인할 때마다 확장 특성 값을 읽어 옵니다. 지금까지 로컬 계정의 흐름에서만 **TechnicalProfile**이 변경되었습니다. 소셜/페더레이션된 계정의 흐름에 새 특성이 필요한 경우, 다른 **TechnicalProfile** 집합을 변경해야 합니다. **다음 단계** 섹션을 참조하세요.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. Azure AD B2C 블레이드를 열고 **ID 경험 프레임워크** > **사용자 지정 정책**으로 이동합니다.
1. 업로드한 사용자 지정 정책을 선택합니다. **지금 실행**을 선택합니다.
1. 메일 주소를 사용하여 등록

애플리케이션으로 다시 전송된 ID 토큰에는 **extension_loyaltyId**가 앞에 오는 사용자 지정 클레임으로 새로운 확장 속성이 포함됩니다. 다음 예제를 참조하세요.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>다음 단계

1. 다음 **TechnicalProfile**을 변경하여 새 클레임을 소셜 계정 로그인에 대한 흐름에 추가합니다. 소셜 및 페더레이션된 계정은 로그인에 이러한 두 개의 **TechnicalProfile**을 사용합니다. 이러한 계정은 사용자 개체의 로케이터로 **alternativeSecurityId**를 사용하여 사용자 데이터를 쓰고 읽습니다.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. 기본 제공 및 사용자 지정 정책 간에 동일한 확장 특성을 사용합니다. 포털 환경을 통해 확장 특성이나 사용자 지정 특성을 추가하는 경우, 해당 특성은 모든 B2C 테넌트에 존재하는 **b2c-extensions-app**을 사용하여 등록됩니다. 사용자 지정 정책에서 확장 특성을 사용하려면 다음 단계를 수행합니다.

   a. portal.azure.com의 B2C 테넌트 내에서 **Azure Active Directory**로 이동하고 **앱 등록**을 선택합니다.  
   b. **b2c-extensions-app**을 찾고 선택합니다.  
   다. **Essentials** 아래에서 **애플리케이션 ID** 및 **개체 ID**를 입력합니다.  
   d. **AAD-Common** TechnicalProfile 메타데이터에 포함합니다.  

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. 포털 환경과 일관성을 유지합니다. 사용자 지정 정책에서 사용하기 전에 포털 UI를 사용하여 이러한 특성을 만듭니다. 포털에서 **ActivationStatus** 특성을 만들 때 다음과 같이 참조해야 합니다.

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>참조

확장 속성에 대한 자세한 내용은 [디렉터리 스키마 확장 | Graph API 개념](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions) 문서를 참조하세요.

> [!NOTE]
> * **TechnicalProfile**은 엔드포인트의 이름, 메타데이터 및 프로토콜을 정의하는 요소 형식 또는 함수입니다. **TechnicalProfile**은 ID 경험 프레임워크가 수행하는 클레임의 교환을 설명합니다. 오케스트레이션 단계 또는 다른 **TechnicalProfile**에서 이 함수를 호출하면 **InputClaims** 및 **OutputClaims**가 호출자의 매개 변수로 제공됩니다.  
> * Graph API의 확장 특성은 `extension_ApplicationObjectID_attributename` 규칙을 사용하여 명명됩니다.  
> * 사용자 지정 정책은 확장 특성을 **extension_attributename**으로 참조합니다. 이 참조는 XML에서 **ApplicationObjectId**를 생략합니다.

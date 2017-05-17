---
title: "연습: B2C 사용자 지정 정책에서 한 단계로 REST API 클레임 교환 | Microsoft Docs"
description: "API와 통합하는 Azure Active Directory B2C 사용자 지정 정책에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27d066cc6f985565a765989837e4a2744387a628
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-an-orchestration-step"></a>연습: Azure AD B2C 사용자 경험에서 오케스트레이션 단계로 REST API 클레임 교환 통합

**Identity Experience Framework**(IEF) 기반 Azure AD B2C를 통해 ID 개발자는 사용자 경험에서 RESTful API와의 상호 작용을 통합할 수 있습니다.  

이 연습 끝부분에서는 RESTful 서비스와 상호 작용하는 Azure AD B2C 사용자 경험을 만들 수 있습니다.

IEF는 클레임으로 데이터를 보내고 다시 클레임으로 데이터를 받습니다.  REST API 클레임 교환은 오케스트레이션 단계로 디자인할 수 있습니다.

- 이렇게 하면 외부 작업을 트리거할 수 있는데, 예를 들어 외부 데이터베이스에 이벤트를 로그할 수 있습니다.
- 값을 가져와 나중에 사용자 데이터베이스에 저장하는 데도 사용할 수 있습니다.
- 수신된 클레임은 나중에 실행 흐름을 변경하는 데 사용할 수 있습니다.

상호 작용은 유효성 검사 프로필로도 디자인할 수 있습니다. 이에 대한 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 사용자 입력에 대한 유효성 검사로 REST API 클레임 교환 통합](active-directory-b2c-rest-api-validation-custom.md)을 참조하세요.

시나리오는 사용자가 프로일 편집을 수행할 때 외부 시스템에서 사용자를 조회하고 해당 사용자가 등록된 도시를 가져 와서 해당 특성을 클레임으로 응용 프로그램에 다시 반환하려는 것입니다.

## <a name="prerequisites"></a>필수 조건

- [시작하기](active-directory-b2c-get-started-custom.md)에 설명된 대로 로컬 계정 등록/로그인을 완료하기 위한 Azure AD B2C 테넌트 구성
- 상호 작용할 REST API 끝점 - 이 연습에서는 예로 매우 단순한 Azure Function Apps WebHook 사용
- **권장**: [유효성 검증 단계로 REST API 클레임 교환을](active-directory-b2c-rest-api-validation-custom.md) 완료

## <a name="step-1---prepare-the-rest-api-function"></a>1단계 - REST API 함수 준비

> [!NOTE]
> REST API 함수를 설정하는 것은 이 문서의 범위를 벗어나는 것입니다. [Azure Function Apps](https://docs.microsoft.com/azure/azure-functions/functions-reference)는 클라우드에서 RESTful 서비스를 만들 수 있는 뛰어난 도구 키트를 제공합니다.

클레임 `email`을 수신하고 `Redmond`의 할당된 값과 함께 클레임 `city`를 반환하는 Azure Function을 설치했습니다. 샘플 Azure Function은 [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples)에 있습니다.

이 컨텍스트에서 Azure Function에서 반환한 `userMessage` 클레임은 선택 사항이며 IEF에 의해 무시됩니다.  잠재적으로 응용 프로그램에 전달된 메시지로 사용되어 나중에 사용자에게 표시될 수 있습니다.

```
if (requestContentAsJObject.email == null)
    {
        return request.CreateResponse(HttpStatusCode.BadRequest);
    }

    var email = ((string) requestContentAsJObject.email).ToLower();


     return request.CreateResponse<ResponseContent>(
            HttpStatusCode.OK,
            new ResponseContent
            {
                version = "1.0.0",
                status = (int) HttpStatusCode.OK,
                userMessage = "User Found",
                city = "Redmond"
            },
            new JsonMediaTypeFormatter(),
            "application/json");
```

**Azure Function Apps**를 통해 특정 함수에 대한 식별자를 포함하여 함수 URL을 쉽게 가져올 수 있습니다.  이 경우 URL은 https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==이며 테스트 용도로 사용할 수 있습니다.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>2단계 - TrustFrameworkExtensions.xml 파일에서 기술적 프로필로 RESTful API 클레임 교환 구성

기술적 프로필은 RESTful 서비스에서 원하는 교환의 전체 구성입니다. `TrustFrameworkExtensions.xml` 파일을 열고 아래 XML 코드 조각을 `<ClaimsProvider>` 요소 내에 추가합니다.

> [!NOTE]
> 아래 설명된 “Restful Provider, Version 1.0.0.0”을 외부 서비스와 상호 작용할 함수(프로토콜)로 간주합니다.  스키마에 대한 전체 정의를 확인할 수 있습니다. <!-- TODO: Link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">     
                <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <Metadata>
                    <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                    <Item Key="AuthenticationType">None</Item>
                    <Item Key="SendClaimsIn">Body</Item>
                </Metadata>
                <InputClaims>
                    <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
                </InputClaims>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
                </OutputClaims>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
```

`<InputClaims>` 요소는 IEF에서 REST 서비스로 전송할 클레임을 정의합니다. 위의 예제에서는 클레임 `givenName`의 내용이 REST 서비스로 클레임 `email`로 전송됩니다.  

`<OutputClaims>` 요소는 IEF가 REST 서비스에서 예상하는 클레임을 정의합니다. 수신한 클레임 수에 관계없이 IEF는 여기에서 식별된 클레임만 사용합니다. 이 예제에서는 `city`로 수신한 클레임이 IEF 클레임 `city`로 매핑됩니다.

## <a name="step-3---add-a-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>3단계 - TrustFrameworkExtensions.xml 파일의 스키마에 새 클레임 `city` 추가

클레임 `city`는 스키마의 어느 곳에서나 달리 정의되지 않습니다. 따라서 `TrustFrameworkExtensions.xml` 파일의 시작 부분에서 찾을 수 있는 요소 `<BuildingBlocks>` 내부에 정의를 추가합니다.

```XML
<BuildingBlocks>
<!--The claimtype city must be added to the TrustFrameworkPolicy-->
<!-- You can add new claims in the BASE file Section III, or in the extensions file-->
<ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

## <a name="step-4---include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-your-trustframeworkextensionsxml"></a>4단계 - TrustFrameworkExtensions.xml에서 프로필 편집 사용자 경험의 오케스트레이션 단계로 REST 서비스 클레임 교환을 포함시킵니다.

사용자가 인증하고(오케스트레이션 1-4단계 - 아래 참조) 업데이트된 프로필 정보(5단계)를 제공한 후에 해당 단계인 프로필 편집 사용자 경험을 추가하기로 결정했습니다.

> [!NOTE]
> REST API 호출을 오케스트레이션 단계로 사용할 수 있는 많은 사용 사례가 있습니다.  오케스트레이션 단계로, 사용자가 처음 등록이나 프로필 업데이트와 같은 작업을 성공적으로 완료하면 정보를 동기화된 상태로 유지하기 위해 외부 시스템에 대한 업데이트로 호출을 사용할 수 있습니다.  이 경우 프로필 편집 후 응용 프로그램에 제공된 정보를 보강하는 데 사용됩니다.

프로필 편집 사용자 경험 XML 코드를 `TrustFrameworkBase.xml` 파일에서 `<UserJourneys>` 요소 내의 `TrustFrameworkExtensions.xml` 파일로 복사한 후 6단계에 따라 수정합니다.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
          <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
```

> [!IMPORTANT]
> 순서가 사용자의 버전과 일치하지 않으면 ClaimsExchange 형식 `SendClaims` 전에 단계로 삽입해야 합니다.

최종 UserJourney XML은 다음과 같습니다.

```XML
<UserJourney Id="ProfileEdit">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>localAccountAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
          </ClaimsExchanges>
           </OrchestrationStep>
           <!-- Add a step 6 to the user journey before the jwt token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
            </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
```

## <a name="step-5---add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>5단계 - 클레임 “city”를 신뢰 당사자 정책 파일에 추가하여 클레임이 응용 프로그램에 전송되도록 함

이렇게 하려면 `ProfileEdit.xml` RP 파일을 편집하고 `<TechnicalProfile Id="PolicyProfile">` 요소를 수정하여 `<OutputClaim ClaimTypeReferenceId="city" />`를 추가합니다.

새 클레임을 추가한 후에는 TechnicalProfile은 다음과 같습니다.

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6---upload-your-changes-and-test"></a>6단계 - 변경 내용 업로드 및 테스트

정책의 기존 버전을 덮어쓰게 됩니다.

1.    (선택 사항) 계속하기 전에 확장 파일의 기존 버전을 저장(다운로드)했습니다.  초기 복잡성을 낮게 유지하기 위해서는 확장 파일의 여러 버전을 업로드하지 않는 것이 좋습니다.
2.    (선택 사항) PolicyId="B2C_1A_TrustFrameworkProfileEdit"를 변경하여 정책 편집 파일 PolicyId의 새 버전에 이름을 변경할 수 있습니다.
3.    확장 파일을 업로드합니다.
4.    정책 편집 신뢰 당사자 파일 업로드
5.    **지금 실행**을 사용하여 정책을 테스트합니다.  IEF에서 응용 프로그램에 다시 반환한 토큰을 검토합니다.

모든 항목이 올바르게 설정되면 토큰에는 `Redmond` 값의 새 클레임 `city`가 포함됩니다.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>다음 단계

[유효성 검증 단계로 REST API 사용](active-directory-b2c-rest-api-validation-custom.md)

[프로필 편집을 수정하여 사용자로부터 추가 정보를 수집하는 방법](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)


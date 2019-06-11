---
title: REST API 클레임 교환을-Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C에서 사용자 지정 정책에 REST API 클레임 교환을 추가 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc0cea765816bfac066b05aca65f668fbce0c8ef
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508761"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책에 REST API 클레임 교환을 추가합니다

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

상호 작용 하는 RESTful API를 사용 하 여 추가할 수 있습니다 하 [사용자 지정 정책을](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C에서. 이 문서에서는 RESTful 서비스와 상호 작용 하는 Azure AD B2C 사용자 경험을 만드는 방법을 보여 줍니다.

REST API 클레임 및 Azure AD B2C 간에 정보를 클레임 교환을 포함 하는 상호 작용 합니다. 클레임 교환에는 다음과 같은 특징이 있습니다.

- 오케스트레이션 단계로 설계할 수 있습니다.
- 외부 동작을 트리거할 수 있습니다. 예를 들어 외부 데이터베이스에 이벤트를 기록할 수 있습니다.
- 값을 가져와서 사용자 데이터베이스에 저장하는 데 사용할 수 있습니다.
- 실행 흐름을 변경할 수 있습니다. 

이 문서에 표시 되는 시나리오에는 다음 작업을 포함 합니다.

1. 외부 시스템에서 사용자를 찾습니다.
2. 해당 사용자를 등록한 도시를 가져옵니다.
3. 해당 특성을 클레임으로 애플리케이션에 반환합니다.

## <a name="prerequisites"></a>필수 조건

- [사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다.
- 상호 작용할 REST API 엔드포인트 예를 들어 간단한 Azure 함수를 사용 하는 문서입니다. Azure 함수를 만들려면 참조 [Azure portal에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function.md)합니다.

## <a name="prepare-the-api"></a>API를 준비 합니다.

이 섹션에서는 Azure function에 대 한 값을 받도록 준비한 `email`에 대 한 값을 반환 하 고 `city` 사용할 수 있는 Azure AD B2C에 의해 클레임으로 합니다.

다음 코드를 사용 하기 위해 만든 Azure 함수에 대 한 run.csx 파일을 변경 합니다. 

```
public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>클레임 교환 구성

기술 프로필 클레임 교환에 대 한 구성을 제공합니다. 

열기는 *TrustFrameworkExtensions.xml* 파일과 안에 다음 XML 요소를 추가 합니다 **ClaimsProvider** 요소입니다.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
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

합니다 **InputClaims** 요소 REST 서비스에 전송 되는 클레임을 정의 합니다. 이 예제에서는 클레임의 값 `givenName` REST 서비스로 클레임으로 보낼 `email`합니다. 합니다 **OutputClaims** 요소 REST 서비스에서 예상 되는 클레임을 정의 합니다.

## <a name="add-the-claim-definition"></a>클레임 정의 추가 합니다.

에 대 한 정의 추가 `city` 내부 합니다 **BuildingBlocks** 요소입니다. 이 요소는 TrustFrameworkExtensions.xml 파일의 시작 부분에서 찾을 수 있습니다.

```XML
<BuildingBlocks>
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

## <a name="add-an-orchestration-step"></a>오케스트레이션 단계를 추가 합니다.

REST API 호출을 오케스트레이션 단계로 사용할 수 있는 사용 사례가 많이 있습니다. 오케스트레이션 단계로서 사용자가 첫 번째 등록과 같은 태스크를 성공적으로 완료한 후 외부 시스템에 대한 업데이트로 사용하거나 동기화된 정보 상태로 유지하기 위한 프로필 업데이트로 사용할 수 있습니다. 이 경우 프로필 편집 후에 애플리케이션에 제공된 정보를 보강하는 데 사용됩니다.

프로필 편집 사용자 경험에 단계를 추가 합니다. 사용자 (오케스트레이션 1-4 단계는 다음과 같은 xml에서)를 인증 하 고는 사용자가 업데이트 된 프로필 정보 (5 단계)을 제공 합니다. 복사 프로필 편집 사용자 경험 XML 코드에서를 *TrustFrameworkBase.xml* 파일을 프로그램 *TrustFrameworkExtensions.xml* 파일을 **UserJourneys** 요소입니다. 6 단계로 수정을 확인 합니다.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

사용자 경험의 최종 XML은이 예제와 같습니다.

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
    <!-- Add a step 6 to the user journey before the JWT token is created-->
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

## <a name="add-the-claim"></a>클레임 추가

편집 합니다 *ProfileEdit.xml* 파일을 추가 `<OutputClaim ClaimTypeReferenceId="city" />` 에 **OutputClaims** 요소입니다.

새 클레임을 추가한 후에 기술 프로필은이 예제와 비슷합니다.

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

## <a name="upload-your-changes-and-test"></a>변경 내용 업로드 및 테스트

1. (선택 사항) 진행 하기 전에 기존 버전을 다운로드 하 여 파일 저장 합니다.
2. 업로드 합니다 *TrustFrameworkExtensions.xml* 하 고 *ProfileEdit.xml* 및 기존 파일을 덮어쓰려면 선택 합니다.
3. 선택 **B2C_1A_ProfileEdit**합니다.
4. 에 대 한 **응용 프로그램 선택** 사용자 지정 정책의 개요 페이지에서 명명 된 웹 응용 프로그램을 선택 합니다 *webapp1* 이전에 등록 합니다. 있는지 확인 합니다 **회신 URL** 는 `https://jwt.ms`합니다.
4. 선택 **지금 실행**합니다. 계정 자격 증명을 사용 하 여 로그인 하 고 클릭 **계속**합니다.

토큰을 새 클레임에 포함 된 모든 항목이 올바르게 설정 되었는지, 하는 경우 `city`에 값을 사용 하 여 `Redmond`입니다.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>다음 단계

- 또한 상호 작용은 유효성 검사 프로필로 설계할 수 있습니다. 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합](active-directory-b2c-rest-api-validation-custom.md)을 참조하세요.
- [프로필 편집을 수정하여 사용자로부터 추가 정보 수집](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

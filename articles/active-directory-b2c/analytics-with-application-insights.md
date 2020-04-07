---
title: 애플리케이션 인사이트로 사용자 행동 추적
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용하여 Azure AD B2C 사용자 여정에서 응용 프로그램 인사이트에서 이벤트 로그를 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25e62e7c6865f91daa242a33a0f491f8015be41a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672528"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Application Insights를 사용하여 Azure Active Directory B2C에서 사용자 동작 추적

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C(Azure AD B2C)는 Azure AD B2C에 제공된 계측 키를 사용하여 [응용 프로그램 인사이트로](../azure-monitor/app/app-insights-overview.md) 직접 이벤트 데이터를 전송하는 것을 지원합니다.  Application Insights 기술 프로필을 사용하면 다음과 같은 사용자 지정 이벤트 로그를 얻을 수 있습니다.

* 사용자 동작에 대한 정보를 얻습니다.
* 개발에서 또는 프로덕션 환경에서 사용자 고유의 정책 문제를 해결합니다.
* 성능을 측정합니다.
* Application Insights에서 알림을 만듭니다.

## <a name="how-it-works"></a>작동 방법

[응용 프로그램 인사이트](application-insights-technical-profile.md) 기술 프로필은 Azure AD B2C의 이벤트를 정의합니다. 이 프로필은 이벤트의 이름, 기록될 클레임 및 계측 키를 지정합니다. 이벤트를 게시하려면 기술 프로필이 [사용자 여정의](userjourneys.md)오케스트레이션 단계로 추가됩니다.

Application Insights는 상관 관계 ID를 사용하여 사용자 세션을 기록하는 이벤트를 통합할 수 있습니다. Application Insights는 짧은 시간 안에 이벤트 및 세션을 사용할 수 있게 하며, 많은 시각화, 내보내기 및 분석 도구를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

Application Insights를 Azure AD B2C와 함께 사용하는 경우 리소스를 만들고 계측 키를 가져오기만 하면 됩니다. 자세한 내용은 [응용 프로그램 인사이트 만들기 리소스를](../azure-monitor/app/create-new-resource.md) 참조하십시오.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 구독이 포함된 디렉터리를 선택하여 Azure 구독이 포함된 디렉터리를 사용하고 있는지 확인합니다. 이 테넌트는 Azure AD B2C 테넌트가 아닙니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **리소스 만들기**를 선택하고 **Application Insights**를 검색하여 선택합니다.
4. **만들기**를 클릭합니다.
5. 리소스의 **이름**을 입력합니다.
6. **애플리케이션 유형**에서 **ASP.NET 웹 애플리케이션**을 선택합니다.
7. **리소스 그룹**: 기존 그룹을 선택하거나 새 리소스 그룹의 이름을 입력합니다.
8. **만들기**를 클릭합니다.
4. Application Insights 리소스를 만든 후 해당 리소스를 열고 **기본 정보**를 확장하고 계측 키를 복사합니다.

![Application Insights 개요 및 계측 키](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공합니다. [클레임 스키마는](claimsschema.md) 클레임을 선언하는 위치입니다.

1. 정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [클레임스키마](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **클레임스키마** 요소에 다음 클레임을 추가합니다. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>새 기술 프로필 추가

기술 프로필은 Azure AD B2C의 ID 경험 프레임워크의 함수로 간주될 수 있습니다. 이 테이블은 세션을 열고 이벤트를 게시하는 데 사용되는 기술 프로필을 정의합니다.

| 기술 프로필 | Task |
| ----------------- | -----|
| 앱인사이트-공통 | 모든 Azure Insights 기술 프로필에 포함할 공통 매개 변수 집합입니다. |
| 앱인사이트-로그인 요청 | 로그인 `SignInRequest` 요청이 수신되면 클레임 집합이 있는 이벤트를 기록합니다. |
| 앱인사이트-사용자 서명 | 사용자가 `UserSignUp` 등록/로그인 여정에서 등록 옵션을 트리거할 때 이벤트를 기록합니다. |
| 앱인사이트-로그인완료 | 토큰이 `SignInComplete` 신뢰할 수 있는 파티 응용 프로그램으로 전송된 경우 인증이 성공적으로 완료될 때 이벤트를 기록합니다. |

프로필을 스타터 팩의 *TrustFrameworkExtensions.xml* 파일에 추가합니다. 이러한 요소를 **ClaimsProviders** 요소에 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> `AppInsights-Common` 기술 프로필의 계측 키를 Application Insights 리소스에서 제공하는 GUID로 변경합니다.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>오케스트레이션 단계로 기술 프로필 추가

`AppInsights-SignInRequest`를 오케스트레이션 2단계로 호출하여 로그인/등록 요청이 수신되었는지 추적합니다.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` 오케스트레이션 단계 바로 *전에*`AppInsights-UserSignup`을 호출하는 새 단계를 추가합니다. 사용자가 등록/로그인 경험에서 등록 단추를 선택할 때 트리거됩니다.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` 오케스트레이션 단계 바로 다음에 `AppInsights-SignInComplete`를 호출합니다. 이 단계는 성공적으로 완료된 경험을 나타냅니다.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 새 오케스트레이션 단계를 추가한 후, 1부터 N까지 건너뛰지 말고 순차적으로 단계 번호를 다시 매깁니다.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>파일을 업로드, 정책 실행 및 이벤트 보기

*TrustFrameworkExtensions.xml* 파일을 저장하고 업로드합니다. 그런 다음, 애플리케이션에서 신뢰 당사자 정책을 호출하거나 Azure Portal에서 **지금 실행**을 사용합니다. 몇 초 내에 사용자 이벤트를 Application Insights에서 사용할 수 있습니다.

1. Azure Active 디렉터리 테넌트에서 **응용 프로그램 인사이트** 리소스를 엽니다.
2. **사용** > **이벤트를**선택합니다.
3. **During**을 **지난 1시간**으로 설정하고 **By**를 **3분**으로 설정합니다.  결과를 보기 위해 **새로 고침**을 선택해야 할 수도 있습니다.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[선택 사항] 더 많은 데이터 수집

필요에 맞게 사용자 경험에 클레임 유형 및 이벤트를 추가합니다. [클레임 확인자](claim-resolver-overview.md) 또는 문자열 클레임 유형을 사용하고 응용 프로그램 인사이트 이벤트 또는 AppInsights-Common 기술 프로필에 **입력 클레임** 요소를 추가하여 클레임을 추가할 수 있습니다.

- **ClaimTypeReferenceId**는 클레임 유형에 대한 참조입니다.
- **PartnerClaimType**은 Azure Insights에 나타나는 속성의 이름입니다. `{property:NAME}`의 구분을 사용합니다. 단, `NAME`은 이벤트에 추가하는 속성입니다.
- **DefaultValue**는 임의의 문자열 값 또는 클레임 확인자를 사용합니다.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [애플리케이션 인사이트](application-insights-technical-profile.md) 기술 프로필에 대해 자세히 알아보십시오. 

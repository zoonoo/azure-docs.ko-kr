---
title: UserJourneys | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 UserJourneys 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05307fe2ad9e0a59fa11c30f2dc7154ba5076603
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174668"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 경험은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 이러한 경로를 통해 사용자를 가져와서 신뢰 당사자에게 표시할 클레임을 검색합니다. 즉, 사용자 경험은 Azure AD B2C ID 경험 프레임워크가 요청을 처리할 때 최종 사용자에게 적용되는 비즈니스 논리를 정의합니다.

이러한 사용자 경험는 관심 커뮤니티의 여러 신뢰 당사자의 핵심 요구 사항을 충족 하는 데 사용할 수 있는 템플릿으로 간주할 수 있습니다. 사용자 경험 정책에 대 한 신뢰 당사자의 정의를 용이 하 게 합니다. 정책 하나가 여러 사용자 경험을 정의할 수 있습니다. 각 사용자 경험은 오케스트레이션 단계 시퀀스입니다.

정책에서 지원하는 사용자 경험을 정의하기 위해 **UserJourneys** 요소가 정책 파일의 최상위 요소 아래에 추가됩니다.

**UserJourneys** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 전체 사용자 흐름에 필요한 모든 구문을 정의하는 사용자 경험입니다. |

**UserJourney** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 정책의 다른 요소에서 사용자 경험을 참조하는 데 사용할 수 있는 사용자 경험의 식별자입니다. [신뢰 당사자 정책](relyingparty.md)의 **DefaultUserJourney** 요소는 이 특성을 가리킵니다. |

**UserJourney** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | 권한 부여 기술 프로필의 목록입니다. | 
| OrchestrationSteps | 1:n | 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스입니다. 모든 사용자 경험은 순서대로 실행되는 정렬된 오케스트레이션 단계 목록으로 구성됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

사용자가 UserJourney를 완료 하 고 액세스 또는 ID 토큰을 획득 했다고 가정 합니다. 사용자 [정보 끝점과](userinfo-endpoint.md)같은 추가 리소스를 관리 하려면 사용자를 식별 해야 합니다. 이 프로세스를 시작 하려면 사용자는 이전에 발급 된 액세스 토큰을 유효한 Azure AD B2C 정책에 의해 원래 인증 되었다는 증명으로 제공 해야 합니다. 사용자가이 요청을 수행할 수 있도록 하려면이 프로세스 중에 사용자에 대 한 유효한 토큰이 항상 제공 되어야 합니다. 권한 부여 기술 프로필은 들어오는 토큰의 유효성을 검사 하 고 토큰에서 클레임을 추출 합니다.

**AuthorizationTechnicalProfiles** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | 권한 부여 기술 프로필의 목록입니다. | 

**AuthorizationTechnicalProfile** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | 예 | 실행할 기술 프로필의 식별자입니다. |

다음 예제에서는 권한 부여 기술 프로필을 사용 하는 사용자 경험 요소를 보여 줍니다.

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. 이러한 오케스트레이션 단계는 정책 파일에서 허용되는 클레임 공급자와 구성 요소를 모두 참조합니다. 사용자 경험을 표시하거나 렌더링해야 하는 모든 오케스트레이션 단계에는 해당하는 콘텐츠 정의 식별자에 대한 참조도 포함됩니다.

오케스트레이션 단계 요소에 정의 된 전제 조건에 따라 오케스트레이션 단계를 조건부로 실행할 수 있습니다. 예를 들어 특정 클레임이 존재 하거나 클레임이 지정 된 값과 같지 않은 경우에만 오케스트레이션 단계를 수행 하도록 선택할 수 있습니다.

오케스트레이션 단계의 정렬된 목록을 지정하기 위해 **OrchestrationSteps** 요소가 정책의 일부로 추가됩니다. 이 요소는 필수 요소입니다.

**OrchestrationSteps** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 정렬된 오케스트레이션 단계입니다. |

**OrchestrationStep** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Order` | 예 | 오케스트레이션 단계의 순서입니다. |
| `Type` | 예 | 오케스트레이션 단계의 유형입니다. 가능한 값은 다음과 같습니다. <ul><li>**ClaimsProviderSelection** - 오케스트레이션 단계에서 사용자가 하나를 선택할 수 있도록 여러 클레임 공급자가 표시됨을 나타냅니다.</li><li>**CombinedSignInAndSignUp** - 오케스트레이션 단계에서 소셜 공급자 로그인 및 로컬 계정 등록 페이지가 결합된 형태로 표시됨을 나타냅니다.</li><li>**ClaimsExchange** - 오케스트레이션 단계에서 클레임 공급자와 클레임을 교환함을 나타냅니다.</li><li>**Getclaims** -오케스트레이션 단계가 구성을 통해 신뢰 당사자 로부터 Azure AD B2C으로 전송 된 클레임 데이터를 처리 하도록 지정 합니다 `InputClaims` .</li><li>**InvokeSubJourney** -오케스트레이션 단계가 공개 미리 보기로 [클레임을 교환](subjourneys.md) 하는 것을 나타냅니다.</li><li>**SendClaims** - 오케스트레이션 단계에서 클레임 발급자가 발급한 토큰과 함께 클레임을 신뢰 당사자에게 전송함을 나타냅니다.</li></ul> |
| ContentDefinitionReferenceId | 예 | 이 오케스트레이션 단계와 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. 일반적으로 콘텐츠 정의 참조 식별자는 자체 어설션된 기술 프로필에서 정의됩니다. 그러나 Azure AD B2C가 기술 프로필을 사용하지 않고 콘텐츠를 표시해야 하는 경우도 있습니다. 두 가지 예가 있습니다. 오케스트레이션 단계의 형식이 다음 중 하나 이면이 고 `ClaimsProviderSelection` , 그렇지 않으면  `CombinedSignInAndSignUp` 기술 프로필을 사용 하지 않고 id 공급자를 선택 해야 Azure AD B2C. |
| CpimIssuerTechnicalProfileReferenceId | 예 | 오케스트레이션 단계의 유형이 `SendClaims`입니다. 이 속성은 신뢰 당사자용 토큰을 발급하는 클레임 공급자의 기술 프로필 식별자를 정의합니다.  해당 식별자가 없으면 신뢰 당사자 토큰이 생성되지 않습니다. |

**OrchestrationStep** 요소에는 다음과 같은 요소가 포함될 수 있습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | 오케스트레이션 단계를 실행하려면 충족해야 하는 전제 조건 목록입니다. |
| ClaimsProviderSelections | 0:n | 오케스트레이션 단계의 클레임 공급자 선택 항목 목록입니다. |
| ClaimsExchanges | 0:n | 오케스트레이션 단계의 클레임 교환 목록입니다. |
| JourneyList | 0:1 | 오케스트레이션 단계에 대 한 하위 여행 후보 목록입니다. |

### <a name="preconditions"></a>Preconditions

**Preconditions** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 사용 중인 기술 프로필별로 클레임 공급자 선택 항목에 따라 클라이언트를 리디렉션하거나 교환 클레임에 대한 서버 호출을 수행합니다. |


#### <a name="precondition"></a>Precondition

오케스트레이션 단계에 정의 된 전제 조건에 따라 오케스트레이션 단계를 조건부로 실행할 수 있습니다. 사전 조건에는 다음과 같은 두 가지 유형이 있습니다.
 
- **클레임이 존재** 함-지정 된 클레임이 사용자의 현재 클레임 모음에 있는 경우 작업을 수행 하도록 지정 합니다.
- **클레임 같음** -지정 된 클레임이 존재 하 고 해당 값이 지정 된 값과 같은 경우 작업을 수행 하도록 지정 합니다. 이 검사는 대/소문자를 구분 하는 서 수 비교를 수행 합니다. 부울 클레임 유형을 확인 하는 경우 `True` , 또는를 사용 `False` 합니다.

**사전 조건** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | 예 | 이 전제 조건에 대해 수행할 확인이나 쿼리의 유형입니다. 값은 **ClaimsExist**(지정한 클레임이 사용자의 현재 클레임 집합에 있으면 작업을 수행해야 함을 지정함) 또는 **ClaimEquals**(지정한 클레임이 있으며 해당 값이 지정된 값과 같으면 작업을 수행해야 함을 지정함)일 수 있습니다. |
| `ExecuteActionsIf` | 예 | `true`또는 `false` 테스트를 사용 하 여 사전 조건에서 작업을 수행할지 여부를 결정 합니다. |

**Precondition** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 값 | 1:2 | 클레임 유형의 식별자입니다. 클레임은 정책 파일 또는 부모 정책 파일의 클레임 스키마 섹션에 이미 정의 되어 있습니다. 전제 조건이의 형식인 경우 `ClaimEquals` 두 번째 요소는 `Value` 확인할 값을 포함 합니다. |
| 작업 | 1:1 | 오케스트레이션 단계 내에서 수행한 전제 조건 확인의 결과가 true이면 수행해야 하는 작업입니다. `Action`의 값이 `SkipThisOrchestrationStep`으로 설정된 경우 연결된 `OrchestrationStep`이 실행되면 안 됩니다. |

#### <a name="preconditions-examples"></a>Preconditions 예제

다음 Preconditions는 사용자의 objectId가 있는지를 확인합니다. 사용자 경험에서 사용자는 로컬 계정을 사용하여 로그인하도록 선택했습니다. objectId가 있으면 이 오케스트레이션 단계를 건너뜁니다.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 Preconditions는 사용자가 소셜 계정을 사용하여 로그인했는지를 확인합니다. 이를 위해 디렉터리에서 사용자 계정 찾기를 시도합니다. 사용자가 로그인 하거나 로컬 계정으로 등록 하는 경우이 오케스트레이션 단계를 건너뜁니다.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions는 여러 전제 조건을 확인할 수 있습니다. 다음 예제에서는 'objectId' 또는 'email'이 있는지 여부를 확인합니다. 첫 번째 조건이 true 이면 여행에서 다음 오케스트레이션 단계로 건너뜁니다.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>클레임 공급자 선택

Id 공급자 선택을 통해 사용자는 옵션 목록에서 작업을 선택할 수 있습니다. Id 공급자 선택은 다음 두 오케스트레이션 단계로 구성 됩니다. 

1. **단추** -형식으로 시작 `ClaimsProviderSelection` 하거나 `CombinedSignInAndSignUp` 사용자가 선택할 수 있는 옵션 목록이 포함 되어 있습니다. 요소 내의 옵션 순서는 `ClaimsProviderSelections` 사용자에 게 표시 되는 단추의 순서를 제어 합니다.
2. **작업** -뒤에 형식이 나옵니다 `ClaimsExchange` . Claim이상 변경에는 작업 목록이 포함 됩니다. 작업은 [OAuth2](oauth2-technical-profile.md), [openid connect Connect](openid-connect-technical-profile.md), [클레임 변환](claims-transformation-technical-profile.md)또는 [자체 어설션된](self-asserted-technical-profile.md)과 같은 기술 프로필에 대 한 참조입니다. 사용자가 단추 중 하나를 클릭 하면 해당 작업이 실행 됩니다.

**ClaimsProviderSelections** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 선택할 수 있는 클레임 공급자의 목록을 제공합니다.|

**ClaimsProviderSelections** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| DisplayOption| 예 | 단일 클레임 공급자를 선택할 수 있는 사례의 동작을 제어 합니다. 가능한 값: `DoNotShowSingleProvider` (기본값) 사용자가 페더레이션 id 공급자로 즉시 리디렉션됩니다. 또는 `ShowSingleProvider` Azure AD B2C 단일 id 공급자를 선택 하 여 로그인 페이지를 표시 합니다. 이 특성을 사용 하려면 [콘텐츠 정의 버전이](page-layout.md) 이상 이어야 합니다 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` .|

**ClaimsProviderSelection** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 예 | 클레임 공급자 선택 항목의 다음 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 ValidationClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |
| ValidationClaimsExchangeId | 예 | 클레임 공급자 선택 항목의 유효성을 검사하기 위해 현재 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 TargetClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |

### <a name="claims-provider-selection-example"></a>클레임 공급자 선택 예제

다음 오케스트레이션 단계에서 사용자는 Facebook, LinkedIn, Twitter, Google 또는 로컬 계정을 사용 하 여 로그인 하도록 선택할 수 있습니다. 사용자가 소셜 ID 공급자 중 하나를 선택하면 `TargetClaimsExchangeId` 특성에 지정된 선택한 클레임 교환을 사용하여 두 번째 오케스트레이션 단계가 실행됩니다. 두 번째 오케스트레이션 단계에서는 로그인 프로세스를 완료할 수 있도록 사용자를 소셜 ID 공급자로 리디렉션합니다. 사용자가 로컬 계정을 사용하여 로그인하도록 선택하는 경우 Azure AD B2C는 같은 오케스트레이션 단계(같은 등록 또는 로그인 페이지)로 유지되며 두 번째 오케스트레이션 단계는 건너뜁니다.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 사용 중인 기술 프로필별로 선택한 ClaimsProviderSelection에 따라 클라이언트를 리디렉션하거나 교환 클레임에 대한 서버 호출을 수행합니다. |

**ClaimsExchange** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 클레임 교환 단계의 식별자입니다. 식별자는 정책의 클레임 공급자 선택 단계에서 클레임 교환을 참조하는 데 사용됩니다. |
| TechnicalProfileReferenceId | 예 | 실행할 기술 프로필의 식별자입니다. |

## <a name="journeylist"></a>JourneyList

**JourneyList** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Candidate(후보) | 1:1 | 호출 될 sub 여행에 대 한 참조입니다. |

### <a name="candidate"></a>Candidate(후보)

**후보** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | 예 | 실행할 [하위](subjourneys.md) 경험의 식별자입니다. |

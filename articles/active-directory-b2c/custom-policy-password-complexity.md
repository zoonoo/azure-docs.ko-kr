---
title: 사용자 지정 정책을 사용하여 암호 복잡성 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 요구 사항을 구성하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138437"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)에서 계정을 만들 때 사용자가 제공하는 암호에 대한 복잡성 요구 사항을 구성할 수 있습니다. 기본적으로 Azure AD B2C는 **강력한** 암호를 사용합니다. 이 문서에서는 [사용자 지정 정책](custom-policy-overview.md)에서 암호 복잡성을 구성하는 방법을 보여 줍니다. [사용자 흐름](user-flow-password-complexity.md)에서 암호 복잡성을 구성할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.


## <a name="add-the-elements"></a>요소 추가

암호 복잡성을 구성하려면 조건자 `newPassword` `reenterPassword` 유효성 검사에 대한 참조를 사용하여 및 [클레임 형식을](claimsschema.md) [재정의합니다.](predicates.md#predicatevalidations) 조건자 유효성 검사 요소는 조건자 집합을 그룹화하여 클레임 유형에 적용할 수 있는 사용자 입력 유효성 검사를 형성합니다. 정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [클레임스키마](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. `newPassword` `reenterPassword` 및 클레임스키마 요소를 **추가합니다.**

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [조건자는](predicates.md) 클레임 유형의 값을 확인하는 기본 유효성 검사를 정의하고 true 또는 false를 반환합니다. 유효성 검사는 지정된 메서드 요소와 메서드와 관련된 매개 변수 집합을 사용하여 수행됩니다. 요소를 닫은 직후 빌딩 **블록** 요소에 다음 술어를 `</ClaimsSchema>` 추가합니다.

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. 요소를 닫은 직후 **빌딩 블록** 요소에 다음 조건자 유효성 `</Predicates>` 검사를 추가합니다.

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. 다음 기술 프로필은 [Azure Active Directory에](active-directory-technical-profile.md)데이터를 읽고 쓰는 Active Directory 기술 프로필입니다. 확장자 파일에서 이러한 기술 프로파일을 재정의합니다. 강력한 `PersistedClaims` 암호 정책을 사용하지 않도록 설정하는 데 사용합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 다음 클레임 공급자를 추가합니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 정책 파일을 저장합니다.

## <a name="test-your-policy"></a>정책 테스트

### <a name="upload-the-files"></a>파일 업로드

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드**를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기를**선택한 다음 *TrustFrameworkExtensions.xml* 파일을 검색하고 선택합니다.
7. **업로드**를 클릭합니다.

### <a name="run-the-policy"></a>정책 실행

1. 가입 또는 로그인 정책을 엽니다. 예를 들어 B2C_1A_signup_signin입니다**.
2. **애플리케이션**은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL**에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행**을 클릭합니다.
4. **지금 등록**을 선택하고, 메일 주소를 입력한 다음, 새 암호를 입력합니다. 지침은 암호 제한 사항에서 제공됩니다. 사용자 정보 입력을 완료한 후 **만들기**를 클릭합니다. 반환된 토큰의 콘텐츠가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경을 구성](custom-policy-password-change.md)하는 방법을 알아봅니다.
- IEF 참조에서 [조건어](predicates.md) 및 [조건어유효성](predicates.md#predicatevalidations) 검사 요소에 대해 자세히 알아봅니다.

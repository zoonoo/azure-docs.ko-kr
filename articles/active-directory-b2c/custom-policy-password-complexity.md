---
title: 사용자 지정 정책을 사용 하 여 암호 복잡성 구성
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
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138437"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)에서 계정을 만들 때 사용자가 제공 하는 암호의 복잡성 요구 사항을 구성할 수 있습니다. 기본적으로 Azure AD B2C는 **강력한** 암호를 사용합니다. 이 문서에서는 [사용자 지정 정책](custom-policy-overview.md)에서 암호 복잡성을 구성하는 방법을 보여 줍니다. [사용자 흐름](user-flow-password-complexity.md)에서 암호 복잡성을 구성할 수도 있습니다.

## <a name="prerequisites"></a>전제 조건

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.


## <a name="add-the-elements"></a>요소 추가

암호 복잡성을 구성 하려면 `newPassword`를 재정의 하 고, [조건자 유효성 검사](predicates.md#predicatevalidations)에 대 한 참조를 사용 하 여 [클레임 형식을](claimsschema.md) `reenterPassword` 합니다. PredicateValidations 요소는 조건부 집합을 그룹화 하 여 클레임 유형에 적용할 수 있는 사용자 입력 유효성 검사를 구성 합니다. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> 합니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 `newPassword` 및 `reenterPassword` 클레임을 추가 합니다.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [조건자](predicates.md) 는 클레임 형식의 값을 확인 하 고 true 또는 false를 반환 하는 기본 유효성 검사를 정의 합니다. 유효성 검사는 지정 된 메서드 요소 및 메서드와 관련 된 매개 변수 집합을 사용 하 여 수행 됩니다. `</ClaimsSchema>` 요소를 닫은 직후 **BuildingBlocks** 요소에 다음 조건자를 추가 합니다.

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

1. `</Predicates>` 요소를 닫은 직후 **BuildingBlocks** 요소에 다음 조건자 유효성 검사를 추가 합니다.

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

1. 다음 기술 프로필은 Active Directory Azure Active Directory 데이터를 읽고 쓰는 [기술 프로필](active-directory-technical-profile.md)입니다. 확장 파일에서 이러한 기술 프로필을 재정의 합니다. `PersistedClaims`를 사용 하 여 강력한 암호 정책을 사용 하지 않도록 설정 합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 다음과 같은 클레임 공급자를 추가 합니다.

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

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드**를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기**를 선택 하 고 *trustframeworkextensions.xml* 파일을 검색 한 다음 선택 합니다.
7. **업로드**를 클릭합니다.

### <a name="run-the-policy"></a>정책 실행

1. 등록 또는 로그인 정책을 엽니다. 예를 들어 B2C_1A_signup_signin입니다.
2. **애플리케이션**은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL**에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행**을 클릭합니다.
4. **지금 등록**을 선택하고, 메일 주소를 입력한 다음, 새 암호를 입력합니다. 지침은 암호 제한 사항에서 제공됩니다. 사용자 정보 입력을 완료한 후 **만들기**를 클릭합니다. 반환된 토큰의 콘텐츠가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경을 구성](custom-policy-password-change.md)하는 방법을 알아봅니다.
- IEF 참조의 [조건자](predicates.md) 및 [PredicateValidations](predicates.md#predicatevalidations) 요소에 대해 자세히 알아보세요.

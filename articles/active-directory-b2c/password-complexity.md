---
title: 암호 복잡성 요구 사항 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 소비자가 제공한 암호에 복잡성 요구 사항을 구성하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f9748d0d278375029fc9875f5b36674d19ad871a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98058976"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호에 복잡성 요구 사항 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)는 계정을 만들 때 최종 사용자가 제공하는 암호에 복잡성 요구 사항을 변경하도록 지원합니다. 기본적으로 Azure AD B2C는 **강력한** 암호를 사용합니다. 또한 Azure AD B2C는 고객이 사용할 수는 암호의 복잡성을 제어하는 구성 옵션을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>암호 규칙 적용

등록 또는 암호 재설정 중에 최종 사용자는 복잡성 규칙을 충족하는 암호를 제공해야 합니다. 암호 복잡성 규칙은 사용자 흐름별로 적용됩니다. 등록 하는 동안 한 사용자 흐름에 4 자리 pin이 필요 하 고, 다른 사용자 흐름에는 등록 하는 동안 8 개의 문자열이 필요 합니다. 예를 들어 자식에 대한 경우보다 성인에 다양한 암호 복잡성을 포함한 사용자 흐름을 사용할 수 있습니다.

암호의 복잡성은 로그인 중에 적용되지 않습니다. 사용자는 로그인 중에 해당 암호를 변경하라는 메시지를 수신하지 않습니다. 현재 복잡성 요구 사항을 충족하지 않기 때문입니다.

암호 복잡성은 다음과 같은 사용자 흐름 형식에서 구성할 수 있습니다.

- 가입 또는 로그인 사용자 흐름
- 암호 재설정 사용자 흐름

사용자 지정 정책을 사용하는 경우 [사용자 지정 정책에서 암호 복잡성을 구성](password-complexity.md)할 수 있습니다.

## <a name="configure-password-complexity"></a>암호 복잡도 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. **사용자 흐름** 을 선택합니다.
2. 사용자 흐름을 선택하고, **속성** 을 클릭합니다.
3. **암호 복잡성** 에서 이 사용자 흐름에 대한 암호 복잡성을 **단순**, **강력** 또는 **사용자 지정** 으로 변경합니다.

### <a name="comparison-chart"></a>비교 차트

| 복잡성 | 설명 |
| --- | --- |
| 단순 | 암호는 적어도 8~64자입니다. |
| 강력 | 암호는 적어도 8~64자입니다. 소문자, 대문자, 숫자 또는 기호와 같은 4개 항목 중 3가지가 필요합니다. |
| 사용자 지정 | 이 옵션을 통해 암호 복잡성 규칙을 대부분 제어할 수 있습니다.  사용자 지정 길이를 구성할 수 있습니다.  숫자 전용 암호(PIN)를 허용할 수 있습니다. |

## <a name="custom-options"></a>사용자 지정 옵션

### <a name="character-set"></a>문자 집합

숫자 전용(PIN) 또는 전체 문자 집합을 허용할 수 있습니다.

- **번호만 해당** 을 사용하면 암호를 입력하는 동안 숫자(0~9)만을 허용합니다.
- **모두** 를 사용하면 모든 문자, 숫자 또는 기호를 허용합니다.

### <a name="length"></a>길이

암호의 길이 요구 사항을 제어할 수 있습니다.

- **최소 길이** 는 적어도 4여야 합니다.
- **최대 길이** 는 최소 길이 보다 크거나 같고 최대 256 자까지 가능 합니다.

### <a name="character-classes"></a>문자 클래스

암호에 사용되는 다양한 문자 형식을 제어할 수 있습니다.

- **소문자, 대문자, 숫자 (0-9), 기호 등 4개 항목 중 2가지** 를 통해 암호에 두 개 이상의 문자 형식을 포함하도록 합니다. 예를 들어, 숫자 및 소문자입니다.
- **3 of 4: 소문자, 대문자, 숫자 (0-9), 기호** 는 암호에 세 개 이상의 문자 형식이 포함 되도록 합니다. 예를 들어, 숫자, 소문자 및 대문자입니다.
- **소문자, 대문자, 숫자 (0-9), 기호 등 4개 항목 중 4가지** 를 통해 암호에 전체 문자 형식을 포함하도록 합니다.

    > [!NOTE]
    > **4개 항목 중 4가지** 로 인해 최종 사용자 불만이 발생할 수 있습니다. 일부 연구에서는 이 요구 사항이 암호 엔트로피를 개선하지 않는다고 합니다. [NIST 암호 지침](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)을 참조하세요.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>암호 조건자 유효성 검사

암호 복잡성을 구성 하려면 `newPassword` `reenterPassword` [조건자 유효성 검사](predicates.md#predicatevalidations)에 대 한 참조를 사용 하 여 및 [클레임 형식을](claimsschema.md) 재정의 합니다. PredicateValidations 요소는 조건부 집합을 그룹화 하 여 클레임 유형에 적용할 수 있는 사용자 입력 유효성 검사를 구성 합니다. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. `newPassword`및 `reenterPassword` 클레임을 **ClaimsSchema** 요소에 추가 합니다.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [조건자](predicates.md) 는 클레임 형식의 값을 확인 하 고 true 또는 false를 반환 하는 기본 유효성 검사를 정의 합니다. 유효성 검사는 지정 된 메서드 요소 및 메서드와 관련 된 매개 변수 집합을 사용 하 여 수행 됩니다. 요소를 닫은 직후 **BuildingBlocks** 요소에 다음 조건자를 추가 합니다 `</ClaimsSchema>` .

    ```xml
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

1. 요소를 닫은 직후 **BuildingBlocks** 요소에 다음 조건자 유효성 검사를 추가 합니다 `</Predicates>` .

    ```xml
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

## <a name="disable-strong-password"></a>강력한 암호 사용 안 함 

다음 기술 프로필은 Active Directory Azure Active Directory 데이터를 읽고 쓰는 [기술 프로필](active-directory-technical-profile.md)입니다. 확장 파일에서 이러한 기술 프로필을 재정의 합니다. `PersistedClaims`를 사용 하 여 강력한 암호 정책을 사용 하지 않도록 설정 합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 다음과 같은 클레임 공급자를 추가 합니다.

```xml
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

정책 파일을 저장합니다.

## <a name="test-your-policy"></a>정책 테스트

### <a name="upload-the-files"></a>파일 업로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. **ID 경험 프레임워크** 를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드** 를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기** 를 선택 하 고 *TrustFrameworkExtensions.xml* 파일을 검색 한 다음 선택 합니다.
7. **업로드** 를 클릭합니다.

### <a name="run-the-policy"></a>정책 실행

1. 등록 또는 로그인 정책을 엽니다. 예를 들어 B2C_1A_signup_signin입니다.
2. **애플리케이션** 은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL** 에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행** 을 클릭합니다.
4. **지금 등록** 을 선택하고, 메일 주소를 입력한 다음, 새 암호를 입력합니다. 지침은 암호 제한 사항에서 제공됩니다. 사용자 정보 입력을 완료한 후 **만들기** 를 클릭합니다. 반환된 토큰의 콘텐츠가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 암호 변경을 구성](add-password-change-policy.md)하는 방법에 대해 알아봅니다.
- IEF 참조의 [조건자](predicates.md) 및 [PredicateValidations](predicates.md#predicatevalidations) 요소에 대해 자세히 알아보세요.


::: zone-end

---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 구성 | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 요구 사항을 구성하는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 04a37e6faf51787457d7ca4ab8434fd253deb2ed
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509150"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD(Azure Active Directory) B2C에서는 계정을 만들 때 사용자가 제공한 암호의 복잡성 요구 사항을 구성할 수 있습니다. 기본적으로 Azure AD B2C는 **강력한** 암호를 사용합니다. 이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)에서 암호 복잡성을 구성하는 방법을 보여 줍니다. [사용자 흐름](active-directory-b2c-reference-password-complexity.md)에서 암호 복잡성을 구성할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

[Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](active-directory-b2c-get-started-custom.md)에 있는 단계를 완료합니다.

## <a name="add-the-elements"></a>요소 추가

1. 시작 팩과 함께 다운로드한 *SignUpOrSignIn.xml* 파일을 복사하고 이름을 *SingUpOrSignInPasswordComplexity.xml*로 변경합니다.
2. *SingUpOrSignInPasswordComplexity.xml* 파일을 열고 **PolicyId** 및 **PublicPolicyUri**를 새 정책 이름으로 변경합니다. 예를 들면, *B2C_1A_signup_signin_password_complexity*입니다.
3. `newPassword` 및 `reenterPassword`의 식별자를 사용하여 다음 **ClaimType** 요소를 추가합니다.

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predicates](predicates.md)에는 `IsLengthRange` 또는 `MatchesRegex` 메서드 형식이 있습니다. `MatchesRegex` 형식은 정규식과 일치시키는 데 사용됩니다. `IsLengthRange` 형식은 최소 및 최대 문자열 길이를 사용합니다. 이 요소가 다음 **Predicate** 요소에 없는 경우 **BuildingBlocks** 요소에 **Predicates** 요소를 추가합니다.

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. 각 **InputValidation** 요소는 정의된 **Predicate** 요소를 사용하여 생성됩니다. 이 요소를 사용하면 `and` 및 `or`와 비슷한 부울 집계를 수행할 수 있습니다. 이 요소가 다음 **InputValidation** 요소에 없는 경우 **BuildingBlocks** 요소에 **InputValidations** 요소를 추가합니다.

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. **PolicyProfile** 기술 프로필에 다음 요소가 있는지 확인합니다.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. 정책 파일을 저장합니다.

## <a name="test-your-policy"></a>정책 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토할 수 있도록 Azure AD B2C 토큰이 `https://jwt.ms`에 반환되도록 하는 것이 유용할 수 있습니다.

### <a name="upload-the-files"></a>파일 업로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드**를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기**를 선택한 후 *SingUpOrSignInPasswordComplexity.xml* 파일을 검색하여 선택합니다.
7. **업로드**를 클릭합니다.

### <a name="run-the-policy"></a>정책 실행

1. 변경한 정책을 엽니다. 예를 들면, *B2C_1A_signup_signin_password_complexity*입니다.
2. **애플리케이션**은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL**에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행**을 클릭합니다.
4. **지금 등록**을 선택하고, 메일 주소를 입력한 다음, 새 암호를 입력합니다. 지침은 암호 제한 사항에서 제공됩니다. 사용자 정보 입력을 완료한 후 **만들기**를 클릭합니다. 반환된 토큰의 콘텐츠가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경을 구성](active-directory-b2c-reference-password-change-custom.md)하는 방법을 알아봅니다.



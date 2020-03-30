---
title: 사용자 지정 정책에서 전화 계수 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 전화 요소 기술 프로필을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332661"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 전화 요소 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 전화 번호 등록 및 확인을 지원합니다. 이 기술 프로필:

- 사용자와 상호 작용하는 사용자 인터페이스를 제공합니다.
- 콘텐츠 정의를 사용하여 모양과 느낌을 제어합니다.
- 전화 통화와 문자 메시지를 모두 지원하여 전화 번호의 유효성을 검사합니다.
- 여러 전화 번호를 지원합니다. 사용자는 전화 번호 중 하나를 선택하여 확인할 수 있습니다.  
- 전화 번호가 제공되면 전화 팩터 사용자 인터페이스에서 사용자에게 전화 번호 확인을 요청합니다. 제공되지 않으면 사용자에게 새 전화 번호를 등록하도록 요청합니다.
- 사용자가 새 전화 번호를 제공했는지 여부를 나타내는 클레임을 반환합니다. 이 클레임을 사용하여 전화 번호를 Azure AD 사용자 프로필에 유지해야 하는지 여부를 결정할 수 있습니다.  

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **처리기** 특성에는 전화 요소에 대해 Azure AD B2C에서 사용하는 프로토콜 처리기 어셈블리의 정규화된 이름이 포함되어야 합니다.`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 등록 및 유효성 검사를 위한 전화 계수 기술 프로필을 보여 주며 있습니다.

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

InputClaims 요소에는 다음 클레임이 포함되어야 합니다. 청구 클레임 이름을 전화 계수 기술 프로필에 정의된 이름에 매핑할 수도 있습니다. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

다음 예제에서는 여러 전화 번호를 사용하는 것을 보여 줍니다. 자세한 내용은 [샘플 정책을](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)참조하십시오.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

InputClaims변환 요소에는 입력 클레임을 수정하거나 전화 요소 페이지에 표시하기 전에 새 클레임을 생성하는 데 사용되는 입력 클레임 변환 요소의 컬렉션이 포함될 수 있습니다.

## <a name="output-claims"></a>출력 클레임

OutputClaims 요소에는 전화 요소 기술 프로필에서 반환되는 클레임 목록이 포함되어 있습니다.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

OutputClaimsTransformations 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 OutputClaimsTransformation 요소 컬렉션을 포함할 수 있습니다.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | yes | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| 수동전화번호입력허용| 예 | 사용자가 전화 번호를 수동으로 입력할 수 있는지 여부를 지정합니다. 가능한 `true` 값: `false` 또는 (기본값).|

### <a name="ui-elements"></a>UI 요소

전화 계수 인증 페이지 사용자 인터페이스 요소를 [지역화할](localization-string-ids.md#azure-mfa-error-messages)수 있습니다.

## <a name="next-steps"></a>다음 단계

- MFA 스타터 [팩으로 소셜 및 로컬 계정을](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 확인합니다.


---
title: 사용자 지정 정책에서 phone 팩터 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 phone 팩터 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437460"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 phone 팩터 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 전화 번호를 등록 하 고 확인 하는 기능을 제공 합니다. 이 기술 프로필:

- 사용자와 상호 작용 하 여 전화 번호를 확인 하거나 등록할 수 있는 사용자 인터페이스를 제공 합니다.
- 전화 번호의 유효성을 검사 하는 전화 통화 및 문자 메시지를 지원 합니다.
- 에서는 여러 개의 전화 번호를 지원 합니다. 사용자는 확인할 전화 번호 중 하나를 선택할 수 있습니다.  
- 사용자가 새 전화 번호를 제공 했는지 여부를 나타내는 클레임을 반환 합니다. 이 클레임을 사용 하 여 Azure AD B2C 사용자 프로필에 전화 번호를 유지할지 여부를 결정할 수 있습니다.  
- [콘텐츠 정의](contentdefinitions.md) 를 사용 하 여 모양과 느낌을 제어 합니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예에서는 등록 및 유효성 검사에 대 한 phone 팩터 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>입력 클레임 변환

InputClaimsTransformations 요소는 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 입력 클레임 변환의 컬렉션을 포함할 수 있습니다. 다음 입력 클레임 변환은 나중에 입력 `UserId` 클레임 컬렉션에서 사용 되는 클레임을 생성 합니다.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>입력 클레임

InputClaims 요소는 다음 클레임을 포함 해야 합니다. 클레임의 이름을 phone 팩터 기술 프로필에 정의 된 이름에 매핑할 수도 있습니다. 

|  데이터 형식| 필수 | Description |
| --------- | -------- | ----------- | 
| 문자열| 예 | 사용자의 고유 식별자입니다. 클레임 이름 또는 대상 Claimtype은로 `UserId`설정 해야 합니다. 이 클레임은 개인 식별이 가능한 정보를 포함 해서는 안 됩니다.|
| string| 예 | 클레임 유형 목록입니다. 각 클레임에는 하나의 전화 번호가 포함 됩니다. 입력 클레임에 전화 번호가 포함 되어 있지 않으면 사용자에 게 새 전화 번호를 등록 하 고 확인 하 라는 메시지가 표시 됩니다. 유효성을 검사 한 전화 번호가 출력 클레임으로 반환 됩니다. 입력 클레임 중 하나에 전화 번호가 포함 된 경우 사용자에 게 확인 메시지를 표시 합니다. 여러 입력 클레임에 전화 번호가 포함 된 경우 사용자에 게 전화 번호 중 하나를 선택 하 고 확인 하 라는 메시지가 표시 됩니다. |

다음 예에서는 여러 전화 번호를 사용 하는 방법을 보여 줍니다. 자세한 내용은 [샘플 정책](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)을 참조 하세요.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

OutputClaims 요소는 phone 팩터 기술 프로필에서 반환 하는 클레임 목록을 포함 합니다.

|  데이터 형식| 필수 | 설명 |
|  -------- | ----------- |----------- |
| boolean | 예 | 사용자가 새 전화 번호를 입력 했는지 여부를 나타냅니다. 클레임 이름 또는 대상 Claimtype은로 설정 해야 합니다.`newPhoneNumberEntered`|
| string| 예 | 확인 된 전화 번호입니다. 클레임 이름 또는 대상 Claimtype은로 `Verified.OfficePhone`설정 해야 합니다.|

OutputClaimsTransformations 요소는 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 OutputClaimsTransformation 요소의 컬렉션을 포함할 수 있습니다.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 예 | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| ManualPhoneNumberEntryAllowed| 아니요 | 사용자가 전화 번호를 수동으로 입력할 수 있는지 여부를 지정 합니다. 가능한 값은 `true`, 또는 `false` (기본값)입니다.|
| authenticationMode 설정 | 아니요 | 전화 번호의 유효성을 검사 하는 메서드입니다. 가능한 값은 `sms`, `phone`또는 `mixed` (기본값)입니다.|
| 설정. 자동 전화| 아니요| 기술 프로필에서 자동으로 전화를 거 나 SMS를 보낼지 여부를 지정 합니다. 가능한 값은 `true`, 또는 `false` (기본값)입니다. 자동 전화 걸기를 사용 `setting.authenticationMode` 하려면 메타 데이터를 `sms`또는 `phone`로 설정 해야 합니다. 입력 클레임 컬렉션에는 전화 번호가 하나 있어야 합니다. |

### <a name="ui-elements"></a>UI 요소

전화 요소 인증 페이지 사용자 인터페이스 요소를 [지역화할](localization-string-ids.md#azure-mfa-error-messages)수 있습니다.

## <a name="next-steps"></a>다음 단계

- MFA 스타터 pack [을 사용 하 여 소셜 및 로컬 계정을](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 확인 합니다.

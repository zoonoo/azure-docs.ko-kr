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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437460"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 전화 요소 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 전화 번호 등록 및 확인을 지원합니다. 이 기술 프로필:

- 전화 번호를 확인하거나 등록하기 위해 사용자와 상호 작용하는 사용자 인터페이스를 제공합니다.
- 전화 통화 및 문자 메시지를 지원하여 전화 번호의 유효성을 검사합니다.
- 여러 전화 번호를 지원합니다. 사용자는 전화 번호 중 하나를 선택하여 확인할 수 있습니다.  
- 사용자가 새 전화 번호를 제공했는지 여부를 나타내는 클레임을 반환합니다. 이 클레임을 사용하여 전화 번호를 Azure AD B2C 사용자 프로필에 유지해야 하는지 여부를 결정할 수 있습니다.  
- 콘텐츠 [정의를](contentdefinitions.md) 사용하여 모양과 느낌을 제어합니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **처리기** 특성에는 전화 요소에 대해 Azure AD B2C에서 사용하는 프로토콜 처리기 어셈블리의 정규화된 이름이 포함되어야 합니다.`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 등록 및 유효성 검사를 위한 전화 계수 기술 프로필을 보여 주며 있습니다.

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>입력 클레임 변환

InputClaims변환 요소에는 입력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 입력 클레임 변환 컬렉션이 포함될 수 있습니다. 다음 입력 클레임 변환은 `UserId` 입력 클레임 컬렉션의 나중에 사용되는 클레임을 생성합니다.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>입력 클레임

InputClaims 요소에는 다음 클레임이 포함되어야 합니다. 청구 클레임 이름을 전화 계수 기술 프로필에 정의된 이름에 매핑할 수도 있습니다. 

|  데이터 형식| 필수 | Description |
| --------- | -------- | ----------- | 
| 문자열| 예 | 사용자의 고유 식별자입니다. 클레임 이름 또는 파트너 클레임 유형을 `UserId`로 설정해야 합니다. 이 클레임에는 개인 식별 정보가 포함되어서는 안 됩니다.|
| 문자열| 예 | 클레임 유형 목록입니다. 각 클레임에는 하나의 전화 번호가 포함됩니다. 입력 클레임중 전화 번호가 포함되어 있지 않으면 새 전화 번호를 등록하고 확인하라는 메시지가 표시됩니다. 유효성이 검사된 전화 번호는 출력 클레임으로 반환됩니다. 입력 클레임 중 하나에 전화 번호가 포함된 경우 사용자에게 전화 번호를 확인하라는 메시지가 표시됩니다. 여러 입력 클레임에 전화 번호가 포함된 경우 사용자는 전화 번호 중 하나를 선택하고 확인하라는 메시지가 표시됩니다. |

다음 예제에서는 여러 전화 번호를 사용하는 것을 보여 줍니다. 자세한 내용은 [샘플 정책을](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)참조하십시오.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

OutputClaims 요소에는 전화 요소 기술 프로필에서 반환되는 클레임 목록이 포함되어 있습니다.

|  데이터 형식| 필수 | Description |
|  -------- | ----------- |----------- |
| boolean | 예 | 사용자가 새 전화 번호를 입력했는지 여부를 나타냅니다. 클레임 이름 또는 파트너 클레임 유형을`newPhoneNumberEntered`|
| 문자열| 예 | 확인된 전화 번호입니다. 클레임 이름 또는 파트너 클레임 유형을 `Verified.OfficePhone`로 설정해야 합니다.|

OutputClaims변환 요소에는 출력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 OutputClaims변환 요소의 컬렉션이 포함될 수 있습니다.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| 특성 | 필수 | Description |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 예 | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| 수동전화번호입력허용| 예 | 사용자가 전화 번호를 수동으로 입력할 수 있는지 여부를 지정합니다. 가능한 값: `true` `false` " 또는 (기본값) .|
| 설정.인증모드 | 예 | 전화 번호의 유효성을 검사하는 방법입니다. 가능한 `sms`값: `phone`" `mixed` 또는 (기본값)|
| 설정.자동 다이얼| 예| 기술 프로파일이 자동 다이얼또는 SMS를 자동 전송할지 여부를 지정합니다. 가능한 값: `true` `false` " 또는 (기본값) . 자동 다이얼을 `setting.authenticationMode` 사용하려면 `sms`메타데이터를 `phone`로 설정하거나 . 입력 클레임 컬렉션에는 단일 전화 번호가 있어야 합니다. |

### <a name="ui-elements"></a>UI 요소

전화 계수 인증 페이지 사용자 인터페이스 요소를 [지역화할](localization-string-ids.md#azure-mfa-error-messages)수 있습니다.

## <a name="next-steps"></a>다음 단계

- MFA 스타터 [팩으로 소셜 및 로컬 계정을](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 확인합니다.

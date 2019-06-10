---
title: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 일반 클레임 변환 예제 | Microsoft Docs
description: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 일반 클레임 변환의 예제를 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5f8068ea7e97343749c719d2d0800e20701079c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510993"
---
# <a name="general-claims-transformations"></a>일반 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Active Directory) B2C 내 ID 경험 프레임워크 스키마의 일반 클레임 변환 사용을 위한 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="doesclaimexist"></a>DoesClaimExist

**inputClaim** 유무를 확인하고 확인 결과에 따라 **outputClaim**를 true 또는 false로 설정합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |모두 | 유무를 확인해야 하는 입력 클레임입니다. |
| OutputClaim | outputClaim | 부울 | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

특정 클레임이 있는지 여부나 값을 포함하는지 여부를 확인하려면 다음 클레임 변환을 사용합니다. 반환 값은 클레임이 있는지 여부를 나타내는 부울입니다. 다음 예제에서는 전자 메일 주소가 있는지 확인합니다.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **inputClaim**: someone@contoso.com
- 출력 클레임: 
    - **outputClaim**: true

## <a name="hash"></a>해시

솔트와 비밀을 사용하여 제공된 입력 텍스트를 해시합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 일반 텍스트(plaintext) | 문자열 | 암호화할 입력 클레임입니다. |
| InputClaim | salt | 문자열 | 솔트 매개 변수입니다. `CreateRandomString` 클레임 변환을 사용하여 임의 값을 만들 수 있습니다. |
| InputParameter | randomizerSecret | 문자열 | 기존 Azure AD B2C **정책 키**를 가리킵니다. 새로 만들려면: Azure AD B2C 테넌트에서 **B2C 설정 > ID 경험 프레임워크**를 선택합니다. 테넌트에 사용 가능한 키를 보려면 **정책 키**를 선택합니다. **추가**를 선택합니다. **옵션**에서 **수동**을 선택합니다. 이름을 입력합니다. B2C_1A_ 접두사가 자동으로 추가될 수 있습니다. 비밀 상자에 사용하려는 비밀을 1234567890과 같이 입력합니다. 키 사용에서 **비밀**을 선택합니다. **만들기**를 선택합니다. |
| OutputClaim | 해시 | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다. `plaintext` inputClaim에구성 된 클레임입니다. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
    - **plaintext**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- 출력 클레임: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=




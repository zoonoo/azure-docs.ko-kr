---
title: Azure Active Directory B2C의 ID 환경 프레임워크 스키마에 대한 정수 클레임 변환 예 | Microsoft Docs
description: Azure Active Directory B2C의 ID 환경 프레임워크 스키마에 대한 정수 클레임 변환 예입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44383033"
---
# <a name="integer-claims-transformations"></a>정수 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Azure Active Directory) B2C에서 ID 환경 프레임워크 스키마의 정수 클레임 변환을 사용하는 예를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

long 데이터 형식을 문자열 데이터 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 문자열로 변환할 ClaimType입니다. |
| OutputClaim | outputClaim | string | 이 ClaimsTransformation이 호출된 후 생성되는 ClaimType입니다. |

이 예제에서는 값 형식이 long인 `numericUserId` 클레임이 값 형식이 문자열인 `UserId` 클레임으로 변환됩니다.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
    - **inputClaim**: 12334(long)
- 출력 클레임: 
    - **outputClaim**: "12334"(문자열)


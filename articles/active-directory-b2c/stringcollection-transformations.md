---
title: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 StringCollection 클레임 변환 예제 | Microsoft Docs
description: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 StringCollection 클레임 변환 예제를 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98453daeb34d093b49cdcc636f68c3d7ae017126
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512429"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Active Directory) B2C 내 ID 경험 프레임워크 스키마의 문자열 컬렉션 클레임 변환 사용을 위한 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="additemtostringcollection"></a>AddItemToStringCollection

새 stringCollection 클레임에 문자열 클레임을 추가합니다. 

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 항목 | 문자열 | 출력 클레임에 추가할 ClaimType입니다. |
| InputClaim | collection | stringCollection | [선택 사항] 지정하는 경우 클레임 변환에서 이 컬렉션의 항목을 복사한 다음 출력 컬렉션 클레임 끝에 추가합니다. |
| OutputClaim | collection | stringCollection | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다. |

신규 또는 기존 stringCollection에 문자열을 추가하려면 이 클레임 변환을 사용합니다. 이 클레임 변환은 일반적으로 **AAD-UserWriteUsingAlternativeSecurityId** 기술 프로필에서 사용됩니다. 새 소셜 계정을 만들기 전에 **CreateOtherMailsFromEmail** 클레임 변환은 ClaimType을 읽고 값을 **otherMails** ClaimType에 추가합니다. 

다음 클레임 변환에서는 **email** ClaimType을 **otherMails** ClaimType에 추가합니다.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- 출력 클레임: 
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

새 stringCollection 클레임에 문자열 매개 변수를 추가합니다. 

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [선택 사항] 지정하는 경우 클레임 변환에서 이 컬렉션의 항목을 복사한 다음 출력 컬렉션 클레임 끝에 추가합니다. |
| InputParameter | 항목 | 문자열 | 출력 클레임에 추가할 값입니다. |
| OutputClaim | collection | stringCollection | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다. |

신규 또는 기존 stringCollection에 문자열 값을 추가하려면 이 클레임 변환을 사용합니다. 다음 예제에서는 상수 전자 메일 주소(admin@contoso.com)를 **otherMails** 클레임에 추가합니다. 

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **collection**: ["someone@outlook.com"]
- 입력 매개 변수 
  - **item**: "admin@contoso.com"
- 출력 클레임:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

제공된 문자열 컬렉션에서 첫 번째 항목을 가져옵니다. 

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 클레임 변환에서 항목을 가져오는 데 사용하는 ClaimType입니다. |
| OutputClaim | extractedItem | 문자열 | 이 ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. 컬렉션의 첫 번째 항목입니다. |

다음 예제에서는 **otherMails** 클레임을 읽고 첫 번째 항목을 **email** 클레임에 반환합니다. 

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- 출력 클레임: 
  - **extractedItem**: "someone@outlook.com"


---
title: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 JSON 클레임 변환 예제 | Microsoft Docs
description: Azure Active Directory B2C의 ID 경험 프레임워크 스키마용 JSON 클레임 변환의 예제를 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a026d205d3ab855ecbb51048e7464df6fb4a094
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510761"
---
# <a name="json-claims-transformations"></a>JSON 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Active Directory) B2C 내 ID 경험 프레임워크 스키마의 JSON 클레임 변환 사용을 위한 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="getclaimfromjson"></a>GetClaimFromJson

JSON 데이터에서 지정된 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 문자열 | 클레임 변환에서 항목을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | claimToExtract | 문자열 | 추출할 JSON 요소의 이름입니다. |
| OutputClaim | extractedClaim | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다(_claimToExtract_ 입력 매개 변수에 지정된 요소 값). |

다음 예제에서는 클레임 변환이 JSON 데이터에서 `emailAddress` 요소를 추출했습니다. `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- 입력 매개 변수:
    - **claimToExtract**: emailAddress
- 출력 클레임: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

JSON 데이터에서 지정된 요소의 목록을 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | 문자열 | 클레임 변환에서 클레임을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | errorOnMissingClaims | 부울 | 클레임 중 하나가 없으면 오류를 throw할지 여부를 지정합니다. |
| InputParameter | includeEmptyClaims | 문자열 | 빈 클레임을 포함할지 여부를 지정합니다. |
| InputParameter | jsonSourceKeyName | 문자열 | 요소 키 이름입니다. |
| InputParameter | jsonSourceValueName | 문자열 | 요소 값 이름입니다. |
| OutputClaim | 컬렉션 | string, int, boolean, datetime |추출할 클레임 목록입니다. 클레임 이름은 _jsonSourceClaim_ 입력 클레임에 지정된 이름과 같아야 합니다. |

다음 예제에서 클레임 변환은 JSON 데이터에서 email(string), displayName(string), membershipNum(int), active(boolean) 및 birthdate(datetime) 클레임을 추출합니다.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- 입력 클레임:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- 입력 매개 변수:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- 출력 클레임:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

JSON 데이터에서 지정된 숫자 (long) 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 문자열 | 클레임 변환에서 클레임을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | claimToExtract | 문자열 | 추출할 JSON 요소의 이름입니다. |
| OutputClaim | extractedClaim | long | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(_claimToExtract_ 입력 매개 변수에 지정된 요소 값). |

다음 예제에서는 클레임 변환이 JSON 데이터에서 `id` 요소를 추출합니다.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- 입력 매개 변수
    - **claimToExtract**: id
- 출력 클레임: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

JSON 데이터 배열에서 첫 번째 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | 문자열 | 클레임 변환에서 JSON 배열의 항목을 가져오는 데 사용하는 ClaimType입니다. |
| OutputClaim | extractedClaim | 문자열 | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(JSON 배열의 첫 번째 요소). |

다음 예제에서는 클레임 변환이 JSON 배열의 첫 번째 요소(전자 메일 주소)를 추출합니다. `["someone@example.com", "Someone", 6353399]`

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- 출력 클레임: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML 데이터를 JSON 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | 문자열 | 클레임 변환에서 데이터를 XML에서 JSON 형식으로 변환하는 데 사용하는 ClaimType입니다. |
| OutputClaim | json : | 문자열 | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(JSON 형식의 데이터). |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

다음 예제에서는 클레임 변환이 다음 XML 데이터를 JSON 형식으로 변환합니다.

#### <a name="example"></a>예
입력 클레임:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

출력 클레임:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


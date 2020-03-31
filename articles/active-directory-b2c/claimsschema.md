---
title: ClaimsSchema  - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 ClaimsSchema 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78397808"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema** 요소는 정책의 일부로 참조할 수 있는 클레임 형식을 정의합니다. 클레임 스키마에서 클레임을 선언합니다. 클레임은 이름, 성, 표시 이름, 전화 번호 등일 수 있습니다. ClaimsSchema 요소에는 **ClaimType** 요소 목록이 포함됩니다. **ClaimType** 요소에는 **Id** 특성(클레임 이름)이 포함됩니다.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

**ClaimType** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 클레임 형식에 사용되는 식별자입니다. 다른 요소는 정책에서 이 식별자를 사용할 수 있습니다. |

**ClaimType** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 다양한 화면에서 사용자에게 표시되는 제목입니다. 값을 [지역화](localization.md)할 수 있습니다. |
| DataType | 1:1 | 클레임의 형식입니다. |
| DefaultPartnerClaimTypes | 0:1 | 지정된 프로토콜에 사용할 파트너 기본 클레임 형식입니다. **InputClaim** 또는 **OutputClaim** 요소에 지정된 **PartnerClaimType**에서 값을 덮어쓸 수 있습니다. 이 요소를 사용하여 프로토콜의 기본 이름을 지정합니다.  |
| Mask | 0:1 | 클레임을 표시할 때 적용할 수 있는 마스킹 문자로 구성된 선택적 문자열입니다. 예를 들어 전화 번호 324-232-4343은 XXX-XXX-4343으로 마스킹할 수 있습니다. |
| UserHelpText | 0:1 | 사용자가 클레임의 용도를 파악하는 데 도움이 될 수 있는 클레임 형식의 설명입니다. 값을 [지역화](localization.md)할 수 있습니다. |
| UserInputType | 0:1 | 클레임 형식의 클레임 데이터를 수동으로 입력할 때 사용자에게 제공해야 하는 입력 컨트롤 유형입니다. 이 페이지 아래쪽에 정의되어 있는 사용자 입력 형식을 참조하세요. |
| 관리자 도움말 텍스트 | 0:1 | 관리자가 해당 용도를 이해하는 데 도움이 될 수 있는 클레임 유형에 대한 설명입니다. |
| 제한 사항 | 0:1 | 정규식(Regex) 또는 허용되는 값 목록과 같은 이 클레임의 값 제한입니다. 값을 [지역화](localization.md)할 수 있습니다. |
PredicateValidationReference| 0:1 | **PredicateValidationsInput** 요소에 대한 참조입니다. **PredicateValidationReference** 요소를 사용하면 적절한 형식의 데이터만 입력하도록 유효성 검사 프로세스를 수행할 수 있습니다. 자세한 내용은 [Predicates](predicates.md)를 참조하세요. |



### <a name="datatype"></a>DataType

**DataType** 요소는 다음 값을 지원합니다.

| Type | Description |
| ------- | ----------- |
|boolean|부울(`true` 또는 `false`) 값을 나타냅니다.|
|date| 일반적으로 일의 날짜로 표현되는 인스턴트 시간을 나타냅니다. 날짜 값은 ISO 8601 규칙을 따릅니다.|
|dateTime|일반적으로 날짜와 시간으로 표시된 시간을 나타냅니다. 날짜 값은 ISO 8601 규칙을 따릅니다.|
|duration|연도, 월, 일, 시간, 분 및 초의 시간 간격을 나타냅니다. 의 형식은 `PnYnMnDTnHnMnS`여기서 `P` 양수를 `N` 나타내거나 음수 값을 나타냅니다. `nY`은 리터럴 `Y`연도 다음에 있는 연도입니다. `nMo`은 리터럴 `Mo`다음의 월 수입니다. `nD`은 일 수 다음에 리터럴입니다. `D` 예: `P21Y` 21년을 나타냅니다. `P1Y2Mo`1년, 2개월을 나타냅니다. `P1Y2Mo5D`1년, 2개월, 5일을 나타냅니다.  `P1Y2M5DT8H5M620S`1년, 2개월, 5일, 8시간, 5분, 20초를 나타냅니다.  |
|phoneNumber|전화 번호를 나타냅니다. |
|int| -2,147,483,648과 2,147,483,647 사이의 숫자를 나타냅니다.|
|long| -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 |
|문자열| 텍스트를 UTF-16 코드 단위의 시퀀스로 나타냅니다.|
|stringCollection|`string`의 컬렉션을 나타냅니다.|
|사용자 ID| 사용자 ID를 나타냅니다.|
|사용자 아이덴티티 컬렉션|`userIdentity`의 컬렉션을 나타냅니다.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes**는 다음 요소를 함할 수 있습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 프로토콜 | 1:n | 기본 파트너 클레임 형식 이름이 포함된 프로토콜 목록입니다. |

**Protocol** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 이름 | yes | Azure AD B2C에서 지원하는 유효한 프로토콜의 이름입니다. 가능한 값은 다음과 같습니다: OAuth1, OAuth2, SAML2, 오픈 아이드 커넥트. |
| PartnerClaimType | yes | 사용할 클레임 형식 이름입니다. |

다음 예제에서는 ID 경험 프레임워크가 SAML2 ID 공급자 또는 신뢰 당사자 애플리케이션과 상호 작용할 때 **surname** 클레임이 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`에 매핑되며 OpenIdConnect 및 OAuth2와 상호 작용할 때는 `family_name`에 매핑됩니다.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

그러므로 Azure AD B2C에서 발급하는 JWT 토큰에서는 ClaimType 이름 **surname** 대신 `family_name`이 생략됩니다.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

**Mask** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | yes | 클레임 마스크의 형식입니다. 가능한 값은 `Simple` 또는 `Regex`입니다. `Simple` 값은 문자열 클레임의 앞부분에 단순 텍스트 마스크가 적용됨을 나타냅니다. `Regex` 값은 문자열 클레임 전체에 정규식이 적용됨을 나타냅니다.  `Regex` 값을 지정하는 경우에는 사용할 정규식과 함께 선택적 특성도 정의해야 합니다. |
| `Regex` | 예 | 을 사용하도록 설정된 경우 **`Type`** 사용할 정규식을 지정합니다. `Regex`

다음 예제에서는 `Simple` 마스크를 사용하여 **PhoneNumber** 클레임을 구성합니다.

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

이 경우 ID 경험 프레임워크에서는 첫 6자리 숫자를 숨기고 전화 번호를 렌더링합니다.

![Xs에 의해 마스크 처음 여섯 자리와 브라우저에 표시된 전화 번호 주장](./media/claimsschema/mask.png)

다음 예제에서는 `Regex` 마스크를 사용하여 **AlternateEmail** 클레임을 구성합니다.

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

이 경우 ID 경험 프레임워크에서는 전자 메일 주소의 첫 문자와 전자 메일 도메인 이름만 렌더링합니다.

![별표에 의해 마스크 문자와 브라우저에 표시된 이메일 주장](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>제한 사항

**Restriction** 요소는 다음 특성을 포함할 수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| MergeBehavior | 예 | 식별자가 같은 상위 정책에서 ClaimType과 열거 값을 병합하는 데 사용할 메서드입니다. 기본 정책에 지정된 클레임을 덮어쓰는 경우 이 특성을 사용합니다. 가능한 값은 `Append`, `Prepend` 또는 `ReplaceAll`입니다. `Append` 값은 상위 정책에 지정된 컬렉션 끝에 추가해야 하는 데이터 컬렉션입니다. `Prepend` 값은 상위 정책에 지정된 컬렉션 앞에 추가해야 하는 데이터 컬렉션입니다. `ReplaceAll` 값은 상위 정책에 지정되어 있는 무시해야 하는 데이터 컬렉션입니다. |

**Restriction** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 열거형 | 1:n | 사용자가 클레임을 선택하는 데 사용할 수 있는 사용자 인터페이스의 옵션(예: 드롭다운의 값)입니다. |
| 패턴 | 1:1 | 사용할 정규식입니다. |

#### <a name="enumeration"></a>열거형

**열거** 요소는 사용자가 `CheckboxMultiSelect`에 `DropdownSingleSelect`있는 값 또는 `RadioSingleSelect`등의 사용자 인터페이스에서 클레임에 대해 선택할 수 있는 사용 가능한 옵션을 정의합니다. 또는 [LocalizedCollections](localization.md#localizedcollections) 요소를 사용 하 여 사용 가능한 옵션을 정의 하 고 지역화 할 수 있습니다. 클레임 **열거형** 컬렉션에서 항목을 조회하려면 [GetMappedValueFromLocalized컬렉션](string-transformations.md#getmappedvaluefromlocalizedcollection) 클레임 변환을 사용합니다.

**Enumeration** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 텍스트 | yes | 이 옵션에 대해 사용자 인터페이스에서 사용자에게 표시되는 표시 문자열입니다. |
|값 | yes | 이 옵션 선택과 연결된 클레임 값입니다. |
| SelectByDefault | 예 | UI에서 이 옵션을 기본적으로 선택해야 하는지 여부를 나타냅니다. 가능한 값은 True 또는 False입니다. |

다음 예제에서는 기본값을 `New York`으로 설정하여 **city** 드롭다운 목록 클레임을 구성합니다.

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

기본값이 New York으로 설정된 드롭다운 city 목록은 다음과 같습니다.

![브라우저에서 렌더링되고 기본값을 표시하는 드롭다운 제어](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>패턴

**Pattern** 요소는 다음 특성을 포함할 수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| RegularExpression | yes | 이 형식의 클레임이 유효하려면 일치해야 하는 정규식입니다. |
| HelpText | 예 | 정규식 검사가 실패하면 사용자에 대한 오류 메시지입니다. |

다음 예제에서는 정규식 입력 유효성 검사 및 도움말 텍스트를 사용하여 **email** 클레임을 구성합니다.

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

이 경우 ID 경험 프레임워크에서는 전자 메일 형식 입력 유효성 검사가 포함된 전자 메일 주소 클레임을 렌더링합니다.

![정규식 제한에 의해 트리거된 오류 메시지를 표시하는 TextBox](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C는 클레임 형식의 클레임 데이터를 수동으로 입력할 때 사용할 수 있는 텍스트 상자, 암호, 드롭다운 목록 등의 다양한 사용자 입력 유형을 지원합니다. [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 및 표시 [컨트롤을](display-controls.md)사용하여 사용자로부터 정보를 수집할 때 **UserInputType을** 지정해야 합니다.

**UserInputType** 사용 가능한 사용자 입력 형식:

| UserInputType | 지원되는 클레임 유형 | 설명 |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |다중 선택 드롭다운 상자. 클레임 값은 선택한 값의 쉼표 구분 기호 문자열로 표시됩니다. |
|DateTimeDropdown | `date`, `dateTime` |날짜, 월 및 연도를 선택하는 드롭다운입니다. |
|DropdownSingleSelect |`string` |단일 선택 드롭다운 상자. 클레임 값은 선택한 값입니다.|
|EmailBox | `string` |이메일 입력 필드. |
|Paragraph | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|단락 태그에만 텍스트를 표시하는 필드입니다. |
|암호 | `string` |암호 텍스트 상자입니다.|
|RadioSingleSelect |`string` | 라디오 버튼의 컬렉션입니다. 클레임 값은 선택한 값입니다.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| 읽기 전용 텍스트 상자. |
|TextBox |`boolean`, `int`, `string` |한 줄 텍스트 상자입니다. |


#### <a name="textbox"></a>TextBox

**TextBox** 사용자 입력 유형은 한 줄 텍스트 상자를 제공하는 데 사용됩니다.

![클레임 유형에 지정된 속성을 표시하는 TextBox](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

**EmailBox** 사용자 입력 유형은 기본적인 전자 메일 입력 필드를 제공하는 데 사용됩니다.

![클레임 유형에 지정된 속성을 표시하는 EmailBox](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>암호

**Password** 사용자 입력 유형은 사용자가 입력하는 암호를 기록하는 데 사용됩니다.

![password가 포함된 클레임 형식 사용](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

**DateTimeDropdown** 사용자 입력 유형은 년/월/일을 선택하는 드롭다운 집합을 제공하는 데 사용됩니다. Predicates 및 PredicateValidations 요소를 사용하여 최소 및 최대 날짜 값을 제어할 수 있습니다. 자세한 내용은 [Predicates 및 PredicateValidations](predicates.md)의 **날짜 범위 구성** 섹션을 참조하세요.

![datetimedropdown이 포함된 클레임 형식 사용](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** 사용자 입력 유형은 사용자가 옵션 하나를 선택할 수 있는 라디오 단추 모음을 제공하는 데 사용됩니다.

![radiodsingleselect가 포함된 클레임 형식 사용](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

**DropdownSingleSelect** 사용자 입력 유형은 사용자가 옵션 하나를 선택할 수 있는 드롭다운 상자를 제공하는 데 사용됩니다.

![dropdownsingleselect가 포함된 클레임 형식 사용](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

**CheckboxMultiSelect** 사용자 입력 유형은 사용자가 여러 옵션을 선택할 수 있는 확인란 모음을 제공하는 데 사용됩니다.

![checkboxmultiselect가 포함된 클레임 형식 사용](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

**Readonly** 사용자 입력 유형은 클레임과 값을 표시할 읽기 전용 필드를 제공하는 데 사용됩니다.

![readonly가 포함된 클레임 형식 사용](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

**Paragraph** 사용자 입력 유형은 단락 태그에 텍스트만 표시하는 필드를 제공하는 데 사용됩니다.  예를 들어 &lt;p&gt;text&lt;/p&gt;를 사용할 수 있습니다. 자체 어설션된 기술 프로필의 **단락** 사용자 입력 유형은 `OutputClaim` 특성(기본값)을 `Required` `false` 설정해야 합니다.

![paragraph가 포함된 클레임 형식 사용](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

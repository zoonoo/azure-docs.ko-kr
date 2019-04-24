---
title: Azure Active Directory의 특성 매핑에 대한 식 작성 | Microsoft Docs
description: Azure Active Directory에서 SaaS 앱 개체의 자동화된 프로비전 중 허용되는 형식으로 특성 값을 변환하기 위해 식 매핑을 사용하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed081b32fd8ac464f7ec66f97c6867708a6f8533
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291435"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory의 특성 매핑에 대한 식 작성
SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 경우, 사용자의 데이터를 SaaS 애플리케이션에 대해 사용하는 형식으로 변환할 수 있는 스크립트 방식의 식을 작성해야 합니다.

## <a name="syntax-overview"></a>구문 개요
특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은  <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 서로 함수를 중첩할 수 있습니다. 예를 들면 다음과 같습니다. <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 예를 들면 다음과 같습니다. "미국"
  3. 기타 함수 예를 들면 다음과 같습니다. FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 예를 들면 다음과 같습니다. "회사 이름: \\"Contoso\\""

## <a name="list-of-functions"></a>함수 목록
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

- - -
### <a name="append"></a>추가
**함수:**<br>  Append(source, suffix)

**설명:**<br>  원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **접미사** |필수 |문자열 |원본 값의 끝에 추가하려는 문자열입니다. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**함수:**<br>  FormatDateTime(source, inputFormat, outputFormat)

**설명:**<br>  한 형식의 날짜 문자열을 다른 형식으로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **inputFormat** |필수 |문자열 |원본 값의 예상된 형식입니다. 지원되는 형식은 [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)를 참조하세요. |
| **outputFormat** |필수 |문자열 |출력 날짜의 형식입니다. |

- - -
### <a name="join"></a>Join
**함수:**<br>  Join(separator, source1, source2, …)

**설명:**<br> 다중 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하고 Join()은 Append()와 유사하며, 각 값은 **separator** 문자열로 구분됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값에 의해 구분됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **구분 기호** |필수 |문자열 |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
| **source1  … sourceN** |필수, 시간 변수 |문자열 |값이 함께 조인될 문자열입니다. |

- - -
### <a name="mid"></a>Mid
**함수:**<br>  Mid(source, start, length)

**설명:**<br>  원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |일반적으로 특성 이름입니다. |
| **시작** |필수 |정수 |부분 문자열이 시작될 **원본** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
| **length** |필수 |정수 |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 부분 문자열을 반환합니다. |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**함수:**<br> NormalizeDiacritics(원본)

**설명:**<br> 하나의 문자열 인수가 필요합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다. 일반적으로 분음 부호 문자(악센트 부호)를 포함하는 이름 및 성을 다양한 사용자 ID(예: 사용자 계정 이름, SAM 계정 이름 및 이메일 주소)에 사용할 수 있는 올바른 값으로 변환하는 데 사용됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 | 일반적으로 이름 또는 성 특성입니다. |

- - -
### <a name="not"></a>not
**함수:**<br>  Not(source)

**설명:**<br> **원본**의 부울 값을 대칭 이동합니다. **원본** 값이 "*True*"인 경우 "*False*"를 반환합니다. 그렇지 않은 경우 "*True*"를 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |부울 문자열 |예상 **원본** 값은 "True" 또는 "False"입니다. |

- - -
### <a name="replace"></a>Replace
**함수:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:**<br>
 문자열 내 값을 대체합니다. 제공된 매개 변수에 따라 다르게 작동합니다.

* **oldValue** 및 **replacementValue**가 제공되는 경우:
  
  * 소스에서 oldValue의 모든 항목을 replacementValue로 대체합니다.
* **oldValue** 및 **template**이 제공되는 경우:
  
  * **template**에서 **oldValue**의 모든 항목을 **원본** 값으로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementValue**가 제공되는 경우:
  
  * 원본 문자열에서 OldValueRegexPattern과 일치하는 모든 값을 replacementValue로 대체합니다.
* **regexPattern**, **regexGroupName**, **replacementPropertyName**이 제공되는 경우:
  
  * **source**에 값이 없는 경우 **source**가 반환됩니다.
  * **source**에 값이 있는 경우 **regexPattern** 및 **regexGroupName**을 사용하여 **replacementPropertyName**으로 속성에서 대체 값을 추출합니다. 대체 값이 결과로 반환됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **oldValue** |옵션 |문자열 |**원본** 또는 **템플릿**에서 대체될 값입니다. |
| **regexPattern** |옵션 |String |**원본**에서 대체될 값에 대한 Regex 패턴입니다. 또는 replacementPropertyName를 사용하면 대체 속성에서 값을 추출하는 패턴입니다. |
| **regexGroupName** |옵션 |문자열 |**regexPattern**내 그룹의 이름입니다. replacementPropertyName를 사용하는 경우에만 replacement 속성에서 replacementValue로 이 그룹의 값을 추출합니다. |
| **replacementValue** |옵션 |문자열 |이전 값과 대체할 새로운 값입니다. |
| **replacementAttributeName** |옵션 |문자열 |원본에 값이 없는 경우 대체 값에 사용할 특성의 이름입니다. |
| **template** |옵션 |문자열 |**template** 값이 제공되면, 템플릿 내에서 **oldValue**를 찾아 원본 값으로 바꿉니다. |

- - -
### <a name="selectuniquevalue"></a>SelectUniqueValue
**함수:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**설명:**<br> 최소 두 개의 인수가 필요하며, 이는 표현식을 사용하여 정의된 고유한 값 생성 규칙입니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다. 발견된 첫 번째 고유한 값이 반환됩니다. 모든 값이 이미 대상에 있는 경우 항목은 위탁되고 감사 로그에 이유가 기록됩니다. 제공할 수 있는 인수 수에 상한은 없습니다.

> [!NOTE]
>1. 최상위 레벨 함수이므로 중첩할 수 없습니다.
>2. 이 함수는 항목 만들기에만 사용할 수 있습니다. 특성과 함께 사용할 경우 **매핑 적용** 속성을 **개체를 만드는 동안만**으로 설정합니다.


**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2개 이상 필요, 상한 없음 |문자열 | 평가할 고유한 값 생성 규칙 목록입니다. |


- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:**<br> SingleAppRoleAssignment([appRoleAssignments])

**설명:**<br> 지정된 애플리케이션에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다. 

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |문자열 |**[appRoleAssignments]** 개체. |

- - -
### <a name="split"></a>분할
**함수:**<br> Split(원본, 구분 기호)

**설명:**<br> 지정된 구분 기호 문자를 사용하여 문자열을 다중 값 배열로 분할합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |**원본** 값입니다. |
| **delimiter** |필수 |문자열 |문자열을 분할하는 데 사용할 문자(예: ",")를 지정 |

- - -
### <a name="stripspaces"></a>StripSpaces
**함수:**<br>  StripSpaces(source)

**설명:**<br>  원본 문자열에서 모든 공백(" ")을 제거합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |**원본** 값입니다. |

- - -
### <a name="switch"></a>Switch
**함수:**<br>  Switch(source, defaultValue, key1, value1, key2, value2, …)

**설명:**<br> **원본** 값이 **key**와 일치하면, 해당 **key**의 **value**를 반환합니다. **원본** 값과 일치하는 키가 없으면 **defaultValue**를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |**Source** 값입니다. |
| **defaultValue** |옵션 |문자열 |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
| **key** |필수 |문자열 |**원본** 값과 비교할 **Key**입니다. |
| **값** |필수 |문자열 |키와 일치하는 **원본** 의 대체 값입니다. |

- - -
### <a name="tolower"></a>ToLower
**함수:**<br> ToLower(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 소문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름 |
| **문화권** |옵션 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

- - -
### <a name="toupper"></a>ToUpper
**함수:**<br> ToUpper(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 대문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **문화권** |옵션 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

## <a name="examples"></a>예
### <a name="strip-known-domain-name"></a>알려진 도메인 이름 제거
사용자 이름을 가져오려면 사용자의 전자 메일에서 알려진 도메인 이름을 제거해야 합니다. <br>
 예를 들어, 도메인이 "contoso.com"인 경우 다음 식을 사용할 수 있습니다.

**식:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**샘플 입출력:** <br>

* **입력**(메일): “john.doe@contoso.com”
* **출력**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>사용자 이름에 상수 접미사 추가
Salesforce 샌드박스를 사용하는 경우 동기화하기 전에 모든 사용자 이름에 추가 접미사를 추가해야할 수 있습니다.

**식:** <br>
`Append([userPrincipalName], ".test")`

**샘플 입/출력:** <br>

* **입력**: (userPrincipalName): “John.Doe@contoso.com”
* **출력**:  “John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성해야 합니다.

**식:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** <br>

* **입력**(givenName): "John"
* **입력**(surname): "Doe"
* **출력**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>문자열에서 분음 부호 제거
악센트 기호를 포함하지 않는 해당 문자로 악센트 기호를 포함하는 문자를 바꿔야 합니다.

**식:** <br>
NormalizeDiacritics([givenName])

**샘플 입/출력:** <br>

* **입력**(givenName): "Zoë"
* **출력**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>문자열을 다중 값 배열로 분할
문자열의 쉼표로 구분된 목록을 가져와 Salesforce의 PermissionSets 특성과 같은 다중 값 특성에 연결할 수 있는 배열로 분할해야 합니다. 이 예의 경우 Azure AD에서 사용 권한 세트의 목록을 extensionAttribute5에 채웠습니다.

**식:** <br>
Split([extensionAttribute5], ",")

**샘플 입/출력:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
SaaS 애플리케이션에 특정 형식의 날짜를 전송하려고 합니다. <br>
 예를 들어 ServiceNow에 대한 날짜 형식을 지정하려고 할 수 있습니다.

**식:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력**(extensionAttribute1): "20150123105347.1Z"
* **출력**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기

Azure AD에 저장된 상태 코드를 기반으로 사용자의 시간대를 정의해야 합니다. <br>
 상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력**(상태): "QLD"
* **출력**: "오스트레일리아/브리즈번"

### <a name="replace-characters-using-a-regular-expression"></a>정규식을 사용하여 문자를 바꿈
정규식 값과 일치하는 문자를 찾아 제거해야 합니다.

**식:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**샘플 입/출력:**

* **INPUT** (mailNickname: "john_doe72"
* **출력**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>생성된 userPrincipalName(UPN) 값을 소문자로 변환
아래 예에서 UPN 값은 PreferredFirstName 및 PreferredLastName 원본 필드를 연결하여 생성되고, ToLower 함수는 생성된 문자열에서 작동하여 모든 문자를 소문자로 변환합니다. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**샘플 입/출력:**

* **입력**(PreferredFirstName): "John"
* **입력**(PreferredLastName): "Smith"
* **출력**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UPN(userPrincipalName) 특성의 고유한 값 생성
사용자의 이름, 중간 이름, 성에 따라, UPN 특성에 값을 할당하려면 먼저 해당 UPN 특성에 대해 값을 생성하고 대상 AD 디렉터리에서 해당 값이 고유한지 확인해야 합니다.

**식:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**샘플 입/출력:**

* **입력**(PreferredFirstName): "John"
* **입력**(PreferredLastName): "Smith"
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 아직 없는 경우 “John.Smith@contoso.com”
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 이미 있는 경우 “J.Smith@contoso.com”
* **출력**: 위의 두 UPN 값이 디렉터리에 이미 있는 경우 “Jo.Smith@contoso.com”

## <a name="related-articles"></a>관련 문서
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)

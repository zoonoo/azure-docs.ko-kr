---
title: Azure AD에서 특성 매핑에 대한 식 작성
description: Azure Active Directory에서 SaaS 앱 개체의 자동화된 프로비전 중 허용되는 형식으로 특성 값을 변환하기 위해 식 매핑을 사용하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28e591234e28770a90bed827e4d36c6342661dd1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866597"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory의 특성 매핑에 대한 식 작성
SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 경우, 사용자의 데이터를 SaaS 애플리케이션에 대해 사용하는 형식으로 변환할 수 있는 스크립트 방식의 식을 작성해야 합니다.

## <a name="syntax-overview"></a>구문 개요
특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은 <br>
  *기능 이름(,`<<argument 1>>`)`<<argument N>>`*
* 서로 함수를 중첩할 수 있습니다. 예를 들어: <br> *함수원(함수투())`<<argument1>>`*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 예: "미국"
  3. 기타 함수 예: 함수원(,`<<argument1>>`FunctionTwo())`<<argument2>>`
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 예: "회사 이름: \\"Contoso""\\

## <a name="list-of-functions"></a>함수 목록
[부속](#append) &nbsp; &nbsp; &nbsp; [IsString](#isstring) &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; [Count](#count) &nbsp; [Item](#item) &nbsp; [Coalesce](#coalesce) &nbsp; [Mid](#mid) [Left](#left) &nbsp; [RemoveDuplicates](#removeduplicates) [Not](#not) [Guid](#guid) &nbsp; &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; &nbsp; &nbsp; [Join](#join) &nbsp; [Replace](#replace) [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [DateFromNum](#datefromnum) &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) [ConvertToBase64](#converttobase64) &nbsp; &nbsp; [IsNull](#isnull) &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; 비트 와 &nbsp; &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; &nbsp; &nbsp; 결합 &nbsp; &nbsp; &nbsp; convertToBase64 &nbsp; &nbsp; &nbsp; convertToUTF8Hex &nbsp; &nbsp; &nbsp; &nbsp; 카운트 &nbsp; &nbsp; &nbsp; CStr &nbsp; &nbsp; &nbsp; 날짜에서부터Num formatDatetime &nbsp; &nbsp; &nbsp; Guid &nbsp; IIF InStr IsNull &nbsp; IsNullOrEmpty &nbsp; IsNullIsNullIsPresent &nbsp; isNullIsString 항목 &nbsp; 조인 왼쪽 중간 정규화 해독제 제거 중복 selectUniqueValue를 대체 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; [ToUpper](#toupper) [StripSpaces](#stripspaces) &nbsp; [Switch](#switch) &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp;&nbsp; [Word](#word) [ToLower](#tolower) SingleAppRoleAssignment&nbsp; &nbsp; 분할&nbsp;&nbsp; 스트립 &nbsp; 스페이스 &nbsp; &nbsp; 스위치를 더 낮은 토퍼 워드로 전환&nbsp; &nbsp;&nbsp; &nbsp;

---
### <a name="append"></a>추가
**함수:**<br>  Append(source, suffix)

**설명:**<br>  원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **접미사** |필수 |String |원본 값의 끝에 추가하려는 문자열입니다. |

---
### <a name="bitand"></a>BitAnd
**함수:**<br> 비트앤드(값1, 값2)

**설명:**<br> 이 함수는 두 매개 변수를 전부 이진 표현으로 변환시키고 비트를 다음과 같이 설정합니다.

0 - value1 및 value2의 해당 비트 중 하나 또는 둘 모두가 0인 경우                                                  
1 - 2개 모두 해당 비트일 경우 1입니다.                                    

즉, 두 매개 변수의 해당 비트가 1일 경우를 제외하는 모든 경우에는 0을 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value1** |필수 |num |값으로 AND'ed여야 하는 숫자 값2|
| **value2** |필수 |num |값이 있는 AND'ed여야 하는 숫자 값1|

**예제:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 및 00000111 = 00000111 그래서 BitAnd 반환 7, 00000111의 바이너리 값

---
### <a name="cbool"></a>CBool
**함수:**<br> CBool (표현식)

**설명:**<br> CBool은 평가된 식에 따라 부울을 반환합니다. 식이 0이 아닌 값으로 평가하면 CBool은 True를 반환하고 다른 경우 False를 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **expression** |필수 | 식 | 유효한 식 |

**예제:**<br>
CBool([attribute1] = [속성2])                                                                    
두개의 속성이 같은 동일한 값을 가지면 True로 반환합니다.

---
### <a name="coalesce"></a>Coalesce
**함수:**<br> 합착(출처1, 소스2, ..., defaultValue)

**설명:**<br> NULL이 아닌 첫 번째 소스 값을 반환합니다. 모든 인수가 NULL이고 defaultValue가 있으면 defaultValue가 반환됩니다. 모든 인수가 NULL이고 defaultValue가 없는 경우 Coalesce는 NULL을 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **source1  … sourceN** | 필수 | String |필요한 가변 횟수입니다. 대개는 원본 개체의 특성 이름입니다. |
| **defaultValue** | 옵션 | String | 모든 소스 값이 NULL일 때 사용할 기본값입니다. 빈 문자열("")일 수 있습니다.

---
### <a name="converttobase64"></a>ConvertToBase64
**함수:**<br> 변환토베이스64(소스)

**설명:**<br> ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |기본 64로 변환할 문자열|

**예제:**<br>
ConvertToBase64("Hello world!")                                                                                                        
"SABlAGwAbABvACAAdwBvAHIAbABkACEA"를 반환합니다.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**함수:**<br> 컨버터토UTF8Hex(출처)

**설명:**<br> ConvertToUTF8Hex 함수는 문자열을 UTF8 16진수 인코딩 값으로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |UTF8 헥스로 변환할 문자열|

**예제:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
48656C6C6F20776F726C6421을 반환합니다.

---
### <a name="count"></a>개수
**함수:**<br> 카운트(특성)

**설명:**<br> Count 함수는 다중값 특성의 요소 수를 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **특성** |필수 |attribute |요소가 계산되는 다중 값 특성|

---
### <a name="cstr"></a>CStr
**함수:**<br> CStr(값)

**설명:**<br> CStr 함수는 값을 문자열 데이터 유형으로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | 숫자, 참조 또는 부울 | 숫자 값, 참조 특성 또는 부울입니다. |

**예제:**<br>
CStr([dn])                                                            
반환 "cn=조, dc=contoso, dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**함수:**<br> 날짜에서 넘(값)

**설명:**<br> DateFromNum 함수는 AD의 날짜 값 형식을 날짜/시간 형식으로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | Date | 날짜 시간 유형으로 변환할 AD 날짜 |

**예제:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
2012-01-01 23:00:00을 나타내는 날짜/시간을 반환합니다.

---
### <a name="formatdatetime"></a>FormatDateTime
**함수:**<br>  FormatDateTime(source, inputFormat, outputFormat)

**설명:**<br>  한 형식의 날짜 문자열을 다른 형식으로 변환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **inputFormat** |필수 |String |원본 값의 예상된 형식입니다. 지원되는 형식은 [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)을 참조하십시오. |
| **outputFormat** |필수 |String |출력 날짜의 형식입니다. |

---
### <a name="guid"></a>Guid
**함수:**<br> Guid()

**설명:**<br> 함수 Guid는 임의의 GUID를 새로 생성합니다.

---
### <a name="iif"></a>IIF
**함수:**<br> IIF(조건, 값IfTrue, 값IfFalse)

**설명:**<br> IIF 함수는 지정된 조건에 따라 가능한 값 집합 중 하나를 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **조건** |필수 |변수 또는 표현식 |true 또는 false로 평가될 수 있는 값 또는 식입니다. |
| **valueIfTrue** |필수 |변수 또는 문자열 | 조건이 true로 평가되는 경우 반환된 값입니다. |
| **valueIfFalse** |필수 |변수 또는 문자열 |조건이 false로 평가되는 경우 반환된 값입니다.|

**예제:**<br>
IIF([국가]="미국",[국가],[부서])

---
### <a name="instr"></a>InStr
**함수:**<br> InStr(값1, 값2, 시작, 비교유형)

**설명:**<br> InStr 함수는 문자열에서 부분 문자열이 처음 나오는 경우를 찾습니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value1** |필수 |String |검색할 문자열 |
| **value2** |필수 |String |찾을 문자열 |
| **start** |옵션 |정수 |하위 문자열을 찾기 위한 시작 위치|
| **비교 유형** |옵션 |열거형 |vbText비교 또는 vb바이너비비교될 수 있습니다. |

**예제:**<br>
InStr("The quick brown fox","quick")                                                                             
5로 계산합니다.

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
7로 계산합니다.

---
### <a name="isnull"></a>IsNull
**함수:**<br> IsNull(표현식)

**설명:**<br> 식이 Null로 계산되면 IsNull 함수는 true를 반환합니다. 특성이 없는 경우 Null로 표현됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **expression** |필수 |식 |평가할 식 |

**예제:**<br>
IsNull([displayName])                                                                                                
특성이 없는 경우 True를 반환합니다.

---
### <a name="isnullorempty"></a>이스널비어
**함수:**<br> IsNullOrEmpty(표현식)

**설명:**<br> 식이 null 또는 빈 문자열일 경우 IsNullOrEmpty 함수는 true를 반환합니다. 특성이 없거나, 빈 문자열로 존재하는 경우 True로 계산합니다.
이 함수의 역원을 IsPresnt라고 합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **expression** |필수 |식 |평가할 식 |

**예제:**<br>
IsNullOrEmpty([표시 이름])                                               
특성이 없거나 빈 문자열인 경우 True를 반환합니다.

---
### <a name="ispresent"></a>IsPresent
**함수:**<br> 현재(표현식)

**설명:**<br> 식이 Null이 아니고 비어 있지 않은 문자열로 계산되는 경우 IsPresent 함수는 true를 반환합니다. 이 함수의 역함수는 IsNullOrEmpty으로 지칭됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **expression** |필수 |식 |평가할 식 |

**예제:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**함수:**<br> IsString(표현식)

**설명:**<br> 식이 문자열 형식으로 계산될 수 있는 경우 IsString 함수는 True로 계산됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **expression** |필수 |식 |평가할 식 |

---
### <a name="item"></a>항목
**함수:**<br> 항목(특성, 인덱스)

**설명:**<br> Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **특성** |필수 |특성 |검색할 다중 값 속성 |
| **index** |필수 |정수 | 다중 값 문자열의 항목에 대한 인덱스|

**예제:**<br>
항목([프록시 주소], 1)

---
### <a name="join"></a>Join
**함수:**<br>  Join(separator, source1, source2, …)

**설명:**<br> Join()은 여러 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하면 Append()와 비슷합니다. 여기서 각 값은 **separator** 문자열로 구분됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값에 의해 구분됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **separator** |필수 |String |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
| **source1  … sourceN** |필수, 시간 변수 |String |값이 함께 조인될 문자열입니다. |

---
### <a name="left"></a>Left
**함수:**<br> 왼쪽(문자열, 눔차르)

**설명:**<br> Left 함수는 문자열 왼쪽부터 지정된 수의 문자를 반환합니다. numChars = 0 인 경우, 빈 문자열을 반환합니다.
numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
문자열이 null이면 빈 문자열을 반환합니다.
문자열이 numChars 내에서 숫자가 지정한 문자보다 적은 문자를 포함하는 경우, 문자열과 동일한 문자열(즉, 매개 변수 1의 모든 문자가 포함)이 반환됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **String** |필수 |특성 | 에서 문자를 반환하는 문자열 |
| **NumChars** |필수 |정수 | 문자열의 시작 부분(왼쪽)에서 반환할 문자 수를 식별하는 숫자|

**예제:**<br>
왼쪽("존 도", 3)                                                            
"Joh"를 반환합니다.

---
### <a name="mid"></a>Mid
**함수:**<br>  Mid(source, start, length)

**설명:**<br>  원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |일반적으로 특성 이름입니다. |
| **start** |필수 |integer |하위 문자열이 시작되어야 하는 **source** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
| **length** |필수 |integer |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 부분 문자열을 반환합니다. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**함수:**<br> NormalizeDiacritics(원본)

**설명:**<br> 하나의 문자열 인수가 필요합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다. 일반적으로 분음 부호 문자(악센트 부호)를 포함하는 이름 및 성을 다양한 사용자 ID(예: 사용자 계정 이름, SAM 계정 이름 및 이메일 주소)에 사용할 수 있는 올바른 값으로 변환하는 데 사용됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String | 일반적으로 이름 또는 성 특성입니다. |

---
### <a name="not"></a>Not
**함수:**<br>  Not(source)

**설명:**<br> **source**의 부울 값을 대칭 이동합니다. **source** 값이 "*True*"이면 "*False*"가 반환됩니다. False인 경우에는 "*True*"가 반환됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |부울 문자열 |필요한 **source** 값은 "True"나 "False"입니다. |

---
### <a name="numfromdate"></a>NumFromDate
**함수:**<br> 숫자FromDate(값)

**설명:**<br> NumFromDate 함수는 DateTime 값을 계정만료와 같은 특성을 설정하는 데 필요한 Active Directory 형식으로 [변환합니다.](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires) 이 함수를 사용하여 Workday 및 SuccessFactors와 같은 클라우드 HR 앱에서 받은 DateTime 값을 해당 AD 표현으로 변환합니다. 

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | String | 지원되는 형식의 날짜 시간 문자열입니다. 지원되는 형식은 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx를 참조하세요. |

**예제:**<br>
* 근무일 예 <br>
  AD의 *accountExpires* 필드에 *2020-12-31-08:00* 형식의 *Workday에서 계약종료* 특성을 매핑하려는 경우 이 함수를 사용하고 로캘과 일치하도록 시간대 오프셋을 변경하는 방법은 다음과 같습니다. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* 성공 요인 예제 <br>
  SuccessFactors에서 속성 *endDate를* 매핑하려는 경우 AD의 *계정 만료* 필드에 *M/d/yyyy hh:mm:ss tt* 형식인 경우 이 함수를 사용하고 로캘과 일치하도록 표준 시간대 오프셋을 변경하는 방법은 다음과 같습니다.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**함수:**<br> 중복 제거(특성)

**설명:**<br> RemoveDuplicates 함수는 다중값 문자열을 사용하여 개별 값을 고유하게 만듭니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **특성** |필수 |다중 값 속성 |중복이 제거된 다중 값 특성|

**예제:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
모든 중복 값이 제거된 삭제된 proxyAddress 특성을 반환합니다.

---
### <a name="replace"></a>Replace
**함수:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:**<br>
 문자열 내 값을 대체합니다. 제공된 매개 변수에 따라 다르게 작동합니다.

* **oldValue** 및 **replacementValue**가 제공된 경우:
  
  * **원본의** **oldValue의** 모든 발생을 **대체값**
* **oldValue** 및 **template**가 제공된 경우:
  
  * **template**의 모든 **oldValue**를 **source** 값으로 바꿉니다.
* **regexPattern** 및 **replacementValue**가 제공되는 경우:

  * 함수에서 **regexPattern**을 **source** 문자열에 적용하고, regex 그룹 이름을 사용하여 **replacementValue**에 대한 문자열을 생성할 수 있습니다.
* **regexPattern**, **regexGroupName**, **replacementValue**가 제공되는 경우:
  
  * 함수에서 **regexPattern**을 **source** 문자열에 적용하고, **regexGroupName**과 일치하는 모든 값을 **replacementValue**로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementAttributeName**이 제공되는 경우:
  
  * **source**에 값이 없는 경우 **source**가 반환됩니다.
  * **source**에 값이 있는 경우 함수에서 **regexPattern**을 **source** 문자열에 적용하고, **regexGroupName**과 일치하는 모든 값을 **replacementAttributeName**과 연결된 값으로 바꿉니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |일반적으로 **소스** 개체에서 특성의 이름입니다. |
| **oldValue** |옵션 |String |**원본** 또는 **템플릿**에서 대체될 값입니다. |
| **regexPattern** |옵션 |String |**source**에서 바뀔, 값의 Regex 패턴입니다. 또는 **replacementPropertyName**을 사용하는 경우 **replacementPropertyName**에서 값을 추출하는 패턴입니다. |
| **regexGroupName** |옵션 |String |**regexPattern**내 그룹의 이름입니다. **replacementPropertyName**을 사용하는 경우에만 **replacementPropertyName**에서 이 그룹의 값을 **replacementValue**로 추출합니다. |
| **replacementValue** |옵션 |String |이전 값과 대체할 새로운 값입니다. |
| **replacementAttributeName** |옵션 |String |대체 값에 사용할 특성의 이름입니다. |
| **템플릿** |옵션 |String |**템플릿** 값이 제공되면 템플릿 내부의 **oldValue를** 찾고 **소스** 값으로 바꿉니다. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**함수:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**설명:**<br> 최소 두 개의 인수가 필요하며, 이는 표현식을 사용하여 정의된 고유한 값 생성 규칙입니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다. 발견된 첫 번째 고유한 값이 반환됩니다. 모든 값이 이미 대상에 있는 경우 항목은 위탁되고 감사 로그에 이유가 기록됩니다. 제공할 수 있는 인수 수에 상한은 없습니다.


 - 최상위 레벨 함수이므로 중첩할 수 없습니다.
 - 우선 순위가 일치하는 특성에는 이 함수를 적용할 수 없습니다.   
 - 이 함수는 항목 만들기에만 사용할 수 있습니다. 특성과 함께 사용할 경우 **매핑 적용** 속성을 **개체를 만드는 동안만**으로 설정합니다.
 - 이 함수는 현재 "Workday에서 Active Directory로의 사용자 프로비저닝"에만 지원되며, 다른 프로비저닝 애플리케이션에서는 사용할 수 없습니다. 


**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2개 이상 필요, 상한 없음 |String | 평가할 고유한 값 생성 규칙 목록입니다. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:**<br> SingleAppRoleAssignment([appRoleAssignments])

**설명:**<br> 지정된 애플리케이션에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다. 

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |String |**[appRoleAssignments]** 개체. |

---
### <a name="split"></a>분할
**함수:**<br> Split(원본, 구분 기호)

**설명:**<br> 지정된 구분 기호 문자를 사용하여 문자열을 다중 값 배열로 분할합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |업데이트할 **source** 값입니다. |
| **구분 기호** |필수 |String |문자열을 분할하는 데 사용할 문자(예: ",")를 지정 |

---
### <a name="stripspaces"></a>StripSpaces
**함수:**<br>  StripSpaces(source)

**설명:**<br>  원본 문자열에서 모든 공백(" ")을 제거합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |업데이트할 **source** 값입니다. |

---
### <a name="switch"></a>스위치
**함수:**<br>  Switch(source, defaultValue, key1, value1, key2, value2, …)

**설명:**<br> **source** 값이 **key**와 일치할 경우 해당 **key**에 대한 **value**를 반환합니다. **source** 값이 아무 키와도 일치하지 않는 경우 **defaultValue**를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다. 이 함수는 manager와 같은 참조 특성에 사용해서는 안 됩니다. 

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |**Source** 값입니다. |
| **defaultValue** |옵션 |String |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
| **key** |필수 |String |**원본** 값과 비교할 **Key**입니다. |
| **value** |필수 |String |키와 일치하는 **원본** 의 대체 값입니다. |

---
### <a name="tolower"></a>ToLower
**함수:**<br> ToLower(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 소문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |대개는 원본 개체의 특성 이름 |
| **문화권** |옵션 |String |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---
### <a name="toupper"></a>ToUpper
**함수:**<br> ToUpper(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 대문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **소스** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **문화권** |옵션 |String |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---
### <a name="word"></a>Word
**함수:**<br> 단어(문자열, 워드 넘버, 구분 기호)

**설명:**<br> Word 함수는 사용할 구분 기호를 설명하는 매개 변수에 따라 문자열 내에 포함된 단어와 반환할 단어 수를 반환합니다. 구분 기호 내의 문자 중 하나로 구분되는 전체 문자열의 각 문자열은 단어로 식별됩니다.

숫자가 < 1인경우 , 빈 문자열을 반환합니다.
문자열이 null이면, 빈 문자열을 반환합니다.
문자열이 단어 수보다 적거나, 구분 기호로 식별되는 단어를 포함할 경우, 빈 문자열이 반환됩니다.

**매개 변수:**<br> 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **String** |필수 |다중 값 속성 |에서 단어를 반환 하는 문자열입니다.|
| **워드 넘버** |필수 | 정수 | 반환할 단어 번호를 식별하는 번호|
| **구분 기호** |필수 |String| 단어를 식별하는 데 사용해야 하는 구분 기호를 나타내는 문자열|

**예제:**<br>
단어 ("빠른 갈색 여우",3,""                                                                                       
"brown"을 반환합니다.

단어 ("이,문자열!&많은 구분 기호",3,",,,& #")                                                                       
반품 "있다"

---

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
* **출력**:John.Doe@contoso.com.test" "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성해야 합니다.

**식:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** <br>

* **입력** (givenName): "John"
* **입력** (surname): "Doe"
* **출력**: "조도"

### <a name="remove-diacritics-from-a-string"></a>문자열에서 분음 부호 제거
악센트 기호를 포함하지 않는 해당 문자로 악센트 기호를 포함하는 문자를 바꿔야 합니다.

**식:** <br>
NormalizeDiacritics([givenName])

**샘플 입/출력:** <br>

* **입력** (givenName): "Zoë"
* **출력**: "조이"

### <a name="split-a-string-into-a-multi-valued-array"></a>문자열을 다중 값 배열로 분할
문자열의 쉼표로 구분된 목록을 가져와 Salesforce의 PermissionSets 특성과 같은 다중 값 특성에 연결할 수 있는 배열로 분할해야 합니다. 이 예의 경우 Azure AD에서 사용 권한 세트의 목록을 extensionAttribute5에 채웠습니다.

**식:** <br>
Split([extensionAttribute5], ",")

**샘플 입/출력:** <br>

* **입력** (확장속성5): "권한 집합, 퍼미션셋2"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
SaaS 애플리케이션에 특정 형식의 날짜를 전송하려고 합니다. <br>
 예를 들어 ServiceNow에 대한 날짜 형식을 지정하려고 할 수 있습니다.

**식:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력** (extensionAttribute1): "20150123105347.1Z"
* **출력**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기

Azure AD에 저장된 상태 코드를 기반으로 사용자의 시간대를 정의해야 합니다. <br>
 상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력** (상태): "QLD"
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

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **출력**:john.smith@contoso.com" "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UPN(userPrincipalName) 특성의 고유한 값 생성
사용자의 이름, 중간 이름, 성에 따라, UPN 특성에 값을 할당하려면 먼저 해당 UPN 특성에 대해 값을 생성하고 대상 AD 디렉터리에서 해당 값이 고유한지 확인해야 합니다.

**식:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**샘플 입/출력:**

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **OUTPUT**:John.Smith@contoso.com" " UPN 값이 John.Smith@contoso.com 디렉토리에 이미 없는 경우
* **OUTPUT**:J.Smith@contoso.com" " 디렉터리에 John.Smith@contoso.com UPN 값이 이미 있는 경우
* **OUTPUT**:Jo.Smith@contoso.com" " 위의 두 UPN 값이 디렉토리에 이미 있는 경우

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>NULL이 아닌 경우 흐름 메일 값, 그렇지 않으면 흐름 사용자PrincipalName
메일 특성이 있는 경우 흐름합니다. 그렇지 않은 경우 대신 userPrincipalName 값을 흐름하려고 합니다.

**식:** <br>
`Coalesce([mail],[userPrincipalName])`

**샘플 입/출력:** <br>

* **입력** (메일): NULL
* **입력(사용자** 대표이름):John.Doe@contoso.com"
* **출력**:John.Doe@contoso.com" "

## <a name="related-articles"></a>관련 문서
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](../app-provisioning/user-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [계정 프로비저닝 알림](../app-provisioning/user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)

---
title: UI 정의 함수 만들기
description: Azure Managed Applications에 대한 UI 정의를 생성할 때 사용하는 함수에 대해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82980816"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition 함수
이 섹션에는 CreateUiDefinition의 지원되는 모든 함수에 대한 서명이 포함되어 있습니다.

함수를 사용 하려면 호출을 대괄호로 묶습니다. 예를 들어:

```json
"[function()]"
```

문자열 및 기타 함수를 함수에 대한 매개 변수로 참조할 수 있지만 문자열을 따옴표로 묶어야 합니다. 예를 들어:

```json
"[fn1(fn2(), 'foobar')]"
```

해당하는 경우 점 연산자를 사용하여 함수 출력의 속성을 참조할 수 있습니다. 예를 들어:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>참조 함수
이러한 함수를 사용하여 CreateUiDefinition의 속성 또는 컨텍스트 출력을 참조할 수 있습니다.

### <a name="basics"></a>기본 사항
기본 사항 단계에서 정의된 요소의 출력 값을 반환합니다.

다음 예제에서는 기본 사항 단계의 `foo` 요소 출력을 반환합니다.

```json
"[basics('foo')]"
```

### <a name="steps"></a>단계
지정된 단계에서 정의된 요소의 출력 값을 반환합니다. 기본 사항 단계의 요소 출력 값을 가져오려면 `basics()`를 대신 사용합니다.

다음 예제에서는 `foo` 단계의 `bar` 요소 출력을 반환합니다.

```json
"[steps('foo').bar]"
```

### <a name="location"></a>위치
기본 사항 단계 또는 현재 컨텍스트에서 선택한 위치를 반환합니다.

다음 예제에서는 `"westus"`를 반환할 수 있습니다.

```json
"[location()]"
```

## <a name="string-functions"></a>문자열 함수
이러한 함수는 JSON 문자열하고만 사용할 수 있습니다.

### <a name="concat"></a>concat
하나 이상의 문자열을 연결합니다.

예를 들어 `element1`의 출력 값이 `"bar"`인 경우 이 예제에서는 `"foobar!"` 문자열을 반환합니다.

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
지정된 문자열의 부분 문자열을 반환합니다. 부분 문자열은 지정된 인덱스에서 시작하고 지정된 길이를 갖습니다.

다음 예제는 `"ftw"`을 반환합니다.

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
현재 문자열에서 지정된 문자열의 모든 항목이 다른 문자열로 바뀐 문자열을 반환합니다.

다음 예제는 `"Everything is awesome!"`을 반환합니다.

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
전역적으로 고유한 문자열(GUID)을 생성합니다.

다음 예제에서는 `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`를 반환할 수 있습니다.

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
소문자로 변환된 문자열을 반환합니다.

다음 예제는 `"foobar"`을 반환합니다.

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
대문자로 변환된 문자열을 반환합니다.

다음 예제는 `"FOOBAR"`을 반환합니다.

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>컬렉션 함수
이러한 함수는 JSON 문자열, 배열, 개체 등의 컬렉션과 함께 사용할 수 있습니다.

### <a name="contains"></a>포함
문자열에 지정된 부분 문자열이 포함되어 있거나, 배열에 지정된 값이 포함되어 있거나, 개체에 지정된 키가 포함되어 있으면 `true`를 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `true`을 반환합니다.

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `false`을 반환합니다.

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

다음 예제는 `true`을 반환합니다.

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
문자열의 문자 수, 배열의 값 수 또는 개체의 키 수를 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `6`을 반환합니다.

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `3`을 반환합니다.

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

다음 예제는 `2`을 반환합니다.

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
문자열, 배열 또는 개체가 null이거나 비어 있으면 `true`를 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `true`을 반환합니다.

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `false`을 반환합니다.

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

다음 예제는 `false`을 반환합니다.

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>예제 4: null 및 undefined
`element1`이 `null` 또는 undefined라고 가정합니다. 다음 예제는 `true`을 반환합니다.

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
지정된 문자열의 첫 번째 문자, 지정된 배열의 첫 번째 값 또는 지정된 개체의 첫 번째 키와 값을 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `"f"`을 반환합니다.

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `1`을 반환합니다.

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
다음 예제는 `{"key1": "foobar"}`을 반환합니다.

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
지정된 문자열의 마지막 문자, 지정된 배열의 마지막 값 또는 지정된 개체의 마지막 키와 값을 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `"r"`을 반환합니다.

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `2`을 반환합니다.

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

다음 예제는 `{"key2": "raboof"}`을 반환합니다.

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
문자열의 시작 부분부터 지정된 개수의 연속 문자, 배열의 시작 부분부터 지정된 개수의 연속 값 또는 개체의 시작 부분부터 지정된 개수의 연속 키와 값을 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `"foo"`을 반환합니다.

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `[1, 2]`을 반환합니다.

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

다음 예제는 `{"key1": "foobar"}`을 반환합니다.

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
컬렉션에서 지정된 개수의 요소를 건너뛴 다음 나머지 요소를 반환합니다.

#### <a name="example-1-string"></a>예제 1: 문자열
다음 예제는 `"bar"`을 반환합니다.

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>예제 2: 배열
`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `[3]`을 반환합니다.

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>예제 3: 개체
`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
다음 예제는 `{"key2": "raboof"}`을 반환합니다.

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>논리 함수
조건문에서 이러한 함수를 사용할 수 있습니다. 일부 함수는 모든 JSON 데이터 형식을 지원하지 않을 수 있습니다.

### <a name="equals"></a>equals
두 매개 변수의 형식과 값이 같으면 `true`를 반환합니다. 이 함수는 모든 JSON 데이터 형식을 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[equals(0, 0)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[equals('foo', 'foo')]"
```

다음 예제는 `false`을 반환합니다.

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 작으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[less(1, 2)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
첫 번째 매개 변수가 두 번째 매개 변수보다 작거나 같으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 크면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `false`을 반환합니다.

```json
"[greater(1, 2)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
첫 번째 매개 변수가 두 번째 매개 변수보다 크거나 같으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>및
모든 매개 변수가 `true`로 평가되면 `true`를 반환합니다. 이 함수는 두 개 이상의 부울 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

다음 예제는 `false`을 반환합니다.

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>또는
매개 변수 중 하나 이상이 `true`로 평가되면 `true`를 반환합니다. 이 함수는 두 개 이상의 부울 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

다음 예제는 `true`을 반환합니다.

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
매개 변수가 `false`로 평가되면 `true`를 반환합니다. 이 함수는 부울 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[not(false)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
null이 아닌 첫 번째 매개 변수의 값을 반환합니다. 이 함수는 모든 JSON 데이터 형식을 지원합니다.

`element1` 및 `element2`를 undefined로 가정합니다. 다음 예제는 `"foobar"`을 반환합니다.

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

이 함수는 페이지를 로드 한 후 사용자 작업으로 인해 발생 하는 선택적 호출의 컨텍스트에서 특히 유용 합니다. UI의 한 필드에 적용 되는 제약 조건이 처음에는 **표시 되지 않는** 다른 필드의 현재 선택 된 값에 따라 달라 지는 경우를 예로 들 수 있습니다. 이 경우를 `coalesce()` 사용 하 여 사용자가 필드와 상호 작용할 때 원하는 효과를 얻을 수 있는 동안 페이지 로드 시 함수를 구문상 유효 하 게 지정할 수 있습니다.

이를 고려 하 `DropDown` 여 사용자는 여러 가지 다른 데이터베이스 유형 중에서 선택할 수 있습니다.

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

이 필드의 현재 선택 된 값에 다른 필드의 동작을 표시 하려면 다음과 같이 `coalesce()` 를 사용 합니다.

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

이는 처음에는 표시 되지 않으므로 값이 없기 때문에 필요 `databaseType` 합니다. 이로 인해 전체 식이 올바르게 계산 되지 않습니다.

## <a name="conversion-functions"></a>변환 함수
이러한 함수를 사용하여 JSON 데이터 형식 및 인코딩 간에 값을 변환할 수 있습니다.

### <a name="int"></a>int
매개 변수를 정수로 변환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수를 지원합니다.

다음 예제는 `1`을 반환합니다.

```json
"[int('1')]"
```

다음 예제는 `2`을 반환합니다.

```json
"[int(2.9)]"
```

### <a name="float"></a>float
매개 변수를 부동 소수점으로 변환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수를 지원합니다.

다음 예제는 `1.0`을 반환합니다.

```json
"[float('1.0')]"
```

다음 예제는 `2.9`을 반환합니다.

```json
"[float(2.9)]"
```

### <a name="string"></a>string
매개 변수를 문자열로 변환합니다. 이 함수는 모든 JSON 데이터 형식의 매개 변수를 지원합니다.

다음 예제는 `"1"`을 반환합니다.

```json
"[string(1)]"
```

다음 예제는 `"2.9"`을 반환합니다.

```json
"[string(2.9)]"
```

다음 예제는 `"[1,2,3]"`을 반환합니다.

```json
"[string([1,2,3])]"
```

다음 예제는 `"{"foo":"bar"}"`을 반환합니다.

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
매개 변수를 부울로 변환합니다. 이 함수는 숫자, 문자열 및 부울 형식의 매개 변수를 지원합니다. JavaScript의 부울과 마찬가지로 `0` 또는 `'false'` 이외의 모든 값은 `true`를 반환합니다.

다음 예제는 `true`을 반환합니다.

```json
"[bool(1)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[bool(0)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[bool(true)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
매개 변수를 네이티브 형식으로 변환합니다. 즉, 이 함수는 `string()`의 역함수입니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `1`을 반환합니다.

```json
"[parse('1')]"
```

다음 예제는 `true`을 반환합니다.

```json
"[parse('true')]"
```

다음 예제는 `[1,2,3]`을 반환합니다.

```json
"[parse('[1,2,3]')]"
```

다음 예제는 `{"foo":"bar"}`을 반환합니다.

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
매개 변수를 base-64로 인코드된 문자열로 인코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"Zm9vYmFy"`을 반환합니다.

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
매개 변수를 base-64로 인코드된 문자열에서 디코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"foobar"`을 반환합니다.

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
매개 변수를 URL로 인코드된 문자열로 인코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"https%3A%2F%2Fportal.azure.com%2F"`을 반환합니다.

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
매개 변수를 URL로 인코드된 문자열에서 디코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"https://portal.azure.com/"`을 반환합니다.

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>수치 연산 함수
### <a name="add"></a>add
두 숫자를 더한 결과를 반환합니다.

다음 예제는 `3`을 반환합니다.

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다.

다음 예제는 `1`을 반환합니다.

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
두 숫자를 곱한 결과를 반환합니다.

다음 예제는 `6`을 반환합니다.

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
첫 번째 숫자를 두 번째 숫자로 나눈 결과를 반환합니다. 결과는 항상 정수입니다.

다음 예제는 `2`을 반환합니다.

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
첫 번째 숫자를 두 번째 숫자로 나눈 나머지를 반환합니다.

다음 예제는 `0`을 반환합니다.

```json
"[mod(6, 3)]"
```

다음 예제는 `2`을 반환합니다.

```json
"[mod(6, 4)]"
```

### <a name="min"></a>분
두 숫자 중 작은 숫자를 반환합니다.

다음 예제는 `1`을 반환합니다.

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
두 숫자 중 큰 숫자를 반환합니다.

다음 예제는 `2`을 반환합니다.

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
지정된 범위 내의 정수 시퀀스를 생성합니다.

다음 예제는 `[1,2,3]`을 반환합니다.

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
지정된 범위 내의 임의 정수를 반환합니다. 이 함수는 암호화하여 보안 설정된 난수를 생성하지 않습니다.

다음 예제에서는 `42`를 반환할 수 있습니다.

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
지정한 숫자보다 작거나 같은 가장 큰 정수를 반환합니다.

다음 예제는 `3`을 반환합니다.

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
지정한 숫자보다 크거나 같은 가장 큰 정수를 반환합니다.

다음 예제는 `4`을 반환합니다.

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>날짜 함수
### <a name="utcnow"></a>utcNow
로컬 컴퓨터의 현재 날짜 및 시간 문자열을 ISO 8601 형식으로 반환합니다.

다음 예제에서는 `"1990-12-31T23:59:59.000Z"`를 반환할 수 있습니다.

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
지정된 타임스탬프에 시간(초)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:00:00.000Z"`을 반환합니다.

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
지정된 타임스탬프에 시간(분)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:00:59.000Z"`을 반환합니다.

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
지정된 타임스탬프에 시간(시)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:59:59.000Z"`을 반환합니다.

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.


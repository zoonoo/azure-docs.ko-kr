---
title: Bicep 논리 연산자
description: 조건을 평가하는 Bicep 논리 연산자에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353556"
---
# <a name="bicep-logical-operators"></a>Bicep 논리 연산자

논리 연산자는 부울 값을 평가하거나, null이 아닌 값을 반환하거나, 조건식을 평가합니다. 예를 실행하려면 Azure CLI 또는 Azure PowerShell을 사용하여 [Bicep 파일을 배포](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)합니다.

| 연산자 | Name |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Or](#or-) |
| `!` | [Not](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [조건 식](#conditional-expression--) |

## <a name="and-"></a>And &&

`operand1 && operand2`

두 값이 모두 true인지 확인합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | true인지 확인할 첫 번째 값입니다. |
| `operand2` | boolean | true인지 확인할 두 번째 값입니다. |
| 추가 피연산자 | boolean | 피연산자를 더 많이 포함할 수 있습니다. |

### <a name="return-value"></a>반환 값

두 값이 모두 true이면 `True`가 반환되고 그렇지 않으면 `false`가 반환됩니다.

### <a name="example"></a>예제

매개 변수 값 집합과 식 집합을 평가합니다.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

예의 출력:

| 이름 | Type | 값 |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Or ||

`operand1 || operand2`

두 값 중 하나가 true인지 확인합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | true인지 확인할 첫 번째 값입니다. |
| `operand2` | boolean | true인지 확인할 두 번째 값입니다. |
| 추가 피연산자 | boolean | 피연산자를 더 많이 포함할 수 있습니다. |

### <a name="return-value"></a>반환 값

두 값 중 하나가 true이면 `True`가 반환하고 그렇지 않으면 `false`가 반환됩니다.

### <a name="example"></a>예제

매개 변수 값 집합과 식 집합을 평가합니다.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

예의 출력:

| 이름 | Type | 값 |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Not !

`!boolValue`

부울 값을 부정합니다.

### <a name="operand"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | 부정된 부울 값입니다. |

### <a name="return-value"></a>반환 값

초기 값을 부정하고 부울 값을 반환합니다. 초기 값이 `true`이면 `false`가 반환됩니다.

### <a name="example"></a>예제

`not` 연산자는 값을 부정합니다. 값이 괄호로 묶일 수 있습니다.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

예의 출력:

| 이름 | Type | 값 |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Coalesce ??

`operand1 ?? operand2`

피연산자에서 null이 아닌 첫 번째 값을 반환합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 문자열, 정수, 부울, 개체, 배열 | `null`에 대한 테스트 값입니다. |
| `operand2` | 문자열, 정수, 부울, 개체, 배열 | `null`에 대한 테스트 값입니다. |
| 추가 피연산자 | 문자열, 정수, 부울, 개체, 배열 | `null`에 대한 테스트 값입니다. |

### <a name="return-value"></a>반환 값

null이 아닌 첫 번째 값을 반환합니다. 빈 문자열, 빈 배열 및 빈 개체는 `null`이 아니며 \<empty> 값이 반환됩니다.

### <a name="example"></a>예제

출력 문은 null이 아닌 값을 반환합니다. 출력 형식은 비교 형식과 일치해야 합니다. 그렇지 않으면 오류가 생성됩니다.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

예의 출력:

| 이름 | Type | 값 |
| ---- | ---- | ---- |
| `nonNullStr` | 문자열 | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | 문자열 | \<empty> |

## <a name="conditional-expression--"></a>조건 식? :

`condition ? true-value : false-value`

조건을 평가하고 조건이 true 또는 false인지 여부를 나타내는 값을 반환합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `condition` | boolean | true 또는 false로 평가되는 조건입니다. |
| `true-value` | 문자열, 정수, 부울, 개체, 배열 | 조건이 true인 경우의 값입니다. |
| `false-value` | 문자열, 정수, 부울, 개체, 배열 | 조건이 false인 경우의 값입니다. |

### <a name="example"></a>예제

이 예에서는 매개 변수의 초기값을 평가하고 조건이 true 또는 false인지 여부를 나타내는 값을 반환합니다.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

예의 출력:

| 이름 | Type | 값 |
| ---- | ---- | ---- |
| `outValue` | 문자열 | true 값 |

## <a name="next-steps"></a>다음 단계

- Bicep 파일을 만들려면 [자습서: 첫 번째 Azure Resource Manager Bicep 파일 만들기 및 배포](bicep-tutorial-create-first-bicep.md)를 참조하세요.
- Bicep 유형 오류를 해결하는 방법에 대한 정보는 [Bicep의 모든 기능](template-functions-any.md)을 참조하세요.
- Bicep 및 JSON 구문을 비교하려면 [템플릿에 대한 JSON 및 Bicep 비교](compare-template-syntax.md)를 참조하세요.
- Bicep 및 ARM 템플릿 함수의 예는 [ARM 템플릿 함수](template-functions.md)를 참조하세요.

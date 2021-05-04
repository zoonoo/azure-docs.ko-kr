---
title: Bicep 숫자 연산자
description: 값을 계산하는 Bicep 숫자 연산자에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211253"
---
# <a name="bicep-numeric-operators"></a>Bicep 숫자 연산자

숫자 연산자는 정수를 사용하여 계산을 수행하고 정수 값을 반환합니다. 예제를 실행하려면 Azure CLI 또는 Azure PowerShell을 사용하여 [Bicep 파일을 배포](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)합니다.

| 연산자 | 이름 |
| ---- | ---- |
| `*` | [곱하기](#multiply-) |
| `/` | [나누기](#divide-) |
| `%` | [나머지값](#modulo-) |
| `+` | [추가](#add-) |
| `-` | [빼기](#subtract--) |
| `-` | [빼기](#minus--) |

> [!NOTE]
> 빼기(subtract) 및 빼기(minus)는 동일한 연산자를 사용합니다. 빼기(subtract)는 두 개의 피연산자를 사용하고 빼기(minus)는 피연산자 하나를 사용하기 때문에 기능이 서로 다릅니다.

## <a name="multiply-"></a>곱하기 *

`operand1 * operand2`

두 정수를 곱합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1`  | integer | 곱할 숫자입니다. |
| `operand2`  | integer  | 숫자의 승수입니다. |

### <a name="return-value"></a>반환 값

곱셈은 곱을 정수로 반환합니다.

### <a name="example"></a>예제

두 정수를 곱하고 곱을 반환합니다.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `product` | integer | 10 |

## <a name="divide-"></a>나누기 /

`operand1 / operand2`

정수를 정수로 나눕니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | integer | 나눈 정수입니다. |
| `operand2` | integer | 나누기에 사용되는 정수입니다. 0일 수 없습니다. |

### <a name="return-value"></a>반환 값

나누기는 몫을 정수로 반환합니다.

### <a name="example"></a>예제

두 정수를 나누고 몫을 반환합니다.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `quotient` | integer | 5 |

## <a name="modulo-"></a>Modulo %

`operand1 % operand2`

정수를 정수로 나누고 나머지를 반환합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1`  | integer  | 나누기한 정수입니다. |
| `operand2`  | integer  | 나누기에 사용되는 정수입니다. 0일 수 없습니다. |

### <a name="return-value"></a>반환 값

나머지는 정수로 반환됩니다. 나누기가 나머지를 생성하지 않으면 0이 반환됩니다.

### <a name="example"></a>예제

두 쌍의 정수를 나누고 나머지를 반환합니다.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | integer | 0 |

## <a name="add-"></a>덧셈(add) +

`operand1 + operand2`

두 개의 정수를 추가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | integer | 더할 숫자입니다. |
| `operand2` | integer | 숫자에 더하는 숫자입니다. |

### <a name="return-value"></a>반환 값

덧셈은 합계를 정수로 반환합니다.

### <a name="example"></a>예제

두 정수를 더하고 합계를 반환합니다.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `sum` | integer | 12 |

## <a name="subtract--"></a>빼기(subtract) -

`operand1 - operand2`

정수에서 정수를 뺍니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | integer | 뺀 더 큰 숫자입니다. |
| `operand2` | integer | 더 큰 숫자에서 뺀 숫자입니다. |

### <a name="return-value"></a>반환 값

빼기는 차이를 정수로 반환합니다.

### <a name="example"></a>예제

정수를 빼고 차이를 반환합니다.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `difference` | integer | 6 |

## <a name="minus--"></a>빼기(minus) -

`-integerValue`

정수에 `-1`을 곱합니다.

### <a name="operand"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `integerValue` | integer | `-1`을 곱한 정수입니다. |

### <a name="return-value"></a>반환 값

`-1`을 곱한 정수입니다. 양의 정수는 음의 정수를 반환하고 음의 정수는 양의 정수를 반환합니다. 값은 괄호로 둘러쌀 수 있습니다.

### <a name="example"></a>예제

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

예제의 출력:

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `startedPositive` | integer | -10 |
| `startedNegative` | integer | 20 |

## <a name="next-steps"></a>다음 단계

- Bicep 파일을 만들려면 [자습서: 첫 번째 Azure Resource Manager Bicep 파일 만들기 및 배포](bicep-tutorial-create-first-bicep.md)를 참조하세요.
- Bicep 형식 오류를 해결하는 방법에 대한 자세한 내용은 [Bicep에 대한 함수](template-functions-any.md)를 참조하세요.
- Bicep 및 JSON에 대한 구문을 비교하려면 [템플릿에 대한 JSON 및 Bicep 비교](compare-template-syntax.md)를 참조하세요.
- Bicep 및 ARM 템플릿 함수의 예는 [ARM 템플릿 함수](template-functions.md)를 참조하세요.

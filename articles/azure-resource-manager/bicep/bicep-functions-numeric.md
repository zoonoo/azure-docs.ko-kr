---
title: Bicep 함수 - 숫자
description: 숫자 작업을 위해 Bicep 파일에서 사용할 함수를 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 2eec27d0317b6cfae4969244d069be6881566942
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027111"
---
# <a name="numeric-functions-for-bicep"></a>Bicep에 대한 숫자 함수

Bicep 파일에서 Resource Manager는 문자열 작업을 위한 다음 함수를 제공합니다.

* [int](#int)
* [max](#max)
* [min](#min)

Azure Resource Manager JSON 숫자 함수 중 일부는 [Bicep 숫자 연산자](./operators-numeric.md)로 바뀝니다.

## <a name="int"></a>int

`int(valueToConvert)`

지정된 값을 정수로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 |문자열 또는 int |정수로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 정수입니다.

### <a name="example"></a>예제

다음 예제는 사용자가 제공한 매개 변수 값을 정수로 변환합니다.

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최대값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최대값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

컬렉션의 최대값을 나타내는 정수입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 정소 목록에 max를 사용하는 방법을 보여 줍니다.

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>분

`min (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최소값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최소값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

컬렉션의 최소값을 나타내는 정수입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 정소 목록에 min을 사용하는 방법을 보여 줍니다.

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="next-steps"></a>다음 단계

* Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.

---
title: UI 정의 컬렉션 함수 만들기
description: 배열 및 개체와 같은 컬렉션으로 작업할 때 사용할 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098199"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition 컬렉션 함수

이러한 함수는 JSON 문자열, 배열, 개체 등의 컬렉션과 함께 사용할 수 있습니다.

## <a name="contains"></a>contains

문자열에 지정된 부분 문자열이 포함되어 있거나, 배열에 지정된 값이 포함되어 있거나, 개체에 지정된 키가 포함되어 있으면 `true`를 반환합니다.

### <a name="example-string-contains"></a>예: 문자열 포함

다음 예제는 `true`을 반환합니다.

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>예: 배열 contains

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `false`을 반환합니다.

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>예: 개체 contains

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `true`을 반환합니다.

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

문자열, 배열 또는 개체가 null이거나 비어 있으면 `true`를 반환합니다.

### <a name="example-string-empty"></a>예: 문자열이 비어 있음

다음 예제는 `true`을 반환합니다.

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>예: 배열이 비어 있습니다.

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `false`을 반환합니다.

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>예: object empty

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `false`을 반환합니다.

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>예: null 및 undefined

`element1`이 `null` 또는 undefined라고 가정합니다. 다음 예제는 `true`을 반환합니다.

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

람다 함수로 제공 된 필터링 논리를 적용 한 후 새 배열을 반환 합니다. 첫 번째 매개 변수는 필터링에 사용할 배열입니다. 두 번째 매개 변수는 필터링 논리를 지정 하는 람다 함수입니다.

다음 샘플에서는 배열을 반환 합니다 `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

지정된 문자열의 첫 번째 문자, 지정된 배열의 첫 번째 값 또는 지정된 개체의 첫 번째 키와 값을 반환합니다.

### <a name="example-string-first"></a>예: string first

다음 예제는 `"c"`을 반환합니다.

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>예: array first

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `1`을 반환합니다.

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>예: object first

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `{"key1": "Linux"}`을 반환합니다.

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

지정된 문자열의 마지막 문자, 지정된 배열의 마지막 값 또는 지정된 개체의 마지막 키와 값을 반환합니다.

### <a name="example-string-last"></a>예: last 문자열

다음 예제는 `"o"`을 반환합니다.

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>예: 마지막 배열

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `3`을 반환합니다.

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>예: last 개체

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `{"key2": "Windows"}`을 반환합니다.

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

문자열의 문자 수, 배열의 값 수 또는 개체의 키 수를 반환합니다.

### <a name="example-string-length"></a>예: 문자열 길이

다음 예제는 `7`을 반환합니다.

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>예: 배열 길이

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `3`을 반환합니다.

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>예: 개체 길이

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `2`을 반환합니다.

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

제공 된 배열에서 람다 함수를 호출한 후 새 배열을 반환 합니다. 첫 번째 매개 변수는 람다 함수에 사용할 배열입니다. 두 번째 매개 변수는 람다 함수입니다.

다음 샘플에서는 모든 값이 배가 되는 새 배열을 반환 합니다. 결과는 `[2, 4, 6]`입니다.

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

다음 샘플에서는 새 배열을 반환 합니다 `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

컬렉션에서 지정된 개수의 요소를 건너뛴 다음 나머지 요소를 반환합니다.

### <a name="example-string-skip"></a>예: 문자열 skip

다음 예제는 `"app"`을 반환합니다.

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>예: 배열 건너뛰기

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `[3]`을 반환합니다.

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>예: object skip

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
다음 예제는 `{"key2": "Windows"}`을 반환합니다.

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>분할

구분 기호로 구분 된 입력의 부분 문자열을 포함 하는 문자열 배열을 반환 합니다.

다음 샘플에서는 배열을 반환 합니다 `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

문자열의 시작 부분부터 지정된 개수의 연속 문자, 배열의 시작 부분부터 지정된 개수의 연속 값 또는 개체의 시작 부분부터 지정된 개수의 연속 키와 값을 반환합니다.

### <a name="example-string-take"></a>예: 문자열 take

다음 예제는 `"web"`을 반환합니다.

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>예: array take

`element1`이 `[1, 2, 3]`을 반환한다고 가정합니다. 다음 예제는 `[1, 2]`을 반환합니다.

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>예: 개체 take

`element1`이 다음을 반환한다고 가정합니다.

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

다음 예제는 `{"key1": "Linux"}`을 반환합니다.

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.

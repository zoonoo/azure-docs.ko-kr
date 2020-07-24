---
title: UI 정의 수학 함수 만들기
description: 수학 연산을 수행할 때 사용할 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098169"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition 수학 함수

함수를 사용 하 여 수학 연산을 수행할 수 있습니다.

## <a name="add"></a>add

두 숫자를 더한 결과를 반환합니다.

다음 예제는 `3`을 반환합니다.

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

지정한 숫자보다 크거나 같은 가장 큰 정수를 반환합니다.

다음 예제는 `4`을 반환합니다.

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

첫 번째 숫자를 두 번째 숫자로 나눈 결과를 반환합니다. 결과는 항상 정수입니다.

다음 예제는 `2`을 반환합니다.

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

지정한 숫자보다 작거나 같은 가장 큰 정수를 반환합니다.

다음 예제는 `3`을 반환합니다.

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

두 숫자 중 큰 숫자를 반환합니다.

다음 예제는 `2`을 반환합니다.

```json
"[max(1, 2)]"
```

## <a name="min"></a>분

두 숫자 중 더 작은 숫자를 반환 합니다.

다음 예제는 `1`을 반환합니다.

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

첫 번째 숫자를 두 번째 숫자로 나눈 나머지를 반환합니다.

다음 예제는 `0`을 반환합니다.

```json
"[mod(6, 3)]"
```

다음 예제는 `2`을 반환합니다.

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

두 숫자를 곱한 결과를 반환합니다.

다음 예제는 `6`을 반환합니다.

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

지정된 범위 내의 임의 정수를 반환합니다. 이 함수는 암호화하여 보안 설정된 난수를 생성하지 않습니다.

다음 예제에서는 `42`를 반환할 수 있습니다.

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

지정된 범위 내의 정수 시퀀스를 생성합니다.

다음 예제는 `[1,2,3]`을 반환합니다.

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다.

다음 예제는 `1`을 반환합니다.

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.

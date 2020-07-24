---
title: UI 정의 논리 함수 만들기
description: 논리적 연산을 수행 하는 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098179"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition 논리 함수

이러한 함수는 조건식에 사용할 수 있습니다. 일부 함수는 모든 JSON 데이터 형식을 지원하지 않을 수 있습니다.

## <a name="and"></a>및

모든 매개 변수가 `true`로 평가되면 `true`를 반환합니다. 이 함수는 부울 형식의 매개 변수만 두 개 이상 지원 합니다.

다음 예제는 `true`을 반환합니다.

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

다음 예제는 `false`을 반환합니다.

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

null이 아닌 첫 번째 매개 변수의 값을 반환합니다. 이 함수는 모든 JSON 데이터 형식을 지원합니다.

`element1` 및 `element2`를 undefined로 가정합니다. 다음 예제는 `"Contoso"`을 반환합니다.

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
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

## <a name="equals"></a>equals

두 매개 변수의 형식과 값이 같으면 `true`를 반환합니다. 이 함수는 모든 JSON 데이터 형식을 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[equals(0, 0)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[equals('web', 'web')]"
```

다음 예제는 `false`을 반환합니다.

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 크면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `false`을 반환합니다.

```json
"[greater(1, 2)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

첫 번째 매개 변수가 두 번째 매개 변수보다 크거나 같으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

조건이 true인지 아니면 false인지에 따라 값을 반환합니다. 첫 번째 매개 변수는 테스트할 조건입니다. 두 번째 매개 변수는 조건이 true 인 경우 반환할 값입니다. 세 번째 매개 변수는 조건이 false 인 경우 반환할 값입니다.

다음 샘플에서는를 반환 합니다 `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 작으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[less(1, 2)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

첫 번째 매개 변수가 두 번째 매개 변수보다 작거나 같으면 `true`를 반환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

매개 변수가 `false`로 평가되면 `true`를 반환합니다. 이 함수는 부울 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[not(false)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>또는

매개 변수 중 하나 이상이 `true`로 평가되면 `true`를 반환합니다. 이 함수는 두 개 이상의 부울 형식의 매개 변수만 지원합니다.

다음 예제는 `true`을 반환합니다.

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

다음 예제는 `true`을 반환합니다.

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.

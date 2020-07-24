---
title: UI 정의 날짜 함수 만들기
description: 날짜 값으로 작업할 때 사용할 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098189"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition 날짜 함수

날짜 값으로 작업할 때 사용할 함수입니다.

## <a name="addhours"></a>addHours

지정된 타임스탬프에 시간(시)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:59:59.000Z"`을 반환합니다.

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

지정된 타임스탬프에 시간(분)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:00:59.000Z"`을 반환합니다.

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
지정된 타임스탬프에 시간(초)에 대한 정수를 더합니다.

다음 예제는 `"1991-01-01T00:00:00.000Z"`을 반환합니다.

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

로컬 컴퓨터의 현재 날짜 및 시간 문자열을 ISO 8601 형식으로 반환합니다.

다음 예제에서는 `"1990-12-31T23:59:59.000Z"`를 반환할 수 있습니다.

```json
"[utcNow()]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.

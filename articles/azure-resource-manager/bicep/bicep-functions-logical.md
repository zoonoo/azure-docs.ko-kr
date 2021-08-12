---
title: Bicep 함수 - 논리
description: 논리 값을 결정하기 위해 Bicep 파일에서 사용할 함수를 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: e2b0f79b2d8e0bf1f00e4db108b7a5f33a1be9a7
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027114"
---
# <a name="logical-functions-for-bicep"></a>Bicep에 대한 논리 함수

Resource Manager는 Bicep에 대한 `bool` 함수를 제공합니다. Azure Resource Manager JSON 논리 함수 중 일부는 [Bicep 논리 연산자](./operators-logical.md)로 바뀝니다.

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부울로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 부울입니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)에서는 문자열 또는 정수에 bool을 사용하는 방법을 보여줍니다.

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>다음 단계

* Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.

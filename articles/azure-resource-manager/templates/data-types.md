---
title: 템플릿의 데이터 형식
description: Azure Resource Manager 템플릿에서 사용할 수 있는 데이터 형식을 설명합니다.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538069"
---
# <a name="data-types-in-arm-templates"></a>ARM 템플릿의 데이터 형식

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 지원되는 데이터 형식을 설명합니다. JSON 및 Bicep 데이터 형식을 둘 다 설명합니다.

## <a name="supported-types"></a>지원되는 형식

ARM 템플릿 내에서 다음 데이터 형식을 사용할 수 있습니다.

* array
* 부울
* int
* 개체
* secureObject - Bicep에서 한정자로 표시됨
* secureString - Bicep에서 한정자로 표시됨
* 문자열

## <a name="arrays"></a>배열

배열은 왼쪽 대괄호(`[`)로 시작하고 오른쪽 대괄호(`]`)로 끝납니다.

JSON에서 배열은 한 줄 또는 여러 줄로 선언할 수 있습니다. 각 요소는 쉼표로 구분합니다.

Bicep에서는 배열은 여러 줄로 선언해야 합니다. 값 사이에 쉼표를 사용하지 마세요.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

배열의 요소는 동일한 형식이거나 다른 형식일 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>부울

부울 값을 지정하는 경우 `true` 또는 `false`를 사용합니다. 값을 따옴표로 묶지 마세요.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>정수

정수 값을 지정하는 경우 따옴표를 사용하지 마세요.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

인라인 매개 변수로 전달된 정수의 경우 값 범위는 배포에 사용하는 SDK 또는 명령줄 도구를 통해 제한될 수 있습니다. 예를 들어, PowerShell을 사용하여 템플릿을 배포하는 경우 정수 형식의 범위는 -2147483648~2147483647일 수 있습니다. 이 제한을 피하려면 [매개 변수 파일](parameter-files.md)에 큰 정수 값을 지정합니다. 리소스 종류는 정수 속성에 대해 고유한 한도를 적용합니다.

## <a name="objects"></a>개체

개체는 왼쪽 중괄호(`{`)로 시작하고 오른쪽 중괄호(`}`)로 끝납니다. 개체의 각 속성은 키와 값으로 구성됩니다. 키와 값은 콜론(`:`)으로 구분합니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에서 키는 큰따옴표로 묶습니다. 각 속성은 쉼표로 구분합니다.

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep에서는 키는 따옴표로 묶지 않습니다. 속성 사이에 쉼표를 사용하지 마세요.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

속성 접근자는 개체의 속성에 액세스하는 데 사용됩니다. 해당 접근자는 `.` 연산자를 사용하여 생성합니다. 예를 들면 다음과 같습니다.

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

이전 선언이 지정된 경우 식 x.y.z는 리터럴 문자열 ‘Hello’로 평가됩니다. 마찬가지로 식 x.q는 정수 리터럴 42로 평가됩니다.

속성 접근자는 모든 개체와 함께 사용할 수 있습니다. 여기에는 개체 형식 및 개체 리터럴의 매개 변수 및 변수가 포함됩니다. 개체가 아닌 형식의 식에서 속성 접근자를 사용하면 오류가 발생합니다.

---

## <a name="strings"></a>문자열

JSON에서 문자열은 큰따옴표를 사용하여 표시합니다. Bicep에서 문자열은 작은따옴표를 사용하여 표시합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>보안 문자열 및 개체

보안 문자열은 문자열과 동일한 형식을 사용하고 보안 개체는 개체와 동일한 형식을 사용합니다. 매개 변수를 보안 문자열 또는 보안 개체로 설정하면 매개 변수 값이 배포 기록에 저장되지 않고 로그되지 않습니다. 그러나 해당 보안 값을 보안 값이 필요하지 않은 속성으로 설정하면 값이 보호되지 않습니다. 예를 들어, 보안 문자열을 태그로 설정하면 해당 값이 일반 텍스트로 저장됩니다. 암호 및 비밀에 보안 문자열을 사용합니다.

Bicep에서는 `@secure()` 한정자를 문자열 또는 개체에 추가합니다.

다음 예제에서는 두 개의 보안 매개 변수를 보여줍니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>다음 단계

템플릿 구문에 관해 자세히 알아보려면 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
